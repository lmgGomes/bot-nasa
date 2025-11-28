##**<ins>🛰️ Bot NASA (Telegram)</ins>**

Um bot simples e divertido que utiliza a API da NASA (APOD - Astronomy Picture of the Day) para enviar a seus usuários a incrível foto astronômica do dia, juntamente com sua descrição.

**<ins>✨ Funcionalidades</ins>**

Foto do Dia: Envia automaticamente a imagem ou vídeo astronômico do dia (APOD) fornecido pela NASA.

Descrição Detalhada: Inclui o título e uma breve explicação sobre o que a imagem/vídeo representa.

Loop de Execução: Mantém um ciclo de polling ativo para verificar e responder a novos comandos (se for um bot de mensagens, como Telegram).

**<ins>🛠️ Tecnologias Utilizadas</ins>**

<sub>Linguagem: Python</sub>

APIs:

NASA APOD API: Para buscar o conteúdo astronômico diário.

API do Telegram / Outro Bot SDK: Para interface e envio das mensagens (se aplicável).

Gerenciamento de Variáveis: python-dotenv (para carregar variáveis de ambiente)

Requisições HTTP: requests

**<ins>🚀 Como Configurar e Executar</ins>**

Siga os passos abaixo para configurar e rodar o bot em seu ambiente.

1. Clonar o Repositório
```python
Bash
git clone https://github.com/ImgGomes/bot-nasa.git
cd bot-nasa
```
3. Instalar Dependências
Certifique-se de ter o Python instalado. Instale as bibliotecas necessárias:
```python
Bash

pip install -r requirements.txt
# Se não houver requirements.txt:
# pip install python-dotenv requests
3. Configurar Variáveis de Ambiente
```
O bot requer suas chaves de API para funcionar. Crie um arquivo chamado .env na raiz do projeto e adicione suas variáveis:

Bot iniciado. Ctrl+c para parar.
