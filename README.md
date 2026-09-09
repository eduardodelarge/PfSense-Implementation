# Laboratório de Firewall e Segmentação de Rede com pfSense

**Autor:** Eduardo Sousa

## Objetivo

Construir um ambiente de laboratório utilizando VirtualBox para simular uma arquitetura de rede corporativa com firewall, segmentação de rede, NAT e gerenciamento isolado.

---

# Índice

1. Visão Geral
2. Objetivos
3. Arquitetura do Ambiente
4. Componentes Utilizados
5. Configuração das Interfaces
6. Configuração do pfSense
7. Configuração do Ubuntu
8. Fluxo do Tráfego
9. Testes Realizados
10. Aprendizados
11. Próximos Passos

---

# 1. Visão Geral

Este laboratório foi desenvolvido para estudar conceitos de redes e segurança da informação utilizando o **pfSense** como firewall principal.

O ambiente foi projetado para simular uma infraestrutura onde:

- uma máquina Ubuntu representa um host interno;
- o pfSense atua como roteador e firewall;
- o host acessa apenas a interface de gerenciamento;
- o acesso à Internet ocorre exclusivamente através do firewall.

Essa arquitetura permite praticar conceitos como segmentação de redes, NAT, roteamento, regras de firewall e troubleshooting de conectividade.

---

# 2. Objetivos

- Compreender o funcionamento do pfSense.
- Implementar uma rede interna isolada.
- Utilizar NAT para acesso externo.
- Separar a rede de gerenciamento da rede operacional.
- Validar regras de firewall através de testes práticos.

---

# 3. Arquitetura do Ambiente

A topologia utilizada foi composta por três redes distintas.

## Topologia

> **Inserir imagem:** `images/topologia.png`

### Estrutura

| Equipamento | Função |
|------------|--------|
| Host | Administração |
| pfSense | Firewall e Roteador |
| Ubuntu | Cliente da rede interna |
| Internet | Acesso externo via NAT |

Fluxo simplificado:

```text
Ubuntu → pfSense → Internet
```

O host comunica-se apenas com a interface de gerenciamento do firewall.

---

# 4. Componentes Utilizados

| Componente | Versão |
|------------|--------|
| VirtualBox | *(preencher)* |
| pfSense | *(preencher)* |
| Ubuntu | *(preencher)* |
| Sistema Host | Windows |

## Recursos das Máquinas Virtuais

| Máquina | CPU | RAM |
|---------|-----|------|
| pfSense | *(preencher)* | *(preencher)* |
| Ubuntu | *(preencher)* | *(preencher)* |

---

# 5. Configuração das Interfaces

O ambiente utiliza três tipos de adaptadores de rede do VirtualBox.

## 5.1 WAN (NAT)

A interface WAN conecta o pfSense à Internet.

### Função

- acesso externo;
- atualização de pacotes;
- navegação das máquinas internas.

Fluxo:

```text
Ubuntu → pfSense → NAT → Internet
```

> **Inserir imagem:** `images/wan-nat.png`

---

## 5.2 LAN (LAN Segment)

O **LAN Segment** cria uma rede privada exclusiva entre as máquinas virtuais.

### Características

- comunicação apenas entre VMs do mesmo segmento;
- o host não participa dessa rede;
- o gateway é o pfSense.

### Endereçamento

| Dispositivo | IP |
|-------------|------|
| pfSense LAN | `192.168.10.1` |
| Ubuntu | `192.168.10.x` |

Fluxo:

```text
Ubuntu → LAN Segment → pfSense
```

> **Inserir imagem:** `images/lan-segment.png`

---

## 5.3 Gerenciamento (Host-Only)

A interface Host-Only foi utilizada exclusivamente para administrar o firewall.

Essa rede permite que o computador host acesse a interface web do pfSense sem fazer parte da rede interna.

### Endereçamento

| Dispositivo | IP |
|-------------|------|
| Host | `192.168.56.1` |
| pfSense MGMT | `192.168.56.2` |

A administração ocorre através do navegador.

```text
https://192.168.56.2
```

> **Inserir imagem:** `images/host-only.png`

---

# 6. Configuração do pfSense

Após instalar o pfSense foram realizadas as seguintes configurações.

## Interfaces

| Interface | Função |
|-----------|--------|
| WAN | Internet |
| LAN | Rede Interna |
| MGMT | Administração |

> **Inserir imagem:** `images/interfaces-pfsense.png`

## Configuração da LAN

- IP da LAN: `192.168.10.1`
- Máscara: `/24`

## Regras de Firewall

Foram criadas regras permitindo:

- acesso da LAN à Internet;
- administração pela interface Host-Only.

> **Inserir imagem:** `images/firewall-rules.png`

---

# 7. Configuração do Ubuntu

Após conectar o Ubuntu ao LAN Segment foram realizadas verificações básicas.

## Verificar endereço IP

```bash
ip addr
```

## Verificar gateway

```bash
ip route
```

## Testar comunicação com o gateway

```bash
ping 192.168.10.1
```

## Testar acesso à Internet

```bash
ping 8.8.8.8
```

## Testar resolução DNS

```bash
curl google.com
```

> **Inserir imagem:** `images/ubuntu-terminal.png`

---

# 8. Fluxo do Tráfego

O caminho percorrido por um pacote é o seguinte.

1. O Ubuntu gera um pacote.
2. O gateway da LAN envia ao pfSense.
3. O firewall verifica as regras.
4. O NAT traduz o endereço privado.
5. O pacote sai para a Internet.
6. A resposta retorna pelo mesmo caminho.

Representação:

```text
Ubuntu
   │
LAN Segment
   │
pfSense
   │
NAT
   │
Internet
```

> **Inserir imagem:** `images/fluxo-pacotes.png`

---

# 9. Testes Realizados

## Teste 1 – Comunicação com o Gateway

### Objetivo

Validar a comunicação entre Ubuntu e pfSense.

```bash
ping 192.168.10.1
```

**Resultado esperado**

- respostas ICMP recebidas.

> **Inserir imagem:** `images/teste-ping-gateway.png`

---

## Teste 2 – Acesso à Internet

### Objetivo

Validar o funcionamento do NAT.

```bash
ping 8.8.8.8
```

> **Inserir imagem:** `images/teste-internet.png`

---

## Teste 3 – Resolução DNS

### Objetivo

Confirmar que a resolução de nomes está funcionando corretamente.

```bash
curl google.com
```

> **Inserir imagem:** `images/teste-dns.png`

---

## Teste 4 – Regras de Firewall

Foram realizados testes permitindo e bloqueando tráfego.

Exemplos:

- ICMP permitido;
- portas específicas bloqueadas;
- validação através dos logs do pfSense.

> **Inserir imagem:** `images/firewall-logs.png`

---

# 10. Aprendizados

Este laboratório permitiu compreender, na prática, como um firewall controla o tráfego entre diferentes redes e como a segmentação reduz a exposição dos ativos internos.

Também reforçou conceitos como:

- NAT;
- roteamento;
- interfaces de gerenciamento;
- troubleshooting de conectividade utilizando `ping`, `ip route`, `curl` e os logs do pfSense.

---

# 11. Próximos Passos

O ambiente pode ser expandido para incluir funcionalidades comuns em redes corporativas.

## Melhorias planejadas

- [ ] Implementar VLANs utilizando IEEE 802.1Q.
- [ ] Configurar roteamento entre VLANs.
- [ ] Centralizar DHCP no pfSense.
- [ ] Instalar Suricata (IDS/IPS).
- [ ] Configurar pfBlockerNG.
- [ ] Integrar com Wazuh (SIEM).
- [ ] Monitorar o ambiente utilizando Zabbix.
- [ ] Criar dashboards no Grafana.

Essas evoluções aproximam o laboratório de cenários reais encontrados em ambientes corporativos de infraestrutura e segurança da informação.
