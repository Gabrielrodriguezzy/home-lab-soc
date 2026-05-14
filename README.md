# 🛡️ Home Lab SOC — Laboratório de Cibersegurança

![header](https://capsule-render.vercel.app/api?type=waving&color=042C53&height=160&section=header&text=Home%20Lab%20SOC&fontSize=40&fontColor=B5D4F4&fontAlignY=45&desc=Blue%20Team%20%7C%20Threat%20Detection%20%7C%20Incident%20Response&descAlignY=62&descColor=9FE1CB)

Laboratório de cibersegurança montado em ambiente doméstico para estudo prático de **Blue Team**, análise de ameaças e resposta a incidentes, simulando um ambiente SOC real.

---

## 🏗️ Arquitetura do Lab

```
PC Principal (i5 11ª Geração / 16GB RAM)
│
├── 🔵 VM Wazuh Server (Ubuntu Server)
│     └── SIEM + EDR + Dashboard de alertas
│
├── 🔴 VM Kali Linux
│     └── Máquina atacante / Red Team
│
└── 🎯 VM Metasploitable 2
      └── Alvo vulnerável para simulação de ataques
          
Rede: Internal Network isolada (10.0.0.0/24)
├── Wazuh Server:    10.0.0.3
├── Kali Linux:      10.0.0.1
└── Metasploitable:  10.0.0.2
```

---

## 🛠️ Stack de Ferramentas

**SIEM / Monitoramento**

![Wazuh](https://img.shields.io/badge/Wazuh-0D1B2A?style=flat-square&logo=wazuh&logoColor=9FE1CB)
![Ubuntu](https://img.shields.io/badge/Ubuntu_Server-0D1B2A?style=flat-square&logo=ubuntu&logoColor=378ADD)

**Red Team / Ofensiva**

![Kali](https://img.shields.io/badge/Kali_Linux-0D1B2A?style=flat-square&logo=kalilinux&logoColor=9FE1CB)
![Nmap](https://img.shields.io/badge/Nmap-0D1B2A?style=flat-square&logo=nmap&logoColor=378ADD)
![Metasploit](https://img.shields.io/badge/Metasploit-0D1B2A?style=flat-square&logo=metasploit&logoColor=B5D4F4)

**Virtualização**

![VirtualBox](https://img.shields.io/badge/VirtualBox-0D1B2A?style=flat-square&logo=virtualbox&logoColor=378ADD)

---

## 📋 Exercícios Realizados

### ✅ 01 — Reconhecimento com Nmap
**Objetivo:** Mapear serviços e portas abertas na Metasploitable 2  
**Técnica:** Port Scanning com detecção de versão (`-sV`)  
**Resultado:** 22 serviços vulneráveis identificados

```bash
nmap -sV 10.0.0.2
```

**Portas críticas encontradas:**

| Porta | Serviço | Versão | Vulnerabilidade |
|-------|---------|--------|-----------------|
| 21 | FTP | vsftpd 2.3.4 | Backdoor conhecida |
| 23 | Telnet | Linux telnetd | Sem criptografia |
| 1524 | Bindshell | Metasploitable | Shell root exposta |
| 3306 | MySQL | 5.0.51a | Banco de dados exposto |
| 5900 | VNC | Protocol 3.3 | Acesso remoto sem auth |

**Detecção no Wazuh:** ✅ 412 alertas gerados — scan detectado em tempo real

---

### ✅ 02 — Análise de Alertas no SIEM
**Objetivo:** Identificar e classificar eventos de segurança no Wazuh  
**Resultado:** Detecção de rootkit — arquivo `/usr/bin/md5sum` trojanizado

**Alerta analisado:**
```
Regra ID:    510
Nível:       7 (Médio)
Título:      Versão trojanizada do arquivo detectada
Arquivo:     /usr/bin/md5sum
Descrição:   Evento de detecção de anomalias baseado em host (rootcheck)
Conformidade: PCI DSS 10.6.1 | GDPR IV_35.7.d
```

**Análise:** O `md5sum` é uma ferramenta nativa do Linux substituída por uma versão maliciosa — técnica comum de rootkit para ocultar presença do atacante e manter persistência no sistema.

---

## 🔧 Setup — Passo a Passo

### Pré-requisitos
- VirtualBox instalado
- Mínimo 16GB RAM
- 100GB de espaço em disco

### 1. Wazuh Server (Ubuntu Server)
```bash
# Instalação automatizada
curl -O https://packages.wazuh.com/4.12/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```
Acessar painel: `https://localhost` (usuário: admin)

### 2. Wazuh Agent (Windows Host)
Gerado pelo painel em **Agents → Deploy new agent → Windows**

### 3. Metasploitable 2
Download em: [sourceforge.net/projects/metasploitable](https://sourceforge.net/projects/metasploitable/)  
Importar o arquivo `.vmdk` no VirtualBox.

### 4. Rede Interna
Configurar todas as VMs com **Rede Interna** (`labrede`) e IPs estáticos:
```bash
# Wazuh
sudo ip addr add 10.0.0.3/24 dev enp0s3

# Kali
sudo ip addr add 10.0.0.1/24 dev eth0

# Metasploitable
sudo ifconfig eth0 10.0.0.2 netmask 255.255.255.0 up
```

---

## 📚 Roadmap de Estudos

- [x] Configuração do ambiente virtualizado
- [x] Instalação e configuração do Wazuh SIEM
- [x] Integração do agente Windows
- [x] Primeiro reconhecimento com Nmap
- [x] Análise de alertas e detecção de rootkit
- [ ] Exploração de vulnerabilidades com Metasploit
- [ ] Análise de tráfego com Wireshark
- [ ] Detecção de brute force
- [ ] Simulação de movimento lateral
- [ ] Criação de regras customizadas no Wazuh
- [ ] Estudo do framework MITRE ATT&CK

---

## 📖 Referências

- [Documentação oficial Wazuh](https://documentation.wazuh.com)
- [VulnHub — Metasploitable](https://www.vulnhub.com)
- [MITRE ATT&CK Framework](https://attack.mitre.org)
- [TryHackMe — SOC Level 1](https://tryhackme.com/path/outline/soclevel1)

---

![footer](https://capsule-render.vercel.app/api?type=waving&color=042C53&height=100&section=footer)
