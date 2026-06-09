Abaixo está a versão adaptada para **PowerShell no Windows**, mantendo apenas os comandos que possuem equivalente direto ou funcionalidade semelhante.

# 1. Explorando a interface de rede [Introdutório]

### Listar interfaces de rede

```powershell
Get-NetAdapter
```

### Ver os endereços IP

```powershell
Get-NetIPAddress
```

Ou de forma resumida:

```powershell
ipconfig
```

### Ver a tabela de rotas

```powershell
Get-NetRoute
```

Ou:

```powershell
route print
```

---

# 2. Testando conectividade com ping e traceroute [Introdutório]

### Ping básico

```powershell
ping google.com
```

Ou limitando a 4 pacotes:

```powershell
ping -n 4 google.com
```

### Traçar o caminho até o servidor

```powershell
tracert google.com
```

### Resolver um domínio para IP

```powershell
nslookup google.com
```

---

# 3. Fazendo requisições HTTP [Prático]

### Baixar o HTML de uma página

```powershell
curl https://example.com
```

ou

```powershell
Invoke-WebRequest https://example.com
```

### Ver apenas os cabeçalhos HTTP

```powershell
curl -I https://example.com
```

ou

```powershell
(Invoke-WebRequest https://example.com).Headers
```

### Consumir uma API pública

```powershell
curl https://api.github.com/users/torvalds
```

### Consumir uma API e formatar o JSON

```powershell
Invoke-RestMethod https://api.github.com/users/torvalds | ConvertTo-Json -Depth 10
```

O PowerShell já possui suporte nativo a JSON, dispensando o uso do `jq`.

---

# 4. Criando um servidor web com Python [Prático]

### Criar pasta e arquivo HTML

```powershell
mkdir "$HOME\meusite"

"<h1>Meu servidor Python!</h1>" | Out-File "$HOME\meusite\index.html"
```

### Entrar na pasta e iniciar o servidor

```powershell
cd "$HOME\meusite"

python -m http.server 8080
```

ou

```powershell
python3 -m http.server 8080
```

### Testar o servidor local

```powershell
curl http://localhost:8080
```

### Parar o servidor

```text
Ctrl + C
```

---

# 5. Sockets em Python: cliente e servidor TCP [Avançado]

### Criar o arquivo servidor.py

```powershell
@'
import socket

HOST = 'localhost'
PORT = 9090

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen()
    print(f"Servidor aguardando conexão em {HOST}:{PORT}...")
    conn, addr = s.accept()
    with conn:
        print(f"Conectado por {addr}")
        while True:
            data = conn.recv(1024)
            if not data:
                break
            print(f"Recebido: {data.decode()}")
            conn.sendall(b"Mensagem recebida!")
'@ | Out-File "$HOME\servidor.py"
```

### Criar o arquivo cliente.py

```powershell
@'
import socket

HOST = 'localhost'
PORT = 9090

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT))
    s.sendall(b"Ola, servidor!")
    resposta = s.recv(1024)
    print(f"Resposta do servidor: {resposta.decode()}")
'@ | Out-File "$HOME\cliente.py"
```

### Executar o laboratório

**Terminal 1**

```powershell
python "$HOME\servidor.py"
```

**Terminal 2**

```powershell
python "$HOME\cliente.py"
```

---

## Equivalência rápida Linux → PowerShell

| Linux           | PowerShell                    |
| --------------- | ----------------------------- |
| `ip addr show`  | `Get-NetIPAddress`            |
| `hostname -I`   | `ipconfig`                    |
| `ip route show` | `Get-NetRoute`                |
| `ping -c 4`     | `ping -n 4`                   |
| `traceroute`    | `tracert`                     |
| `nslookup`      | `nslookup`                    |
| `curl`          | `curl` ou `Invoke-WebRequest` |
| `mkdir`         | `mkdir`                       |
| `cd`            | `cd`                          |
| `python3`       | `python`                      |
| `Ctrl+C`        | `Ctrl+C`                      |

Essa adaptação funciona em **Windows 10, Windows 11 e Windows Server** utilizando PowerShell 5.1 ou PowerShell 7+.
