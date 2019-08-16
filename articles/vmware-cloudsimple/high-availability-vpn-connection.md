---
title: 'Soluzione VMware di Azure di CloudSimple: configurare la disponibilità elevata dal gateway VPN da sito locale a CloudSimple'
description: Viene descritto come configurare una connessione a disponibilità elevata dall'ambiente locale a un gateway VPN CloudSimple abilitato per la disponibilità elevata
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d08dce95836328f6a0991601951057944fa5f61e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536443"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Configurare una connessione a disponibilità elevata da locale a gateway VPN CloudSimple

Gli amministratori di rete possono configurare una connessione VPN da sito a sito IPsec a disponibilità elevata dall'ambiente locale a un gateway VPN CloudSimple.

Questa guida illustra i passaggi per configurare un firewall locale per una connessione VPN da sito a sito IPsec a disponibilità elevata. I passaggi dettagliati sono specifici del tipo di firewall locale. Come esempio, in questa guida vengono illustrati i passaggi per due tipi di firewall: Cisco ASA e Palo Alto Networks.

## <a name="default-configuration-for-cloudsimple-vpn-gateways"></a>Configurazione predefinita per i gateway VPN CloudSimple

Per impostazione predefinita, i gateway VPN CloudSimple sono configurati in modalità IKEv1 insieme agli attributi fase 1 e fase 2 seguenti. Se si desidera utilizzare attributi VPN diversi oppure utilizzare IKEv2 anziché IKEV1, <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">aprire una richiesta di supporto</a>.

### <a name="phase-1"></a>Fase 1

| Parametro | Valore |
|-----------|-------|
| Versione IKE | IKEv1 |
| Crittografia | AES 256 |
| Algoritmo hash| SHA 256 |
| Gruppo Diffie Hellman (gruppo DH) | 1 |
| Durata | 86.400 secondi |
| Dimensioni dei dati | 4 GB |

### <a name="phase-2"></a>Fase 2

| Parametro | Value |
|-----------|-------|
| Crittografia | AES 256 |
| Algoritmo hash| SHA 256 |
| Gruppo della segretezza diretta perfetto (gruppo PFS) | Nessuna |
| Durata | 28.800 secondi |
| Dimensioni dei dati | 4 GB |

## <a name="before-you-begin"></a>Prima di iniziare

Prima di configurare il firewall locale, completare le seguenti attività.

1. Verificare che l'organizzazione abbia [acquistato](create-nodes.md) i nodi necessari e che sia stato creato almeno un cloud privato CloudSimple.
2. [Configurare un gateway VPN da sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tra la rete locale e il cloud privato CloudSimple.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Configurare il firewall Cisco ASA locale

Le istruzioni riportate in questa sezione si applicano a Cisco ASA versione 8,4 e successive. Nell'esempio di configurazione, Cisco Adaptive Security Appliance software versione 9,10 viene distribuito e configurato in modalità IKEv1.

Per il funzionamento della VPN da sito a sito, è necessario consentire UDP 500/4500 e ESP (protocollo IP 50) dall'IP pubblico primario e secondario di CloudSimple (IP peer) nell'interfaccia esterna del gateway VPN Cisco ASA locale.

### <a name="1-configure-phase-1-ikev1"></a>1. Configurare la fase 1 (IKEv1)

Per abilitare la fase 1 (IKEv1) nell'interfaccia esterna, immettere il comando CLI seguente nel firewall Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Creare un criterio IKEv1

Creare un criterio IKEv1 che definisce gli algoritmi e i metodi da usare per l'hashing, l'autenticazione, il gruppo Diffie-Hellman, la durata e la crittografia.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Creare un gruppo di tunnel

Creare un gruppo di tunnel sotto gli attributi IPsec. Configurare l'indirizzo IP del peer e la chiave precondivisa del tunnel, che è possibile impostare durante [la configurazione del gateway VPN da sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Configurare la fase 2 (IPsec)

Per configurare la fase 2 (IPsec), creare un elenco di controllo di accesso (ACL) che definisce il traffico da crittografare e tunneling. Nell'esempio seguente il traffico di interesse si trova nel tunnel originato dalla subnet locale (10.16.1.0/24) alla subnet remota del cloud privato (192.168.0.0/24). Se sono presenti più subnet tra i siti, l'ACL può contenere più voci.

In Cisco ASA versioni 8,4 e successive, è possibile creare oggetti o gruppi di oggetti che fungano da contenitori per reti, subnet, indirizzi IP host o più oggetti. Creare un oggetto per il locale e un oggetto per le subnet remote e usarli per le istruzioni Crypto ACL e NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Definire una subnet locale locale come oggetto

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Definire la subnet remota CloudSimple come oggetto

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Configurare un elenco di accesso per il traffico di interesse

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Configurare il set di trasformazioni

Configurare il set di trasformazione (TS), che deve coinvolgere la ```ikev1```parola chiave. Gli attributi di crittografia e hash specificati nel TS devono corrispondere ai parametri elencati nella [configurazione predefinita per i gateway VPN CloudSimple](#default-configuration-for-cloudsimple-vpn-gateways).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Configurare la mappa di crittografia

Configurare la mappa Crypto, che contiene i componenti seguenti:

* Indirizzo IP peer
* ACL definito che contiene il traffico di interesse
* Set di trasformazione

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Applicare la mappa di crittografia

Applicare la mappa di crittografia sull'interfaccia esterna:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Confermare le regole NAT applicabili

Di seguito è riportata la regola NAT usata. Verificare che il traffico VPN non sia soggetto ad altre regole NAT.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Esempio di output stabilito da VPN da sito a sito IPsec da Cisco ASA

Output fase 1:

![Output della fase 1 per il firewall Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Output fase 2:

![Output della fase 2 per il firewall Cisco ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Configurare il firewall di Palo Alto Networks locale

Le istruzioni riportate in questa sezione si applicano a Palo Alto Networks versione 7,1 e successive. In questo esempio di configurazione, la versione del software della serie VM Palo Alto Networks 8.1.0 viene distribuita e configurata in modalità IKEv1.

Per il funzionamento della VPN da sito a sito, è necessario consentire UDP 500/4500 e ESP (protocollo IP 50) dall'IP pubblico primario e secondario di CloudSimple (IP peer) nell'interfaccia esterna del gateway Palo Alto Networks locale.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Creare interfacce tunnel primarie e secondarie

Accedere a palo alto firewall, selezionare**interfacce** > di **rete** > **tunnel** > **Aggiungi**, configurare i campi seguenti e fare clic su **OK**.

* Nome dell'interfaccia. Il primo campo viene popolato automaticamente con la parola chiave ' tunnel '. Nel campo adiacente immettere un numero compreso tra 1 e 9999. Questa interfaccia verrà usata come interfaccia tunnel principale per il trasporto del traffico da sito a sito tra il Data Center locale e il cloud privato.
* Commento. Immettere i commenti per una semplice identificazione dello scopo del tunnel
* Profilo NetFlow. Lasciare il valore predefinito.
* Config. Assegna interfaccia a: Router virtuale: Selezionare **predefinito**. 
        Area di sicurezza: Selezionare la zona per il traffico LAN attendibile. In questo esempio, il nome della zona per il traffico LAN è "Trust".
* IPv4. Fare clic su **Aggiungi** e aggiungere eventuali indirizzi IP non sovrapposti non usati/32 nell'ambiente, che verranno assegnati all'interfaccia del tunnel primario e verranno usati per il monitoraggio dei tunnel (descritto più avanti).

Poiché questa configurazione è per una VPN a disponibilità elevata, sono necessarie due interfacce del tunnel: Uno primario e uno secondario. Ripetere i passaggi precedenti per creare l'interfaccia del tunnel secondario. Selezionare un ID tunnel diverso e un indirizzo IP non usato/32 diverso.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Configurare route statiche per le subnet del cloud privato da raggiungere tramite la VPN da sito a sito

Le route sono necessarie per le subnet locali per raggiungere le subnet del cloud privato CloudSimple.

Selezionare >  > **router virtuali di**rete le**Route**statiche > predefinite Aggiungi, configurare i campi seguenti e fare clic su **OK.**  > 

* Nome. Immettere un nome per identificare facilmente lo scopo della route.
* Destinazione. Specificare le subnet del cloud privato CloudSimple da raggiungere tramite le interfacce del tunnel S2S da locale
* Interfaccia. Selezionare l'interfaccia del tunnel primario creata nel passaggio 1 (sezione 2) nell'elenco a discesa. In questo esempio è tunnel. 20.
* Hop successivo. Selezionare **Nessuno**.
* Distanza amministrativa. Lasciare il valore predefinito.
* Metrica. Immettere un valore compreso tra 1 e 65535. La chiave consiste nell'immettere una metrica inferiore per la route corrispondente all'interfaccia del tunnel primario rispetto alla route corrispondente all'interfaccia del tunnel secondario che rende la route precedente preferita. Se il valore della metrica del tunnel. 20 è 20 anziché un valore della metrica pari a 30 per il tunnel. 30, è preferibile il tunnel. 20.
* Tabella di route. Lasciare il valore predefinito.
* Profilo BFD. Lasciare il valore predefinito.
* Monitoraggio del percorso. Lasciare deselezionato.

Ripetere i passaggi precedenti per creare un'altra route per le subnet del cloud privato da usare come route secondaria/di backup tramite l'interfaccia del tunnel secondario. Questa volta selezionare un ID tunnel diverso e una metrica più elevata rispetto alla route primaria.

### <a name="3-define-the-cryptographic-profile"></a>3. Definire il profilo crittografico

Definire un profilo crittografico che specifichi i protocolli e gli algoritmi per l'identificazione, l'autenticazione e la crittografia da usare per la configurazione di tunnel VPN in IKEv1 fase 1.

Selezionare **rete** > **espandere profili** > di rete IKE Crypto Aggiungi, configurare i campi seguenti e fare clic su OK. > 

* Nome. Immettere un nome per il profilo di crittografia IKE.
* Gruppo DH. Fare clic su **Aggiungi** e selezionare il gruppo DH appropriato.
* Crittografia. Fare clic su **Aggiungi** e selezionare il metodo di crittografia appropriato.
* L'autenticazione. Fare clic su **Aggiungi** e selezionare il metodo di autenticazione appropriato.
* Durata della chiave. Lasciare il valore predefinito.
* IKEv2 multiple authentication. Lasciare il valore predefinito.

### <a name="4-define-ike-gateways"></a>4. Definire i gateway IKE

Definire i gateway IKE per stabilire la comunicazione tra i peer attraverso ogni estremità del tunnel VPN.

Selezionare **rete** > **espandere profili**di > retegateway IKE Aggiungi, configurare i campi seguenti e fare clic su **OK.**  > 

Scheda Generale:

* Nome. Immettere il nome del gateway IKE a cui eseguire il peering con il peer VPN CloudSimple primario.
* Versione. Selezionare la **modalità solo IKEv1**.
* Tipo di indirizzo. Selezionare **IPv4**.
* Interfaccia. Selezionare l'interfaccia pubblica o esterna.
* Indirizzo IP locale. Lasciare il valore predefinito.
* Tipo di indirizzo IP peer. Selezionare **IP**.
* Indirizzo peer. Immettere l'indirizzo IP del peer VPN CloudSimple primario.
* L'autenticazione. Selezionare la **chiave precondivisa**.
* Chiave pre-condivisa/conferma chiave precondivisa. Immettere la chiave precondivisa in modo che corrisponda alla chiave del gateway VPN CloudSimple.
* Identificazione locale. Immettere l'indirizzo IP pubblico del palo alto firewall locale.
* Identificazione peer. Immettere l'indirizzo IP del peer VPN CloudSimple primario.

Scheda Opzioni avanzate:

* Abilitare la modalità passiva. Lasciare deselezionato.
* Abilitare l'attraversamento NAT. Lasciare deselezionata l'opzione se l'istanza locale di palo alto firewall non è dietro un dispositivo NAT. In caso contrario, selezionare la casella di controllo.

IKEv1

* Modalità Exchange. Selezionare **principale**.
* Profilo di crittografia IKE. Selezionare il profilo di crittografia IKE creato in precedenza. Lasciare deselezionata la casella Abilita frammentazione.
* Rilevamento peer inattivo. Lasciare deselezionata la casella.

Ripetere i passaggi precedenti per creare il gateway IKE secondario.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definire i profili di crittografia IPSEC

Selezionare **rete** > **espandere profili** > di rete IPSec crittografia Aggiungi, configurare i campi seguenti e fare clic su OK. > 

* Nome. Immettere un nome per il profilo di crittografia IPsec.
* Protocollo IPsec. Selezionare **ESP**.
* Crittografia. Fare clic su **Aggiungi** e selezionare il metodo di crittografia appropriato.
* L'autenticazione. Fare clic su **Aggiungi** e selezionare il metodo di autenticazione appropriato.
* Gruppo DH. Selezionare **No-PFS**.
* Vita. Impostato su 30 minuti.
* Abilita. Lasciare deselezionata la casella.

Ripetere i passaggi precedenti per creare un altro profilo di crittografia IPsec, che verrà usato come peer VPN CloudSimple secondario. Lo stesso profilo di crittografia IPSEC può essere usato anche per i tunnel IPsec primari e secondari (vedere la procedura seguente).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Definire i profili di monitoraggio per il monitoraggio del tunnel

Selezionare **rete** > **espandere** > **monitoraggio**profili di rete Aggiungi, configurare i campi seguenti e fare clic su OK. > 

* Nome. Immettere un nome del profilo di monitoraggio da usare per il monitoraggio del tunnel per la reazione proattiva all'errore.
* Azione. Selezionare **failover**.
* Intervallo. Immettere il valore **3**.
* Soglia. Immettere il valore **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Configurare i tunnel IPsec primari e secondari.

Selezionare > i**tunnel**IPSec di rete Aggiungi, configurare i campi seguenti e fare clic su OK. > 

Scheda Generale:

* Nome. Immettere un nome per il tunnel IPSEC primario a cui eseguire il peering con il peer VPN CloudSimple primario.
* Interfaccia tunnel. Selezionare l'interfaccia del tunnel primario.
* Tipo. Lasciare il valore predefinito.
* Tipo di indirizzo. Selezionare **IPv4**.
* Gateway IKE. Selezionare il gateway IKE primario.
* Profilo di crittografia IPsec. Selezionare il profilo IPsec primario. Selezionare **Mostra opzioni avanzate**.
* Abilitare la protezione della riproduzione. Lasciare il valore predefinito.
* Copiare l'intestazione TOS. Lasciare deselezionata la casella.
* Monitoraggio tunneling. Selezionare la casella.
* IP di destinazione. Immettere qualsiasi indirizzo IP appartenente alla subnet del cloud privato CloudSimple consentita tramite la connessione da sito a sito. Verificare che le interfacce del tunnel, ad esempio tunnel. 20-10.64.5.2/32 e tunnel. 30-10.64.6.2/32, in palo alto, siano autorizzate a raggiungere l'indirizzo IP del cloud privato CloudSimple tramite la VPN da sito a sito. Per gli ID proxy, vedere la configurazione seguente.
* Profilo. Selezionare il profilo di monitoraggio.

Scheda ID proxy: Fare clic su **IPv4** > **Aggiungi** e configurare quanto segue:

* ID proxy. Immettere un nome per il traffico interessante. Potrebbero essere presenti più ID proxy trasportati all'interno di un tunnel IPsec.
* Local. Specificare le subnet locali che possono comunicare con subnet del cloud privato tramite la VPN da sito a sito.
* Remoto. Specificare le subnet remote del cloud privato per le quali è consentita la comunicazione con le subnet locali.
* Protocollo. Selezionare **any**.

Ripetere i passaggi precedenti per creare un altro tunnel IPsec da usare per il peer VPN CloudSimple secondario.

## <a name="references"></a>Riferimenti

Configurazione di NAT in Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Guida alla configurazione della serie Cisco ASA 5500</a>

Attributi IKEv1 e IKEv2 supportati in Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Guida alla configurazione dell'interfaccia della riga di comando serie Cisco ASA</a>

Configurazione della VPN da sito a sito IPsec in Cisco ASA con la versione 8,4 e successive:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Configurare i tunnel da sito a sito IPsec IKEv1 con ASDM o l'interfaccia della riga di comando in ASA</a>

Configurazione di Cisco Adaptive Security Appliance Virtual (ASAv) in Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Appliance virtuale Cisco Adaptive Security (ASAv) Guida introduttiva</a>

Configurazione della VPN da sito a sito con ID proxy in palo alto:

<a href="https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/vpns/set-up-Site-to-Site-vpn.html#" target="_blank">Configurare una VPN da sito a sito</a>

Impostazione del monitoraggio tunnel:

<a href="https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html" target="_blank">Configurare il monitoraggio del tunnel</a>

Operazioni del tunnel del gateway IKE o IPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/vpns/set-up-Site-to-Site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel.html#" target="_blank">Abilitare/disabilitare, aggiornare o riavviare un gateway IKE o un tunnel IPsec</a>
