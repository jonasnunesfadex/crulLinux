
---

> 💡 **Dica Inicial:** Acesse **cocalc.com** → crie uma conta gratuita → clique em **New Project** → abra um **Terminal** (menu *Files* > *New* > *Terminal*). Você terá um Linux Ubuntu completo diretamente no seu navegador para praticar os comandos abaixo.

---

## 1. Explorando a interface de rede `[Introdutório]`

Primeiro, vamos ver quais interfaces de rede existem na máquina — é como checar as "portas" disponíveis no sistema.

### Listar interfaces de rede

```bash
ip addr show

```

Você verá algo como `lo` (*loopback* = o computador falando consigo mesmo) e `eth0` (interface de rede real). Cada uma delas tem um endereço IP associado.

### Ver os IPs de forma resumida

```bash
hostname -I

```

### Ver a tabela de rotas (como os pacotes chegam à internet)

```bash
ip route show

```

> 📌 **Nota:** O endereço IP que aparece é o do servidor do CoCalc. Você está mexendo em um Linux real hospedado na nuvem!

---

## 2. Testando conectividade com ping e traceroute `[Introdutório]`

O `ping` envia um "oi" para um servidor e mede quanto tempo ele demora para responder. Já o `traceroute` mostra o caminho exato que o pacote percorre pela internet.

### Ping básico (pressione Ctrl+C para parar)

```bash
ping -c 4 google.com

```

O parâmetro `-c 4` limita o teste a apenas 4 pacotes. Observe o tempo em **ms** (milissegundos) — quanto menor ele for, mais rápida é a resposta.

### Traçar o caminho até o servidor

```bash
traceroute google.com

```

Cada linha do resultado representa um "salto" (*hop*) — ou seja, um roteador pelo qual o seu pacote passou. Os caracteres `* * *` indicam roteadores que bloqueiam esse tipo de rastreamento por segurança.

### Verificar se um domínio resolve para um IP

```bash
nslookup google.com

```

---

## 3. Fazendo requisições HTTP com curl `[Prático]`

O `curl` simula exatamente o que um navegador de internet faz: ele pede uma página a um servidor e recebe a resposta estruturada. É fundamental para entender como a web funciona nos bastidores.

### Buscar o HTML de uma página

```bash
curl https://example.com

```

### Ver apenas os cabeçalhos HTTP (metadados da resposta)

```bash
curl -I https://example.com

```

Aqui você verá o **status HTTP** (ex: `200 OK`, `301 Moved Permanently`, `404 Not Found`), o servidor web utilizado, a data e o tipo do conteúdo.

### Consumir uma API pública e ver o retorno em JSON

```bash
curl https://api.github.com/users/torvalds

```

### Consumir a mesma API, mas com o JSON formatado

```bash
sudo apt-get install -y jq && curl https://api.github.com/users/torvalds | jq .

```

> 💡 **Dica:** Com a ferramenta `jq` instalada, o JSON fica colorido e indentado, tornando a leitura muito mais humana e amigável!

---

## 4. Criando um servidor web com Python `[Prático]`

Agora você vai criar um servidor HTTP do zero usando Python puro — sem precisar instalar nenhuma biblioteca externa.

### Criar uma pasta e um arquivo HTML básico

```bash
mkdir ~/meusite && echo "<h1>Meu servidor Python!</h1>" > ~/meusite/index.html

```

### Entrar na pasta e subir o servidor na porta 8080

```bash
cd ~/meusite && python3 -m http.server 8080

```

O terminal vai "travar" — isso é perfeitamente normal! Significa que o servidor está ativo e escutando. **Abra uma segunda aba de terminal no CoCalc** para continuar executando os comandos abaixo.

### Na segunda aba, teste o seu próprio servidor com o curl

```bash
curl http://localhost:8080

```

Você receberá o HTML de volta! Toda vez que alguém acessa o seu servidor, o log de acesso aparece detalhado lá na primeira aba.

> 🛑 **Para parar o servidor:** Volte à primeira aba de terminal e pressione **Ctrl + C**.

---

## 5. Sockets em Python: cliente e servidor TCP `[Avançado]`

Aqui vamos entender o nível mais baixo da rede: como dois programas se comunicam diretamente usando sockets TCP — a base de praticamente toda a comunicação na internet.

### Criar o arquivo do servidor (`servidor.py`)

Copie e cole o bloco abaixo no terminal para criar o script:

```bash
cat > ~/servidor.py << 'EOF'
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
EOF

```

### Criar o arquivo do cliente (`cliente.py`)

Copie e cole este bloco para gerar o script do cliente:

```bash
cat > ~/cliente.py << 'EOF'
import socket

HOST = 'localhost'
PORT = 9090

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT))
    s.sendall(b"Ola, servidor!")
    resposta = s.recv(1024)
    print(f"Resposta do servidor: {resposta.decode()}")
EOF

```

### Execução do laboratório

Abra dois terminais simultâneos no CoCalc e execute um comando em cada um:

* **No Terminal 1:** Inicie o servidor
```bash
python3 ~/servidor.py

```


* **No Terminal 2:** Execute o cliente para disparar a mensagem
```bash
python3 ~/cliente.py

```



Você verá a mensagem chegar instantaneamente no servidor e a confirmação voltar para o cliente. É exatamente isso que acontece quando você acessa qualquer site no seu dia a dia, só que envelopado em centenas de outras camadas!
