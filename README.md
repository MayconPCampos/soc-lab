# SOC Lab – Detecção e Resposta a Incidentes

##  Sobre o projeto
Este repositório apresenta um laboratório prático de Segurança da Informação com foco em operações de SOC (Security Operations Center).

O objetivo é simular ataques reais em um ambiente controlado, analisando sua detecção através de ferramentas como SIEM (Wazuh) e IDS (Suricata), além da investigação e documentação dos incidentes.

---

## Tecnologias utilizadas
- Wazuh (SIEM)
- Suricata (IDS)
- pfSense (Firewall)
- Kali Linux (ataques)
- Ubuntu Server (alvo)
- Virtualização

---

## Topologia do laboratório

![Topologia](Arquitetura/Topologia do Laboratório.png)

---

##  Cenários simulados

###  Teste 1 – Port Scan
Simulação de varredura de portas com Nmap e detecção via Suricata  
 [Ver documentação](attacks/port-scan/README.md)

---

### Teste 2 – Service Enumeration
Enumeração de serviços SSH e coleta de informações criptográficas  
 [Ver documentação](attacks/service-scan/README.md)

---

###  Teste 3 – Brute Force SSH
Simulação de ataque de força bruta com Hydra e análise de logs  
 [Ver documentação](attacks/brute-force/README.md)

---

### Teste 4 – Pós-exploração
Alteração de arquivos críticos e detecção com Wazuh (FIM)  
 [Ver documentação](attacks/post-exploitation/README.md)

---

### Teste 5 – Persistência
Criação de backdoor com cron para execução contínua  
 [Ver documentação](attacks/persistence/README.md)

---

## Objetivos do laboratório

- Praticar detecção de ameaças
- Desenvolver análise de logs
- Simular cenários reais de ataque
- Aplicar conceitos de SOC
- Documentar incidentes de segurança
