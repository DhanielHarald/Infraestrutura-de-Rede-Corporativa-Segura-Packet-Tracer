# Projeto de Infraestrutura de Rede Corporativa Segura (Cisco Packet Tracer)

Este projeto é uma simulação completa de uma infraestrutura de rede corporativa de pequeno a médio porte, construída inteiramente no Cisco Packet Tracer. O objetivo principal foi projetar e implementar uma rede segura, segmentada, escalável e realista, aplicando as melhores práticas de segurança em múltiplas camadas (Defense in Depth).

O design evoluiu de uma simples rede plana para uma arquitetura complexa de "roteador duplo", separando o roteamento interno do firewall de perímetro.

## Topologia Lógica Final

A topologia lógica demonstra a arquitetura de rede, incluindo o plano de IP, as VLANs, as zonas de segurança (Interna, DMZ, Convidados) e o fluxo de tráfego para a Internet.

<img width="1681" height="733" alt="topologia_logica" src="https://github.com/user-attachments/assets/afb0ebab-b852-4a56-8d47-fb0f1ba419df" />


## Topologia Física

A topologia física demonstra o layout do escritório, o cabeamento estruturado e a organização dos equipamentos no data center, mostrando a separação física dos departamentos e a segurança dos ativos de rede.

<img width="1530" height="746" alt="topologia_fisica" src="https://github.com/user-attachments/assets/85c01ca5-a8dd-4176-9cb5-e91e0aaab67d" />


---

## Tecnologias e Habilidades Implementadas

Este projeto demonstra proficiência nas seguintes áreas:

* **Roteamento e Switching (Camada 2/3):** Configuração de roteadores e switches Cisco.
* **Segmentação de Rede (VLANs):** Criação de Redes Locais Virtuais para isolar o tráfego de departamentos (Vendas, TI, Convidados) e serviços (DMZ).
* **Roteamento Inter-VLAN:** Configuração de "Router-on-a-Stick" no roteador interno para permitir a comunicação controlada entre VLANs.
* **Firewall & ACLs:** Implementação de Listas de Controle de Acesso (ACLs) para criar regras de firewall granulares (ex: bloquear Convidados de acessar a rede interna).
* **Micro-segmentação:** Criação de uma VLAN de alta segurança (`IT-Admin`) separada da VLAN de suporte (`IT-Support`) para proteger dispositivos de gerenciamento.
* **Design de Perímetro (Arquitetura de Roteador Duplo):** Separação de funções entre um roteador interno (para VLANs) e um roteador de perímetro (para NAT e acesso à Internet).
* **NAT (Network Address Translation):** Configuração de PAT (Overload) no roteador de perímetro para traduzir todos os IPs internos em um único IP público.
* **Serviços de Rede Essenciais:**
    * **DHCP:** Configuração do roteador interno para atuar como servidor DHCP para múltiplas VLANs.
    * **DNS:** Configuração de um servidor DNS interno na DMZ para resolver nomes de domínio da intranet.
* **Segurança de Porta (Switch):** Implementação de `Port Security` com `mac-address sticky` para prevenir que dispositivos não autorizados se conectem fisicamente às portas do switch.
* **Acesso Wireless (Wi-Fi):** Instalação de um Access Point (AP) e mapeamento do SSID de convidados (`ELDE-Guest`) diretamente para a VLAN de Convidados.
* **Gerenciamento Seguro (SSH):** Desativação do Telnet e configuração do SSH (Secure Shell) em todos os dispositivos de rede para gerenciamento remoto criptografado.
* **Monitoramento de Rede (Syslog):** Configuração de um servidor Syslog para receber e centralizar logs do roteador.
* **Cabeamento Estruturado:** Design de uma topologia física realista com um armário de fiação central (Main Wiring Closet) e distribuição de cabos para as salas.

---

## Arquitetura da Rede e Plano de IP

A rede foi projetada com uma arquitetura de "roteador duplo" para separação de funções e segurança.

* **`Roteador-ELDE` (Interno):** Gerencia todo o roteamento Inter-VLAN e aplica as ACLs *internas* (ex: bloqueando a `VLAN 99` de acessar a `VLAN 10`).
* **`Firewall-Router` (Externo):** Atua como o firewall de perímetro. Gerencia o **NAT** e é o único dispositivo que fala diretamente com a Internet.

| ID da VLAN | Nome | Rede IP | Propósito |
| :--- | :--- | :--- | :--- |
| **10** | Sales (Vendas) | `192.168.10.0 /24` | Rede corporativa padrão para funcionários de vendas. |
| **20** | IT-Support (Suporte) | `192.168.20.0 /24` | Rede para a equipe de suporte técnico de TI. |
| **25** | IT-Admin (Admin) | `192.168.25.0 /24` | Rede de alta segurança (micro-segmentada) para o Administrador e dispositivos de gerenciamento (Syslog). |
| **99** | Guests (Convidados) | `192.168.99.0 /24` | Rede isolada para visitantes (Wi-Fi e portas físicas), com acesso apenas à Internet. |
| **100** | DMZ (DNS Server) | `192.168.100.0 /24` | Zona Desmilitarizada para hospedar serviços internos (Servidor DNS/Intranet). |
| **N/A** | Transit | `192.168.254.0 /24`| Rede de trânsito segura que conecta o `Roteador-ELDE` ao `Firewall-Router`. |
| **N/A** | Internet (WAN) | `200.200.200.0 /24`| Simula a rede pública do provedor de Internet (ISP). |

---

## Política de Segurança Implementada

A segurança foi o foco principal deste projeto:

1.  **Isolamento de Convidados:** A rede de Convidados (VLAN 99) é **totalmente bloqueada** de acessar qualquer rede IP interna (`192.168.x.x`), mas tem permissão para acessar a Internet (através do NAT).
2.  **Proteção da DMZ:** O servidor DNS/Intranet na DMZ (VLAN 100) é acessível pelas redes internas, mas está isolado em sua própria VLAN para conter qualquer possível violação.
3.  **Micro-segmentação de TI:** Os técnicos de Suporte (VLAN 20) não podem acessar dispositivos na rede de Admin (VLAN 25), aplicando o "Princípio do Menor Privilégio".
4.  **Segurança de Acesso Físico:** As portas do switch nos escritórios (`Sales`, `IT`) estão travadas pelo `Port Security` para o MAC address do dispositivo autorizado.
5.  **Gerenciamento Criptografado:** Todo o gerenciamento de dispositivos de rede (roteadores, switches) DEVE ser feito via **SSH**.

## Como Testar

Após baixar o arquivo `.pkt` e abri-lo no Cisco Packet Tracer:

1.  **Teste de Acesso à Internet (Sucesso):**
    * Vá em qualquer PC da rede `Sales` ou `IT` (ex: `PC-Sales01`).
    * Abra o **Web Browser** e acesse `http://8.8.8.8`.
    * **Resultado:** A página "ELDE Technology and Security" deve carregar.

2.  **Teste de Isolamento de Convidados (Falha e Sucesso):**
    * Vá no `Laptop-Guest` ou `Smartphone-Guest`.
    * Abra o **Web Browser** e acesse a Intranet (`www.elde.net.com` ou `192.168.100.10`).
    * **Resultado:** A página **falhará** (Request Timeout), provando que a ACL `GUEST_RULES` está bloqueando o acesso interno.
    * Agora, acesse a Internet (`http://8.8.8.8`).
    * **Resultado:** A página **funcionará**, provando que a ACL permite o tráfego para a Internet.

3.  **Teste de Acesso de Admin (Sucesso):**
    * Vá no `Laptop-IT Admin`.
    * Abra o **Command Prompt** e conecte-se via SSH ao roteador: `ssh -l admin 192.168.25.1`.
    * **Resultado:** O login SSH deve funcionar.

## Destaques da Configuração

Abaixo estão alguns dos principais trechos de código que fazem a rede funcionar.

### 1. ACL `GUEST_RULES` (No `Roteador-ELDE`)
Esta ACL é a principal regra de segurança para convidados. A ordem é crucial: ela nega o tráfego interno (Regra 20) *antes* de permitir o tráfego da Internet (Regra 30).

```bash
ip access-list extended GUEST_RULES
 ! Regra 10: Permitir que o DHCP funcione
 10 permit udp host 0.0.0.0 host 255.255.255.255 eq bootps
 ! Regra 20: Bloquear acesso a TODAS as redes internas (192.168.x.x)
 20 deny ip 192.168.99.0 0.0.0.255 192.168.0.0 0.0.255.255
 ! Regra 30: Permitir todo o resto (a Internet)
 30 permit ip any any
