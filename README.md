
#.env
export TELEGRAM_TOKEN=8215475928:AAHwzDJUZ_2ChDqBseABumMTRbeicTsG99k
NASA_API_KEY=jJ1ftG2ZmZztjF8VLgpDc0tEdoBnoEJZQrUwesH5

import os
from dotenv import load_dotenv
import requests
import time

load_dotenv() 

TELEGRAM_TOKEN = os.getenv("TELEGRAM_TOKEN")
NASA_API_KEY = os.getenv("NASA_API_KEY") 
API_URL = f"https://api.telegram.org/bot{TELEGRAM_TOKEN}" 
LAST_UPDATE_ID = 0 

if not TELEGRAM_TOKEN or not NASA_API_KEY:
    print("X ERRO: Chaves de API ausentes.") #PRINT USADO PORQUE ME EXTRESSEI MUITO COM A API
    exit()

def main_loop(): #PONTO CENTRAL DA EXECUÇÃO DO PROGRAMA
    global LAST_UPDATE_ID

    print(" Bot iniciado. Ctrl+C para parar.")

    while True:
        try: #TRY TA INICIANDO UMA "TENTATIVA" PRA NÃO QUEBRAR O PROGRAMA DIRETO
            updates_url = f"{API_URL}/getUpdates?offset={LAST_UPDATE_ID + 1}" #AQUI É PRA SABER SE TEM ALGUMA MENSAGEM NOVA 
            response = requests.get(updates_url)  
            response.raise_for_status() #VEREFICA SE A RESPOSTA FOI BEM SUCEDIDA
            
            updates = response.json().get('result', [])
            
            if not updates:
                time.sleep(0.5) #AQUI É O TEMPO DE RESPOSTA DO BOT, ANTES TAVA 3.0 MAS TAVA DEMORANDO MUITO
                continue

            for update in updates:
                LAST_UPDATE_ID = update['update_id'] #ESSA PARTE É EXTREMAMENTE IMPORTANTE PORQUE ME ESTRESSOU MUITO, O LAST UPDATE FAZ ELE NÃO LER AS MENSAGENS ANTIGAS E RODALAS DNV
                
                message = update.get('message') #PEGA OS DADOS DA MSG
                if not message:
                    continue
                    
                chat_id = message['chat']['id']
                text = message.get('text', '') #SE A MENSAGEM NÃO TIVER TEXTO ELE ENVIA UMA STRING VAZIA PRA EVITAR ERROS
                
                if text.lower().startswith('/nasa'): #VERIFICA SE O COMANDO (EM MINUSCULO) COMEÇA COM /NASA
                    
                    partes = text.split()
                    data_escolhida = partes[1] if len(partes) > 1 else None #ELE VERIFICA SE ALISTA TEM MAIS DE UM ITEM, SEJA A DATA OU SÓ O /NASA

                    apod_url = "https://api.nasa.gov/planetary/apod"
                    params = {'api_key': NASA_API_KEY}
                    if data_escolhida:
                        params['date'] = data_escolhida
                        
                    nasa_response = requests.get(apod_url, params=params)
                    nasa_response.raise_for_status()  
                    dados = nasa_response.json()
                    
                    photo_url = dados.get('url')  #EXTRAI A URL DA IMAGEM
                    title = dados.get('title') #EXTRAI O TITULO DA IMAGEM, ISSO NÃO É NECESSARIO MAS EU GOSTEI DE COMO FICOU NO FINAL
                    explanation = dados.get('explanation') #AQUI MANDA AQUELE TEXTO EM BAIXO, QUE INCLUSIVE PRECISO ARRUMAR UM JEITO DELE VIM TRADUZIDO PRAS PESSOAS ENTENDEREM 

                    is_image = photo_url and any(ext in photo_url.lower() for ext in ['.jpg', '.jpeg', '.png']) 
                    #A LINHA A CIMA É EXTREMAMENTE IMPORTANTE PORQUE TA FUNCIONANDO COMOM UMA CHECAGEM DE SEGURANÇA

                    if is_image:
                        caption = f"**{title}**\n\n{explanation}"
                        send_url = f"{API_URL}/sendPhoto"
                        payload = {'chat_id': chat_id, 'photo': photo_url, 'caption': caption, 'parse_mode': 'Markdown'}
                    else:
                        mensagem = f"O conteúdo da NASA ({title}) não é uma foto estática. Tente o link: {photo_url}"
                        send_url = f"{API_URL}/sendMessage"
                        payload = {'chat_id': chat_id, 'text': mensagem}
                    
                    requests.post(send_url, data=payload).raise_for_status()

                elif text.lower().startswith('/start') or text.lower().startswith('/help'):
                    help_msg = "🤖 Olá! Para a foto da NASA:\n1. Hoje: `/nasa`\n2. Data Específica: `/nasa YYYY-MM-DD`"
                    send_url = f"{API_URL}/sendMessage"
                    requests.post(send_url, data={'chat_id': chat_id, 'text': help_msg}).raise_for_status()

        except Exception:
            time.sleep(5) 

if __name__ == '__main__':
    try:
        main_loop()
    except KeyboardInterrupt:
        print("\nBot encerrado pelo usuário (Ctrl+C).")
    except Exception:
        pass
    
