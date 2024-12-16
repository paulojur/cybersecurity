**Documentação da Investigação HTTP no Servidor VM1**

### **Objetivo**
Realizar uma análise inicial de serviços abertos no servidor (VM1) e explorar seus diretórios e arquivos expostos via HTTP, usando ferramentas locais e baseadas em protocolo HTTP, com foco no aprendizado.

---

### **Passo 1: Escaneamento inicial**

**Ferramenta utilizada:** Nmap

- Foi identificado que a porta 80 (HTTP) estava aberta.
- Serviço em execução: **SimpleHTTPServer 0.6 (Python 3.11.9)**
- Outras portas: Nenhuma aberta.

**Comando utilizado:**
```bash
nmap -sV 192.168.1.2
```

Resultado relevante:
```plaintext
80/tcp open  http SimpleHTTPServer 0.6 (Python 3.11.9)
```

---

### **Passo 2: Exploração via Browser e CMD**

1. **Via navegador:** Acessar `http://192.168.1.2` listou os conteúdos presentes no diretório raiz do servidor HTTP.
2. **Via linha de comando:** O comando `curl` foi utilizado para visualizar os códigos HTML expostos no servidor HTTP.

**Comando curl:**
```bash
curl http://192.168.1.2
```

3. Foi encontrada uma pasta chamada **.genupg** contendo o arquivo `private-keys-v1.d`:
   - O arquivo estava vazio ao ser analisado tanto no browser quanto no terminal.

**Observação:** Apesar do nome do arquivo sugerir conteúdo sensível, não houve informação útil neste caso.

---

### **Passo 3: Varredura com Dirb**

**Ferramenta utilizada:** Dirb

- Foi usada para identificar outros diretórios e arquivos expostos no servidor HTTP.
- Foram encontrados 7 itens relevantes, incluindo as pastas **cache** e **config**.

**Comando utilizado:**
```bash
dirb http://192.168.1.2
```

**Resultado parcial:**
```plaintext
+ http://192.168.1.2/.cache
+ http://192.168.1.2/.config
```

1. Dentro de `.cache`, uma pasta chamada **sessions** foi encontrada.
2. Dentro de `.config`, arquivos como `qt5ct.conf` e `qterminal.ini` foram identificados.

---

### **Passo 4: Exploração de Subdiretórios**

**Análise da pasta `.cache/sessions`:**
- Foram encontrados dois subdiretórios:
  1. `thumbs-kali:0/`: Continha um arquivo PNG, aparentemente um printscreen de uma sessão anterior.
  2. `xfce4-session-kali:0`: Continha um arquivo de log com mais de 60 linhas, incluindo informações relevantes:

**Trecho do log:**
```plaintext
[Session: Default]
Client0_ClientId=2917c8c68-77e3-4ead-afc1-fdb69a1a3752
Client0_Hostname=local/kali
Client0_CurrentDirectory=/home/kali
```

**Análise da pasta `.config`:**
- Arquivos como **`qt5ct.conf`**, **`qterminal.ini`** e **`scripts-accels`** foram listados, mas nenhum apresentou vulnerabilidades evidentes.

---

### **Passo 5: Comando "ls" na VM1**

Ao acessar a VM1 diretamente (via terminal), foi usado o comando `ls` para explorar os diretórios locais do sistema.

**Comando utilizado:**
```bash
ls -1 /.config
```

**Resultado:**
- Diretório principal: `/home/user`.
- Constatado que alguns diretórios listados pelo dirb estavam na árvore de arquivos local do sistema.

---

### **Considerações Finais**

- **Dificuldades enfrentadas:**
  - Falta de algumas ferramentas pré-instaladas no Kali (como o `gobuster`).
  - Necessidade de interpretação manual de logs e arquivos desconhecidos.

- **Próximos passos sugeridos:**
  - Focar em aprender sobre a interpretação de logs e arquivos de configuração para identificar possíveis vulnerabilidades.
  - Investigar mais profundamente arquivos como `xfce4-session-kali:0`, que pode conter informações sensíveis relacionadas à sessão do sistema.

---

**Conclusão:**
As explorações feitas até o momento mostraram que o servidor HTTP (VM1) expõe alguns diretórios e arquivos sensíveis, mas sem indícios claros de vulnerabilidades que possam ser exploradas diretamente. A análise será continuada com foco em investigações mais aprofundadas e práticas seguras.

