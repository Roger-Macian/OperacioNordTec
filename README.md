# Operació Nord Tec

**Nom i cognoms:** Roger Macian Torrens  
**Grup:** ASIX1B  
**Empresa consultora:** SecureSys Consulting  
**Empresa client:** NordTec  
**Projecte:** Operació Nord Tec

---

## Índex

1. [Missió i context del projecte](#1-missió-i-context-del-projecte)
2. [Estat del projecte](#2-estat-del-projecte)
3. [Arquitectura de la infraestructura](#3-arquitectura-de-la-infraestructura)
4. [Configuracions](#4-configuracions)
5. [Incidències i solucions](#5-incidències-i-solucions)
6. [Decisions tècniques](#6-decisions-tècniques)
7. [Reflexions tècniques](#7-reflexions-tècniques)

---

## 1. Missió i context del projecte

### Descripció del projecte

**Operació Nord Tec** és un projecte de desplegament i administració d'infraestructures TIC en què assumim el paper d'un equip d'administradors de sistemes de **SecureSys Consulting**, una empresa especialitzada en el desplegament i la protecció d'infraestructures informàtiques.

La nostra empresa ha estat contractada per **NordTec**, una empresa tecnològica de Barcelona dedicada als serveis SaaS (*Software as a Service*), que acaba d'iniciar la seva activitat i necessita construir tota la seva infraestructura informàtica des de zero.

NordTec no disposa d'una infraestructura prèvia ni d'un departament TIC propi. Per aquest motiu, el nostre equip serà responsable de planificar, implementar, configurar, protegir i documentar la infraestructura necessària.

Durant el desenvolupament del projecte, el client anirà presentant noves peticions que haurem de resoldre i incorporar a la infraestructura existent.

### Informació del client

| Camp | Informació |
|---|---|
| Empresa | NordTec |
| Plantilla | 25 treballadors |
| Sector | Empresa tecnològica · serveis SaaS |
| Ubicació | Barcelona |
| Departament TIC | No en disposa |
| Infraestructura inicial | Inexistent |
| Previsió | Creixement durant els propers mesos |
| Empresa responsable | SecureSys Consulting |

### Objectius del projecte

- Dissenyar i desplegar una infraestructura TIC des de zero.
- Planificar l'arquitectura de xarxa i dels sistemes.
- Configurar els dispositius, servidors i serveis necessaris.
- Implementar mesures de seguretat per protegir la infraestructura.
- Garantir la connectivitat i el funcionament dels serveis.
- Preparar la infraestructura per al futur creixement de NordTec.
- Detectar, analitzar i solucionar les incidències tècniques.
- Documentar les configuracions i justificar les decisions adoptades.
- Adaptar els sistemes a les noves necessitats del client.

### Metodologia de treball

El projecte es desenvolupa progressivament al llarg del curs, incorporant les noves peticions del client. La documentació principal es manté al fitxer `README.md` del repositori Git i inclou l'arquitectura de xarxa, les configuracions, les captures, les ordres, les incidències i les decisions tècniques. Al final de cada sessió es preveu actualitzar el repositori mitjançant commits descriptius.

**Criteri de documentació:** es diferencia entre configuració observada en fitxers, acció realitzada segons el registre de treball i funcionament verificat mitjançant proves. Una configuració escrita no implica automàticament que estigui aplicada o que funcioni.

---

## 2. Estat del projecte

### Tasques completades o documentades

- [x] Identificació del client i de les seves necessitats inicials.
- [x] Creació de l'estructura inicial del README.
- [x] Documentació de la missió i del context del projecte.
- [x] Identificació de **tres màquines virtuals diferenciades**: Firewall, Lab i Client.
- [x] Identificació de tres segments interns en la configuració del Firewall: Kali, DMZ i LAN.
- [x] Recollida dels fitxers de configuració Netplan mostrats per a les tres màquines.
- [x] Documentació del contingut del script `lmsi.sh` amb regles IPTables de filtratge, NAT i DNAT.
- [x] Registre d'incidències relacionades amb permisos, sintaxi YAML, configuració de xarxa i connectivitat.

### Accions realitzades segons el registre, pendents de verificació final

- [ ] Confirmar que Netplan s'ha aplicat correctament a les tres màquines.
- [ ] Confirmar l'estat efectiu de les regles d'IPTables, no només el contingut del script.
- [ ] Confirmar que el reenviament IPv4 està activat i que persisteix després de reiniciar.
- [ ] Confirmar la ruta predeterminada i la interfície real de sortida a Internet del Firewall.

### Tasques pendents

- [ ] Verificar les connexions virtuals i elaborar el diagrama físic/lògic definitiu.
- [ ] Completar la identificació de les interfícies externes `enp1s0` i `enp2s0`.
- [ ] Resoldre o documentar el problema de connectivitat cap a `8.8.8.8`.
- [ ] Verificar el funcionament de NAT i l'encaminament entre xarxes.
- [ ] Comprovar si el servei web de la DMZ està instal·lat i en funcionament.
- [ ] Revisar les polítiques de seguretat del Firewall abans de donar-les per definitives.
- [ ] Incorporar captures de les comprovacions i de la topologia.
- [ ] Fer les proves finals de connectivitat i serveis.

**Estat actual:** infraestructura de laboratori **parcialment configurada i documentada**, amb comprovacions operatives encara pendents. No s'ha acreditat la connectivitat completa a Internet.

---

## 3. Arquitectura de la infraestructura

### Màquines virtuals identificades

Aquesta pràctica fa servir **tres màquines diferents**:

| Màquina | Identificador observat | Sistema | Funció |
|---|---|---|---|
| Firewall | `Lab_0373_firewall` | Ubuntu Server | Router/firewall central, segmentació i NAT |
| Lab | `UbuntuServer_lab_0373` | Ubuntu Server | Servidor ubicat a la xarxa DMZ; servei web pendent de confirmar |
| Client | `ubuntu22` / `Ubuntu_desk_lab0226` | Ubuntu Desktop | Estació client de la LAN |

**Nota:** la interfície anomenada `#kali` al Firewall identifica un segment de xarxa; no s'ha confirmat una quarta màquina Kali activa en aquesta pràctica.

### Diagrama lògic provisional

```text
        Xarxes externes / xarxa del laboratori
                    (per confirmar)
                       |       |
                  enp1s0     enp2s0
                   DHCP        DHCP
                       \       /
                  +-------------------+
                  | Firewall          |
                  | Ubuntu Server     |
                  | IPTables / NAT    |
                  +-------------------+
                       |    |     |
               enp3s0 |    |     | enp5s0
              10.0.0.1|    |     |192.168.20.1
                       |    |     +--> LAN: 192.168.20.0/24
                       |    |           Client: 192.168.20.10
                       |    |           GW: 192.168.20.1
                       |    |
                       |    | enp4s0: 192.168.10.1
                       |    +--------> DMZ: 192.168.10.0/24
                       |                 Lab: 192.168.10.10
                       |                 GW: 192.168.10.1
                       |
                       +-------------> Segment Kali: 10.0.0.0/24
                                         (equips no confirmats)
```

> El diagrama reflecteix els segments previstos segons Netplan, **no** una verificació de les connexions de la plataforma de virtualització. Cal incorporar una captura de la topologia real.

### Interfícies del Firewall

Configuració mostrada de `/etc/netplan/50-cloud-init.yaml`:

| Interfície | Mode IPv4 | IP configurada | Segment / funció anotada |
|---|---|---|---|
| `enp1s0` | DHCP | Assignació dinàmica (no coneguda) | Connexió externa probable; sortida usada al script NAT |
| `enp2s0` | DHCP | Assignació dinàmica (no coneguda) | Connexió addicional pendent d'identificar |
| `enp3s0` | Estàtica | `10.0.0.1/24` | Segment «Kali» |
| `enp4s0` | Estàtica | `192.168.10.1/24` | DMZ |
| `enp5s0` | Estàtica | `192.168.20.1/24` | LAN |

### Adreçament IP per màquina

| Dispositiu | Interfície | IP / màscara | Porta d'enllaç configurada | Funció |
|---|---|---|---|---|
| Firewall | `enp3s0` | `10.0.0.1/24` | No aplicable a la interfície interna | Gateway segment Kali |
| Firewall | `enp4s0` | `192.168.10.1/24` | No aplicable a la interfície interna | Gateway DMZ |
| Firewall | `enp5s0` | `192.168.20.1/24` | No aplicable a la interfície interna | Gateway LAN |
| Lab | `enp1s0` | `192.168.10.10/24` | `192.168.10.1` | Servidor DMZ |
| Client | `enp1s0` | `192.168.20.10/24` | `192.168.20.1` | Estació client LAN |

### Xarxes i protocols

| Xarxa | Rang CIDR | Passarel·la al Firewall | Propòsit |
|---|---|---|---|
| Kali | `10.0.0.0/24` | `10.0.0.1` | Segment separat |
| DMZ | `192.168.10.0/24` | `192.168.10.1` | Xarxa del servidor Lab |
| LAN | `192.168.20.0/24` | `192.168.20.1` | Xarxa del client |

**Tecnologies presents en la configuració:** IPv4, TCP/IP, DHCP (dues interfícies externes del Firewall), adreçament estàtic, encaminament IPv4, IPTables, NAT de sortida (`MASQUERADE`) i redirecció de destinació (`DNAT`). La resolució DNS i el servei HTTP encara no estan verificats.

**Arquitectura de l'aplicació:** pendent de concretar. El script conté una redirecció del port TCP 80 cap al servidor Lab, però això no acredita que el servei HTTP estigui instal·lat o publicat correctament.

---

## 4. Configuracions

### 4.1. Firewall: configuració de xarxa amb Netplan

**Màquina:** `Lab_0373_firewall`  
**Fitxer observat:** `/etc/netplan/50-cloud-init.yaml`

```yaml
network:
  version: 2
  ethernets:
    enp1s0:
      dhcp4: true
    enp2s0:
      dhcp4: true
    # kali
    enp3s0:
      dhcp4: false
      addresses:
        - 10.0.0.1/24
    # dmz
    enp4s0:
      dhcp4: false
      addresses:
        - 192.168.10.1/24
    # lan
    enp5s0:
      dhcp4: false
      addresses:
        - 192.168.20.1/24
```

Aquesta configuració defineix dues interfícies DHCP i tres xarxes internes amb IP estàtica. **No** es pot confirmar a partir del fitxer quines adreces ha rebut DHCP ni quina ruta per defecte s'utilitza realment.

> **Atenció a cloud-init:** el mateix fitxer adverteix que la configuració pot ser generada automàticament i que els canvis poden no persistir. Abans de desactivar la gestió de xarxa de cloud-init, cal revisar les instruccions del laboratori.

**Verificacions recomanades (sense modificar configuracions):**

```bash
ip -br addr
ip route
sudo netplan generate
```

### 4.2. Lab: servidor de la DMZ

**Màquina:** `UbuntuServer_lab_0373`  
**Interfície:** `enp1s0`  
**Fitxer de configuració mostrat:** Netplan (nom definitiu del fitxer pendent de confirmar)

```yaml
network:
  version: 2
  ethernets:
    enp1s0:
      dhcp4: false
      addresses:
        - 192.168.10.10/24
      routes:
        - to: default
          via: 192.168.10.1
```

**Funció:** assignar una IP fixa al servidor Lab i fer servir la interfície DMZ del Firewall (`192.168.10.1`) com a porta d'enllaç.

A les captures es documenten intents anteriors amb errors d'indentació de YAML i una porta d'enllaç escrita incorrectament com `192.168.10.1/24`. La versió mostrada a dalt recull les correccions de sintaxi compartides. **Aplicació efectiva pendent de confirmar.**

### 4.3. Client: configuració de LAN amb NetworkManager

**Màquina:** `ubuntu22` / `Ubuntu_desk_lab0226`  
**Fitxer mostrat:** `/etc/netplan/01-network-manager-all.yaml`

```yaml
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    enp1s0:
      dhcp4: false
      addresses:
        - 192.168.20.10/24
      routes:
        - to: default
          via: 192.168.20.1
      nameservers:
        addresses: []
```

**Observació:** el fragment original compartit deixava `nameservers.addresses:` sense cap valor visible. En aquesta transcripció s'ha indicat una llista buida (`[]`) per fer explícit que **no s'ha aportat cap DNS**; no és prova que aquest sigui el contingut exacte guardat. Si es necessita resolució de noms, caldrà definir servidors DNS adequats.

**Resultat de prova observat:**

```text
ping 8.8.8.8
12 packets transmitted, 0 received, 100% packet loss
```

La prova indica que **en aquell moment** el Client no rebia resposta d'Internet. No identifica per si sola si l'error era de la ruta, el Firewall, NAT, la xarxa externa o un bloqueig d'ICMP.

### 4.4. Script de Firewall: `lmsi.sh`

**Sistema:** Firewall  
**Tecnologia:** IPTables (filtratge i traducció d'adreces)

**Script compartit:**

```bash
#!/bin/bash

# 1. Limpieza de tablas existentes
iptables -F
iptables -t nat -F
iptables -X

# 2. Política por defecto (Permisiva para routing)
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# 3. Enmascaramiento para salida a Internet (NAT)
iptables -t nat -A POSTROUTING -s 192.168.10.0/24 -o enp1s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 192.168.20.0/24 -o enp1s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o enp1s0 -j MASQUERADE

# 4. Publicación del servidor Web interno (DNAT)
iptables -t nat -A PREROUTING -i enp3s0 -p tcp --dport 80 -j DNAT --to-destination 192.168.10.10
iptables -t nat -A PREROUTING -i enp3s0 -p tcp -d 10.0.0.1 --dport 80 -j DNAT --to-destination 192.168.10.10
```

**Lectura tècnica:**

- `iptables -F`, `iptables -X` i `iptables -t nat -F` eliminen les regles/cadenes corresponents de les taules de filtratge i NAT. **És una operació disruptiva** que pot eliminar proteccions existents.
- Les polítiques `INPUT`, `FORWARD` i `OUTPUT` es defineixen com `ACCEPT`, és a dir, una configuració **permissiva de laboratori**, no un firewall de producció endurit.
- Les regles `POSTROUTING` intenten aplicar NAT de sortida a les tres subxarxes internes quan el trànsit surt per `enp1s0`.
- Les regles `PREROUTING` redirigeixen el trànsit TCP destinat al port 80 que **entra per `enp3s0`** cap a `192.168.10.10`. Això no equival a publicar el servidor web a Internet per la interfície externa.
- La segona regla DNAT és més específica per la destinació `10.0.0.1:80`, però pot resultar redundant per als paquets que ja coincideixen amb la primera regla general. Cal revisar-ne l'objectiu.
- El script no inclou activació de `ip_forward` ni persistència automàtica de regles després d'un reinici.

**Comandes d'execució registrades:**

```bash
sudo chmod +x lmsi.sh
sudo chown isard:isard lmsi.sh
sudo ./lmsi.sh
```

El canvi de propietari no és estrictament necessari per executar un script amb `sudo`. Per observar les regles carregades es va proposar/utilitzar:

```bash
sudo iptables -L
sudo iptables -t nat -L -n -v
```

**Precaució:** `iptables -L` consulta per defecte la taula `filter`; no mostra les regles de NAT. A més, que hi hagi regles a `nat` no demostra que els paquets arribin ni que surtin correctament.

**Estat de verificació:** el contingut del script està documentat; falta una captura o sortida completa de les regles efectives i dels comptadors.

### 4.5. Activació de l'encaminament IPv4

**Ordre utilitzada segons el resum de treball:**

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Activa el reenviament IPv4 **de manera temporal**. S'ha de confirmar el valor actual:

```bash
sysctl net.ipv4.ip_forward
```

Si el laboratori requereix persistència després del reinici, es pot configurar mitjançant un fitxer específic de `sysctl.d`, prèvia comprovació que no hi hagi directives contradictòries:

```bash
echo 'net.ipv4.ip_forward=1' | sudo tee /etc/sysctl.d/99-ip-forward.conf
sudo sysctl --system
```

Aquesta configuració persistent és **una proposta**, no una acció confirmada.

### 4.6. Ordres de comprovació i diagnòstic

Executar al **Firewall**:

```bash
ip -br addr
ip route
sudo iptables -S
sudo iptables -t nat -S
sudo iptables -t nat -L -n -v
sysctl net.ipv4.ip_forward
ping -c 4 8.8.8.8
```

Executar al **Lab**:

```bash
ip -br addr
ip route
ping -c 4 192.168.10.1
ping -c 4 8.8.8.8
```

Executar al **Client**:

```bash
ip -br addr
ip route
ping -c 4 192.168.20.1
ping -c 4 192.168.10.10
ping -c 4 8.8.8.8
```

Aquestes ordres són de diagnòstic i no substitueixen les proves de HTTP, DNS o permisos de trànsit entre segments. Els resultats estan **pendents** de documentar.

### 4.7. Captures de pantalla pendents d'incorporar al repositori

- `[PENDENT]` Netplan definitiu del Firewall (`50-cloud-init.yaml`).
- `[PENDENT]` Netplan del Lab (`192.168.10.10/24`).
- `[PENDENT]` Netplan del Client (`192.168.20.10/24`).
- `[PENDENT]` Script `lmsi.sh` i comprovació de les regles NAT.
- `[PENDENT]` Resultats de les proves de ping i taules de rutes.
- `[PENDENT]` Topologia real de la infraestructura virtual.

---

## 5. Incidències i solucions

La informació prové de les captures i del resum de treball compartits. Quan falta una nova prova després de la correcció, s'indica explícitament.

### INC-01 · Error de permisos en executar `lmsi.sh`

- **Símptoma:** `Permission denied` en executar `./lmsi.sh`.
- **Causa probable:** el fitxer no tenia permís d'execució. També calen privilegis elevats per modificar IPTables.
- **Accions registrades:** `sudo chmod +x lmsi.sh`, `sudo chown isard:isard lmsi.sh`, `sudo ./lmsi.sh`.
- **Estat:** execució indicada com a resolta al resum; no s'ha aportat el registre final complet d'execució.

### INC-02 · Les cadenes d'IPTables semblaven buides

- **Símptoma:** `sudo iptables -L` no mostrava les regles NAT esperades.
- **Causa:** la consulta sense `-t nat` mostra la taula `filter`, no la taula `nat`.
- **Comprovació:** `sudo iptables -t nat -L -n -v`.
- **Estat:** diferència de taules aclarida; falta verificar els comptadors i el trànsit real.

### INC-03 · Fitxer Netplan amb indentació incorrecta

- **Missatge observat:** `Invalid YAML: inconsistent indentation: ethernets:`.
- **Causa:** l'estructura jeràrquica de YAML depèn de la indentació correcta.
- **Acció:** corregir la indentació de `network`, `version`, `ethernets`, les interfícies i les subclaus.
- **Estat:** s'han compartit versions corregides; manca la confirmació final de `sudo netplan generate`/`try`.

### INC-04 · Claus i valors incorrectes a Netplan

- **Errors detectats:** ús de `ethernet:` en lloc de `ethernets:`; porta d'enllaç amb màscara (`via: 192.168.10.1/24`).
- **Correcció:** `ethernets:` i `via: 192.168.10.1`.
- **Estat:** correccions reflectides als fragments posteriors.

### INC-05 · Pèrdua de connectivitat del Client

- **Prova observada:** `ping 8.8.8.8` amb 12 paquets enviats, 0 rebuts i **100 % de pèrdua**.
- **Hipòtesis de diagnòstic:** error de gateway, reenviament IPv4 inactiu, NAT per interfície incorrecta, manca de connectivitat externa del Firewall o filtratge d'ICMP.
- **Accions relacionades:** revisió de Netplan, regles NAT i activació temporal de `net.ipv4.ip_forward=1`.
- **Estat:** **pendent de verificació**; no s'ha aportat una prova posterior amb resposta correcta.

### INC-06 · Necessitat d'activar el reenviament IPv4

- **Context:** el Firewall ha d'encaminar trànsit entre les xarxes internes i la connexió exterior.
- **Acció registrada:** `sudo sysctl -w net.ipv4.ip_forward=1`.
- **Limitació:** l'ordre no garanteix persistència després de reiniciar.
- **Estat:** aplicació temporal indicada al resum; estat actual i persistència pendents de confirmar.

### INC-07 · Paquet `xmllint` no localitzat

- **Màquina:** Firewall.
- **Ordre observada:** `sudo apt install xmllint`.
- **Missatge observat:** `E: No se ha podido localizar el paquete xmllint`.
- **Causa:** `xmllint` és el nom de l'eina, però a Ubuntu generalment es distribueix al paquet `libxml2-utils`.
- **Solució proposada:** `sudo apt update && sudo apt install libxml2-utils`.
- **Nota:** `xmllint` valida XML; **no** s'utilitza per validar els fitxers YAML de Netplan.
- **Estat:** pendent de confirmar si cal aquesta eina i si s'ha instal·lat.

### INC-08 · Ambigüitat en la interfície de sortida i el NAT

- **Observació:** el Firewall té `enp1s0` i `enp2s0` amb DHCP, però el script fa `MASQUERADE` només en sortir per `enp1s0`.
- **Risc:** si la ruta per defecte surt per `enp2s0`, les regles NAT actuals podrien no coincidir amb el trànsit esperat.
- **Diagnòstic pendent:** `ip route`, `ip -br addr` i comptadors d'IPTables.
- **Estat:** pendent.

### Plantilla per registrar noves incidències

**Incidència:** `[Títol descriptiu]`  
**Missatge d'error exacte:** `[Text o captura]`  
**Quan:** `[Data, sessió i context]`  
**Causa:** `[Verificada o hipòtesi]`  
**Solució:** `[Ordres i canvis aplicats]`  
**Detectada per:** `[@usuari]`  
**Prova de validació:** `[Resultat mesurable]`  
**Estat:** `Pendent / En estudi / Resolta i verificada`

---

## 6. Decisions tècniques

| Decisió | Opció emprada | Motivació | Limitacions o alternatives |
|---|---|---|---|
| Sistema del Firewall | Ubuntu Server | Administració de xarxa i IPTables en Linux | Revisar versió i manteniment |
| Sistemes dels equips | Ubuntu Server (Lab) i Ubuntu Desktop (Client) | Diferenciar servidor i estació de treball | Versions exactes pendents |
| Topologia | Firewall central i xarxes separades Kali/DMZ/LAN | Segmentar els diferents entorns | Connexions virtuals pendents de verificar |
| Adreçament intern | IPv4 privat estàtic `/24` | Gateways i equips previsibles | Cal controlar IP duplicades |
| Connectivitat externa | DHCP a `enp1s0` i `enp2s0` del Firewall | Assignació dinàmica configurada | Interfície de sortida efectiva no confirmada |
| Encaminament | `net.ipv4.ip_forward=1` | Permetre el trànsit entre interfícies | Persistència no demostrada |
| NAT | IPTables `MASQUERADE` | Compartir la sortida externa amb subxarxes internes | Depèn de la interfície real de sortida |
| Redirecció HTTP | DNAT TCP 80 cap a `192.168.10.10` des d'`enp3s0` | Prova de publicació/redirecció al servidor DMZ | No publica automàticament a Internet |
| Seguretat del filtratge | Polítiques `ACCEPT` del script | Facilita proves inicials d'encaminament | **No és una política segura de producció** |
| Documentació | Git + `README.md` | Traçabilitat i registre de canvis | Cal adjuntar evidències i resultats |

### Justificació de la segmentació

La divisió entre LAN (`192.168.20.0/24`), DMZ (`192.168.10.0/24`) i segment Kali (`10.0.0.0/24`) permet establir configuracions i polítiques diferenciades en el futur. **La separació d'adreces no implica per si sola aïllament de seguretat:** amb la política `FORWARD ACCEPT`, un trànsit entre segments podria estar permès llevat que altres regles ho impedeixin.

### Decisions i millores pendents

1. Confirmar quina interfície del Firewall surt realment a Internet.
2. Definir una política de filtratge restrictiva segons els requisits del laboratori, en lloc de mantenir `ACCEPT` de manera indiscriminada.
3. Decidir si el servei web s'ha de publicar només des de la xarxa Kali, des de la LAN o des d'una xarxa externa; adaptar DNAT a la necessitat real.
4. Revisar la persistència d'IPTables, Netplan i `ip_forward` després d'un reinici.
5. Definir serveis DNS i documentar com els equips resolen noms.
6. Incloure un procediment de còpia de seguretat de les configuracions abans de modificar-les.

---

## 7. Reflexions tècniques

### Coneixements adquirits

El treball ha permès practicar la separació de funcions entre màquines virtuals, l'ús d'IPv4 amb adreces estàtiques i DHCP, la configuració de Netplan amb YAML i la relació entre taules `filter` i `nat` d'IPTables. També ha servit per diferenciar l'execució d'un script de la verificació efectiva de la seva funció.

### Dificultats trobades

- La indentació de YAML i l'escriptura correcta de `ethernets:` i `via:`.
- Els permisos d'execució i la necessitat d'utilitzar privilegis administratius per modificar IPTables.
- La interpretació de les taules de filtratge i NAT.
- La configuració d'encaminament entre diverses interfícies.
- La investigació d'una prova de connectivitat amb el 100 % de pèrdua de paquets.
- La identificació del paquet correcte que proporciona l'ordre `xmllint`.

### Possibles millores

- Fer proves escalonades: IP local → gateway del segment → Firewall extern → IP pública → DNS → servei HTTP.
- Registrar captures de `ip route`, `ip -br addr` i comptadors d'IPTables abans i després de cada modificació.
- Evitar canvis globals de filtratge sense una còpia prèvia o una consola de recuperació.
- Endurir les regles del Firewall i limitar el trànsit entre LAN, DMZ i altres segments segons necessitats reals.
- Mantenir les configuracions versionades amb Git i documentar cada incidència amb una prova de validació.

### Conclusions

L'Operació Nord Tec ha passat de la documentació inicial a una **primera configuració de laboratori** amb tres màquines independents: un Firewall/router, un servidor Lab a la DMZ i un Client a la LAN. La configuració mostrada preveu tres segments interns i utilitza Netplan i IPTables per gestionar-ne l'adreçament i les traduccions de xarxa.

Encara no es pot considerar acabada la infraestructura: falten evidències de la configuració efectiva del Firewall, comprovacions de ruta i NAT, verificació dels serveis i una prova satisfactòria de connectivitat de punta a punta. Aquestes comprovacions serviran per distingir una configuració aparentment correcta d'un sistema realment funcional, segur i mantenible.

---

*Document en evolució. Actualitzar els apartats «pendent» després de validar la configuració real de les màquines.*
