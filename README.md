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
 [Ver documentação](https://github.com/MayconPCampos/soc-lab/blob/main/ataques/scan%20de%20portas/Scan%20de%20portas.md)

---

### Teste 2 – Service Enumeration
Enumeração de serviços SSH e coleta de informações criptográficas  
 [Ver documentação](https://github.com/MayconPCampos/soc-lab/blob/main/ataques/scan%20de%20servi%C3%A7os%20e%20enumera%C3%A7%C3%A3o/Scan%20de%20servi%C3%A7os%20e%20enumera%C3%A7%C3%A3o.md)

---

###  Teste 3 – Brute Force SSH
Simulação de ataque de força bruta com Hydra e análise de logs  
 [Ver documentação](https://github.com/MayconPCampos/soc-lab/blob/main/ataques/brute%20force%20(SSH)/Brute%20force%20(SSH).md)

---

### Teste 4 – Pós-exploração
Alteração de arquivos críticos e detecção com Wazuh (FIM)  
 [Ver documentação](https://github.com/MayconPCampos/soc-lab/blob/main/ataques/altera%C3%A7%C3%A3o%20de%20arquivos%20Cr%C3%ADticos%20(P%C3%B3s%20acesso)/Altera%C3%A7%C3%A3o%20de%20arquivos%20Cr%C3%ADticos%20(P%C3%B3s%20acesso).md)

---

### Teste 5 – Persistência
Criação de backdoor com cron para execução contínua  
 [Ver documentação](https://github.com/MayconPCampos/soc-lab/blob/main/ataques/persist%C3%AAncia/Persist%C3%AAncia.md)

---

## Objetivos do laboratório

- Praticar detecção de ameaças
- Desenvolver análise de logs
- Simular cenários reais de ataque
- Aplicar conceitos de SOC
- Documentar incidentes de segurança
