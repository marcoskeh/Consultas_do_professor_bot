import telebot
import requests
pyTelegramBotAPI
requests
# services:
  - type: web
    name: consultas_do_professor_bot
    env: python
    buildCommand: ""
    startCommand: "python bot.py"Token do seu Bot
TOKEN = '8179142206:AAGZiF1yjWZX8-U7vJ_gXqYky3MZwyPe_g4'

# Cria o bot
bot = telebot.TeleBot(TOKEN)

@bot.message_handler(commands=['start'])
def start(message):
    bot.reply_to(message, "Bem-vindo ao *Consultas_do_professor_bot*!\n\nComandos disponíveis:\n/cpf [número]\n/nome [nome completo]\n/placa [placa do veículo]", parse_mode='Markdown')

@bot.message_handler(commands=['cpf'])
def consultar_cpf(message):
    try:
        cpf = message.text.split(' ')[1]
        response = requests.get(f'https://api-publica.sandbox.bar/proxy/cpf/{cpf}')
        if response.status_code == 200:
            dados = response.json()
            resposta = f"Nome: {dados.get('nome', 'Não encontrado')}\nNascimento: {dados.get('nascimento', 'Não encontrado')}"
            bot.reply_to(message, resposta)
        else:
            bot.reply_to(message, "CPF não encontrado ou API fora do ar.")
    except Exception as e:
        bot.reply_to(message, "Erro ao consultar CPF. Use assim: /cpf 00000000000")

@bot.message_handler(commands=['nome'])
def consultar_nome(message):
    try:
        nome = message.text.replace('/nome', '').strip()
        if nome == "":
            bot.reply_to(message, "Use assim: /nome João da Silva")
            return
        bot.reply_to(message, f"🔍 Buscando nome: {nome}\n\n(Consulta de nome é limitada em APIs públicas.)")
    except Exception as e:
        bot.reply_to(message, "Erro ao consultar nome.")

@bot.message_handler(commands=['placa'])
def consultar_placa(message):
    try:
        placa = message.text.split(' ')[1].upper()
        response = requests.get(f'https://apicarros.com/v1/consulta/{placa}/json')
        if response.status_code == 200:
            dados = response.json()
            resposta = f"Modelo: {dados.get('modelo', 'N/A')}\nMarca: {dados.get('marca', 'N/A')}\nCor: {dados.get('cor', 'N/A')}\nAno: {dados.get('ano', 'N/A')}"
            bot.reply_to(message, resposta)
        else:
            bot.reply_to(message, "Placa não encontrada ou API indisponível.")
    except Exception as e:
        bot.reply_to(message, "Erro ao consultar placa. Use assim: /placa ABC1D23")

print("Bot rodando...")
bot.infinity_polling()
