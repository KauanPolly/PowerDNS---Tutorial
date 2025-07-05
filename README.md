

# PowerDNS --- Tutorial

Este repositório contém uma configuração completa e funcional do PowerDNS com os modos Authoritative e Recursor separados, sem banco de dados, utilizando arquivos de zona BIND e IPs de documentação. Ideal para testes, aprendizado e uso como template público.

🌐 Visão Geral:
- PowerDNS Authoritative + Recursor separados
- Com banco de dados PostgreSQL (PowerDNS gsql backend)
- Suporte a IPv4 e IPv6
- DNSSEC opcional com `pdnsutil`
- Firewall com UFW (exemplo incluso)
- IPs de produção NÃO incluídos (somente IPs reservados)

🔧 Instalação dos Pacotes:
sudo apt update
sudo apt install pdns-server pdns-backend-pgsql pdns-recursor bind9utils

📁 Estrutura de Arquivos Esperada:
/etc/powerdns/
├── pdns.conf               # Configuração do Authoritative
├── recursor.conf           # Configuração do Recursor
(Zonas gerenciadas via banco de dados PostgreSQL, não há arquivos .zone ou named.conf)

⚙️ Arquivo: pdns.conf (Authoritative)
launch=bind
launch=gpgsql
gpgsql-host=127.0.0.1
gpgsql-user=SEU_USUARIO
gpgsql-password=SUA_SENHA
gpgsql-dbname=powerdns
local-address=127.0.0.1
local-ipv6=::1
local-port=5300
disable-syslog=no
loglevel=4
log-dns-queries=yes

📄 Arquivo: named.conf
⚠️ Não utilizado neste projeto — zonas são gerenciadas via PostgreSQL.

📄 Arquivo: example.zone
(⚠️ não utilizado neste setup)

Este projeto utiliza o PowerDNS Authoritative com backend **PostgreSQL**.
A zona DNS é gerenciada dinamicamente via banco de dados, e não com arquivos `.zone`.

A zona `example.com` e seus registros (A, AAAA, NS, etc.) devem ser inseridos na tabela `records`
conforme estrutura padrão do schema do PowerDNS.


⚙️ Arquivo: recursor.conf
local-address=0.0.0.0,::
local-port=53
forward-zones=.=127.0.0.1:5300
allow-from=192.0.2.0/24,2001:db8::/32,127.0.0.1,::1

🔐 Ativar DNSSEC (opcional):
sudo pdnsutil secure-zone example.com

🔥 Regras de Firewall com UFW:
sudo ufw allow from 192.0.2.0/24 to any port 53 proto udp
sudo ufw allow from 192.0.2.0/24 to any port 53 proto tcp
sudo ufw allow from 2001:db8::/32 to any port 53 proto udp
sudo ufw allow from 2001:db8::/32 to any port 53 proto tcp

✅ Testes de Funcionamento:
sudo systemctl restart pdns
sudo systemctl restart pdns-recursor

dig @192.0.2.1 www.example.com
dig +dnssec @192.0.2.1 example.com

journalctl -u pdns
journalctl -u pdns-recursor

🧪 Ambiente Recomendado:
- Ubuntu Server 22.04 ou superior
- PowerDNS Authoritative e Recursor versão 4.x
- IPs de exemplo: 192.0.2.1 e 2001:db8::1

📄 Licença:
Este projeto está licenciado sob a Licença MIT. Livre para uso, modificação e distribuição.
```
