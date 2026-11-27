# 🌐 Tutorial: Configuração de Entroncamento (VLAN Trunking) em Rede Hierárquica

Este repositório apresenta a documentação técnica em formato de **tutorial explicativo** referente à configuração de entroncamento (**modo trunk**) nas portas de interligação entre switches de uma topologia hierárquica no **Cisco Packet Tracer**, garantindo a passagem das **VLANs 10 (Alunos)** e **20 (Visitantes)**.

---

## 🎯 Objetivo da Atividade

Configurar o entroncamento (**modo trunk**) nas portas que interligam os switches nas camadas de **Acesso**, **Distribuição** e **Núcleo**, assegurando que o tráfego das **VLANs 10 (`Alunos`)** e **20 (`Visitantes`)** possa trafegar adequadamente entre os equipamentos da topologia.

---

## 🖼️ 1. Diagrama Atualizado da Topologia

![Diagrama da Topologia Atualizado](./diagrama-topologia.png)

> **Dica de entrega:** Salve a imagem da topologia no repositório com o nome `diagrama-topologia.png` (ou ajuste o caminho acima para o nome exato do seu arquivo de imagem).

### Observações da Topologia Representada no Diagrama

De acordo com a imagem da rede no Cisco Packet Tracer, a topologia é composta pelos seguintes blocos:

* **Topo / Conexão Externa:**
  * `Router0` (modelo `2811`) conectado a `Multilayer Switch0` e `Multilayer Switch1` (indicadores verdes ativos).
  * `Server0` (`Server-PT`) conectado diretamente a `Multilayer Switch1` (indicadores verdes ativos).
* **Camada de Núcleo (Core):**
  * `Multilayer Switch0` (`3650-24PS`) e `Multilayer Switch1` (`3650-24PS`) interligados entre si por **4 enlaces paralelos** (com indicadores verdes e laranja em convergência/estado).
* **Camada de Distribuição (Distribution):**
  * `Multilayer Switch2` (`3650-24PS`) conectado a `Multilayer Switch0` por **2 enlaces paralelos destacados em vermelho** (com indicadores verdes e laranja).
  * `Multilayer Switch3` (`3650-24PS`) conectado a `Multilayer Switch1` por **2 enlaces paralelos destacados em vermelho** (com indicadores verdes e laranja).
* **Camada de Acesso (Access):**
  * `Switch0` (`2960-24TT`) e `Switch1` (`2960-24TT`) conectados a `Multilayer Switch2` (1 enlace cada).
  * `Switch2` (`2960-24TT`) e `Switch3` (`2960-24TT`) conectados a `Multilayer Switch3` (1 enlace cada).
* **Dispositivos Finais (Endpoints):**
  * Cada switch de acesso (`Switch0` a `Switch3`) possui um computador (`PC0` a `PC3`) e um laptop (`Laptop0` a `Laptop3`) associados na parte inferior do diagrama.

---

## 📋 2. Documentação Técnica Detalhada

### 2.1. VLANs Configuradas

| ID da VLAN | Nome da VLAN | Status Esperado | Finalidade |
| :--- | :--- | :--- | :--- |
| **10** | `Alunos` | `active` | Tráfego de rede da VLAN 10 |
| **20** | `Visitantes` | `active` | Tráfego de rede da VLAN 20 |

> **Atenção importante:** Para que os enlaces trunk permitam efetivamente o tráfego das VLANs `10` e `20` e para que elas apareçam como ativas e em encaminhamento no *Spanning Tree Protocol (STP)*, as VLANs **10** e **20** devem estar criadas na base de dados de cada switch antes ou durante a validação.

### 2.2. Mapeamento das Interfaces por Camada e Enlace

Como os rótulos textuais das portas não aparecem ampliados no zoom do diagrama, utilize os nomes exatos das interfaces exibidos ao passar o cursor ou ativar **Always Show Port Labels** no Packet Tracer (`Options > Preferences > Always Show Port Labels in Logical Workspace`):

| Camada | Equipamento de Origem | Modelo | Qtd. de Enlaces | Equipamento de Destino | Tipo de Enlace Esperado | VLANs Permitidas |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Núcleo** | `Multilayer Switch0` | `3650-24PS` | 4 paralelos | `Multilayer Switch1` (`3650-24PS`) | **Trunk** (`802.1Q`) | `10, 20` |
| **Núcleo** | `Multilayer Switch1` | `3650-24PS` | 4 paralelos | `Multilayer Switch0` (`3650-24PS`) | **Trunk** (`802.1Q`) | `10, 20` |
| **Distribuição / Núcleo** | `Multilayer Switch2` | `3650-24PS` | 2 paralelos (vermelhos) | `Multilayer Switch0` (`3650-24PS`) | **Trunk** (`802.1Q`) | `10, 20` |
| **Distribuição / Núcleo** | `Multilayer Switch3` | `3650-24PS` | 2 paralelos (vermelhos) | `Multilayer Switch1` (`3650-24PS`) | **Trunk** (`802.1Q`) | `10, 20` |
| **Acesso / Distribuição** | `Switch0` | `2960-24TT` | 1 | `Multilayer Switch2` (`3650-24PS`) | **Trunk** (`802.1Q`) | `10, 20` |
| **Acesso / Distribuição** | `Switch1` | `2960-24TT` | 1 | `Multilayer Switch2` (`3650-24PS`) | **Trunk** (`802.1Q`) | `10, 20` |
| **Acesso / Distribuição** | `Switch2` | `2960-24TT` | 1 | `Multilayer Switch3` (`3650-24PS`) | **Trunk** (`802.1Q`) | `10, 20` |
| **Acesso / Distribuição** | `Switch3` | `2960-24TT` | 1 | `Multilayer Switch3` (`3650-24PS`) | **Trunk** (`802.1Q`) | `10, 20` |

#### Considerações Técnicas sobre os Enlaces do Diagrama
1. **Diferença entre Switches `3650-24PS` e `2960-24TT`:**
   * Nos **Multilayer Switches (`3650-24PS`)**, é necessário definir explicitamente o encapsulamento `switchport trunk encapsulation dot1q` antes de aplicar `switchport mode trunk`.
   * Nos **Switches de Acesso (`2960-24TT`)**, o encapsulamento `802.1Q` já é o padrão.
2. **Enlaces paralelos e convergência STP:**
   * Há 4 enlaces paralelos entre `Multilayer Switch0` e `Multilayer Switch1`, e 2 enlaces paralelos ligando cada switch de distribuição (`Multilayer Switch2` e `Multilayer Switch3`) ao respectivo switch de núcleo.
   * Indicadores **laranja** nos enlaces entre switches indicam convergência do *Spanning Tree Protocol (STP)* ou portas em estado não encaminhador enquanto o protocolo avalia a topologia.

---

## 💻 3. Comandos Sequenciais do Cisco IOS

### 3.1. Criação das VLANs 10 e 20 (Em todos os Switches)

```cisco
enable
configure terminal
vlan 10
 name Alunos
vlan 20
 name Visitantes
end
```

### 3.2. Configuração de Trunk na Camada de Acesso (`Switch0`, `Switch1`, `Switch2` e `Switch3` — Modelo `2960-24TT`)

Substitua `<porta_uplink>` pela interface que interliga o switch de acesso ao switch de distribuição (ex.: `GigabitEthernet0/1` ou `FastEthernet0/24`):

```cisco
enable
configure terminal
interface <porta_uplink>
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
end
copy running-config startup-config
```

### 3.3. Configuração de Trunk na Camada de Distribuição (`Multilayer Switch2` e `Multilayer Switch3` — Modelo `3650-24PS`)

Substitua `<portas_trunk_distribuicao>` pelas interfaces que conectam aos switches de acesso e aos 2 enlaces paralelos com o núcleo:

```cisco
enable
configure terminal
interface range <portas_trunk_distribuicao>
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
end
copy running-config startup-config
```

### 3.4. Configuração de Trunk na Camada de Núcleo (`Multilayer Switch0` e `Multilayer Switch1` — Modelo `3650-24PS`)

Substitua `<portas_trunk_nucleo>` pelas 4 interfaces paralelas entre `Multilayer Switch0` e `Multilayer Switch1` e pelas 2 interfaces que descem para o switch de distribuição correspondente:

```cisco
enable
configure terminal
interface range <portas_trunk_nucleo>
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
end
copy running-config startup-config
```

---

## 🔍 4. Verificação da Configuração

Execute os comandos abaixo no modo privilegiado (`#`) de cada switch para conferir se os troncos subiram corretamente e se as VLANs `10` e `20` estão permitidas e ativas:

```cisco
show interfaces trunk
show vlan brief
```

### O que verificar na saída de `show interfaces trunk`
* **Mode:** `on`
* **Encapsulation:** `802.1q`
* **Status:** `trunking`
* **Vlans allowed on trunk:** `10,20`
* **Vlans allowed and active in management domain:** `10,20` (com as VLANs `10` e `20` criadas no switch)
* **Vlans in spanning tree forwarding state and not pruned:** `10,20`