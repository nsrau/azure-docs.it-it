---
title: 'Soluzione Azure VMware di CloudSimple : Configurare la disponibilità elevata dal gateway VPN locale a quello CloudSimple'
description: Descrive come configurare una connessione a disponibilità elevata dall'ambiente locale a un gateway VPN CloudSimple abilitato per la disponibilità elevata
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025266"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Configurare una connessione a disponibilità elevata dal gateway VPN locale al gateway VPN CloudSimple a high availability connection from on-premises to CloudSimple VPN gateway

Gli amministratori di rete possono configurare una connessione VPN IPsec a disponibilità elevata dall'ambiente locale a un gateway VPN CloudSimple.Network administrators can configure a high availability IPsec Site-to-Site VPN connection from their on-premises environment to a CloudSimple VPN gateway.

In questa guida vengono illustrati i passaggi per configurare un firewall locale per una connessione a disponibilità elevata VPN IPsec da sito a sito. I passaggi dettagliati sono specifici per il tipo di firewall locale. Ad esempio, questa guida presenta i passaggi per due tipi di firewall: Cisco ASA e Palo Alto Networks.

## <a name="before-you-begin"></a>Prima di iniziare

Completare le attività seguenti prima di configurare il firewall locale.

1. Verificare che l'organizzazione abbia eseguito il provisioning dei nodi necessari e abbia creato almeno un cloud privato CloudSimple.Verify that your organization has [provisioned](create-nodes.md) the required nodes and created at at least one CloudSimple Private Cloud.
2. [Configurare un gateway VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) da sito a sito tra la rete locale e il cloud privato CloudSimple.

Vedere [Panoramica dei gateway VPN](cloudsimple-vpn-gateways.md) per le proposte di fase 1 e fase 2 supportate.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Configurare il firewall Cisco ASA locale

Le istruzioni contenute in questa sezione si applicano a Cisco ASA versione 8.4 e successive. Nell'esempio di configurazione, Cisco Adaptive Security Appliance Software Version 9.10 viene distribuito e configurato in modalità IKEv1.

Affinché la VPN da sito a sito funzioni, è necessario consentire UDP 500/4500 ed ESP (protocollo IP 50) dall'IP pubblico (peer IP) primario e secondario CloudSimple sull'interfaccia esterna del gateway VPN Cisco ASA locale.

### <a name="1-configure-phase-1-ikev1"></a>1. Configurare la fase 1 (IKEv1)

Per abilitare la fase 1 (IKEv1) sull'interfaccia esterna, immettere il seguente comando CLI nel firewall Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Creare un criterio IKEv1

Creare un criterio IKEv1 che definisca gli algoritmi e i metodi da utilizzare per l'hashing, l'autenticazione, il gruppo Diffie-Hellman, la durata e la crittografia.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Creare un gruppo di tunnel

Creare un gruppo di tunnel con gli attributi IPsec. Configurare l'indirizzo IP peer e la chiave precondivisa del tunnel, impostata durante la configurazione del gateway VPN da [sito a sito.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Configurare la fase 2 (IPsec)

Per configurare la fase 2 (IPsec), creare un elenco di controllo di accesso (ACL) che definisca il traffico da crittografare e eseguire il tunneling. Nell'esempio seguente, il traffico di interesse proviene dal tunnel proveniente dalla subnet locale locale (10.16.1.0/24) alla subnet remota del cloud privato (192.168.0.0/24). L'ACL può contenere più voci se sono presenti più subnet tra i siti.

Nelle versioni 8.4 e successive di Cisco ASA è possibile creare oggetti o gruppi di oggetti che fungono da contenitori per reti, subnet, indirizzi IP host o più oggetti. Creare un oggetto per il locale e un oggetto per le subnet remote e utilizzarli per l'ACL crittografico e le istruzioni NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Definire una subnet locale locale come oggettoDefine an on-premises local subnet as an object

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Definire la subnet remota CloudSimple come oggettoDefine the CloudSimple remote subnet as an object

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Configurare un elenco di accesso per il traffico di interesse

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Configurare il set di trasformazioni

Configurare il set di trasformazioni (TS), che deve includere la parola chiave ```ikev1```. Gli attributi di crittografia e hash specificati nel Servizio terminal devono corrispondere ai parametri elencati in [Configurazione predefinita per i gateway VPN CloudSimple](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Configurare la mappa crittografica

Configurare la mappa crittografica, che contiene questi componenti:

* Indirizzo IP peer
* ACL definito che contiene il traffico di interesse
* Set di trasformazioni

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Applicare la mappa crittografica

Applicare la mappa crittografica sull'interfaccia esterna:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Confermare le regole NAT applicabili

Di seguito è riportata la regola NAT utilizzata. Assicurarsi che il traffico VPN non sia soggetto a qualsiasi altra regola NAT.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Esempio di VPN da sito a sito IPsec stabilita dall'output da Cisco ASA

Uscita fase 1:

![Output della fase 1 per il firewall Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Uscita fase 2:

![Uscita di fase 2 per il firewall Cisco ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Configurare il firewall delle reti Palo Alto locale

Le istruzioni riportate in questa sezione si applicano a Palo Alto Networks versione 7.1 e successive. In questo esempio di configurazione, Palo Alto Networks VM-Series Software Version 8.1.0 viene distribuito e configurato in modalità IKEv1.In this configuration example, Palo Alto Networks VM-Series Software Version 8.1.0 is deployed and configured in IKEv1 mode.

Affinché la VPN da sito a sito funzioni, è necessario consentire UDP 500/4500 ed ESP (ip protocol 50) dall'IP pubblico (peer IP) primario e secondario CloudSimple sull'interfaccia esterna del gateway Palo Alto Networks locale.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Creare interfacce tunnel primarie e secondarie

Accedere al firewall Palo Alto, selezionare**Aggiunta tunnel** > **interfacce** > di **rete** > **,** configurare i seguenti campi e fare clic su **OK**.

* Nome interfaccia. Il primo campo viene compilato automaticamente con la parola chiave 'tunnel'. Nel campo adiacente immettere un numero compreso tra 1 e 9999. Questa interfaccia verrà utilizzata come interfaccia tunnel principale per trasportare il traffico da sito a sito tra il data center locale e il cloud privato.
* Commento. Inserire i commenti per facilitare l'identificazione dello scopo del tunnel
* Netflow Profilo. Lasciare il valore predefinito.
* Conﬁg. Assegna interfaccia a: Router virtuale: selezionare **default**. 
        Area di sicurezza: selezionare l'area per il traffico LAN attendibile. In questo esempio, il nome della zona per il traffico LAN è "Trust".
* IPv4. Fare clic su **Aggiungi** e aggiungere qualsiasi indirizzo IP /32 inutilizzato non sovrapposto nell'ambiente, che verrà assegnato all'interfaccia del tunnel primario e verrà utilizzato per il monitoraggio dei tunnel (illustrato più avanti).

Poiché questa configurazione è per una VPN a disponibilità elevata, sono necessarie due interfacce tunnel: una primaria e una secondaria. Ripetere i passaggi precedenti per creare l'interfaccia del tunnel secondario. Selezionare un ID tunnel diverso e un indirizzo IP /32 inutilizzato diverso.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Configurare route statiche per le subnet del cloud privato da raggiungere tramite la VPN da sito a sito

Le route sono necessarie affinché le subnet locali raggiungano le subnet del cloud privato CloudSimple.Routes are necessary for the on-premises subnets to reach CloudSimple private cloud subnets.

Selezionare Route**statiche** > **dei router** > virtuali di **rete** > *predefinite* > **Aggiungi**, configurare i seguenti campi e fare clic su **OK**.

* Name. Immettere un nome per facilitare l'identificazione dello scopo del percorso.
* Destinazione. Specificare le subnet del cloud privato CloudSimple da raggiungere tramite le interfacce del tunnel S2S dall'ambiente localeSpecify the CloudSimple private cloud subnets to be reached over S2S tunnel interfaces from on-premises
* Interfaccia. Selezionare l'interfaccia del tunnel primario creata nel passaggio 1 (Sezione-2) dall'elenco a discesa. In questo esempio, è tunnel.20.In this example, it is tunnel.20.
* Hop successivo. Selezionare **Nessuno**.
* Distanza dell'amministratore. Lasciare il valore predefinito.
* Metrica. Immettere un valore compreso tra 1 e 65535. La chiave consiste nell'immettere una metrica inferiore per la route corrispondente all'interfaccia del tunnel primario rispetto all'interfaccia del tunnel secondario corrispondente alla route che rende la route precedente preferita. Se tunnel.20 ha un valore metrico di 20 rispetto a un valore metrico di 30 per tunnel.30, è preferibile il tunnel.20.
* Tabella del percorso. Lasciare il valore predefinito.
* profilo BFD. Lasciare il valore predefinito.
* Monitoraggio del percorso. lasciare la casella deselezionata.

Ripetere i passaggi precedenti per creare un'altra route per le subnet del cloud privato da utilizzare come route secondaria/di backup tramite l'interfaccia del tunnel secondario. Questa volta, selezionare UN ID tunnel diverso e una metrica superiore rispetto alla route primaria.

### <a name="3-define-the-cryptographic-profile"></a>3. Definire il profilo crittografico

Definire un profilo di crittografia che specifichi i protocolli e gli algoritmi per l'identificazione, l'autenticazione e la crittografia da utilizzare per la configurazione di tunnel VPN nella fase 1 di IKEv1.

Selezionare **Rete** > **Espandi profili** > di rete**IKE Crypto** > **Aggiungi**, configurare i seguenti campi e fare clic su **OK**.

* Name. Immettere un nome qualsiasi del profilo crittografico IKE.
* gruppo DH. Fare clic su **Aggiungi** e selezionare il gruppo DH appropriato.
* Crittografia. Fare clic su **Aggiungi** e selezionare il metodo di crittografia appropriato.
* L'autenticazione. Fare clic su **Aggiungi** e selezionare il metodo di autenticazione appropriato.
* Durata della chiave. Lasciare il valore predefinito.
* Autenticazione IKEv2 Multiplo. Lasciare il valore predefinito.

### <a name="4-define-ike-gateways"></a>4. Definire i gateway IKE

Definire i gateway IKE per stabilire la comunicazione tra i peer attraverso ogni estremità del tunnel VPN.

Selezionare **Rete** > **Espandi profili** > di rete**Gateway** > IKE**Aggiungi**, configurare i seguenti campi e fare clic su **OK**.

Scheda Generale:

* Name. Immettere il nome del gateway IKE di cui eseguire il peer CloudSimple con il peer VPN CloudSimple primario.
* Versione. Selezionare La **modalità Solo IKEv1**.
* Tipo di indirizzo. Selezionare **IPv4**.
* Interfaccia. Selezionare l'interfaccia pubblica rivolta o esterna.
* Indirizzo IP locale. Lasciare il valore predefinito.
* Tipo di indirizzo IP peer. Selezionare **IP**.
* Indirizzo peer. Immettere l'indirizzo IP peer VPN CloudSimple primario.
* L'autenticazione. Selezionare **Chiave precondivisa**.
* Chiave precondivisa/ Conferma chiave precondivisa. Immettere la chiave precondivisa in modo che corrisponda alla chiave del gateway VPN CloudSimple.
* Identificazione locale. Immettere l'indirizzo IP pubblico del firewall Palo Alto locale.
* Identificazione peer. Immettere l'indirizzo IP peer VPN CloudSimple primario.

Scheda Opzioni avanzate:

* Abilitare la modalità passiva. lasciare la casella deselezionata.
* Abilitare NAT Traversal. Lasciare deselezionato se il firewall Palo Alto locale non è protetto da qualsiasi dispositivo NAT. In caso contrario, selezionare la casella di controllo.

IKEv1:

* Modalità Exchange. Selezionare **principale**.
* Profilo Di crittografia IKE. Selezionare il profilo IKE Crypto creato in precedenza. Lasciare deselezionata la casella Abilita frammentazione.
* Rilevamento peer morto. Lasciare la casella deselezionata.

Ripetere i passaggi precedenti per creare il gateway IKE secondario.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definire i profili di crittografia IPSEC

Selezionare Rete**Espandi profili di** >  **rete** > Profili di rete**IpSEC Crypto** > **Aggiungi**, configurare i seguenti campi e fare clic su **OK**.

* Name. Immettere un nome per il profilo crittografico IPsec.
* Protocollo IPsec. Selezionare **ESP**.
* Crittografia. Fare clic su **Aggiungi** e selezionare il metodo di crittografia appropriato.
* L'autenticazione. Fare clic su **Aggiungi** e selezionare il metodo di autenticazione appropriato.
* gruppo DH. Selezionare **no-pfs**.
* Vita. Impostare come 30 minuti.
* Abilita. Lasciare la casella deselezionata.

Ripetere i passaggi precedenti per creare un altro profilo crittografico IPsec, che verrà utilizzato come peer VPN CloudSimple secondario. Lo stesso profilo di crittografia IPSEC può essere utilizzato anche per i tunnel IPsec primario e secondario (vedere la procedura seguente).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Definire i profili di monitor per il monitoraggio del tunnel

Selezionare **Network** > **Expand Network Expand Network Profiles** > **Monitor** > **Add**, configurare i seguenti campi e fare clic su **OK**.

* Name. Immettere un nome qualsiasi del profilo Monitor da utilizzare per il monitoraggio del tunnel per una reazione proattiva all'errore.
* Azione. Selezionare **Fail Over**.
* Intervallo. Immettere il valore **3**.
* Soglia. Immettere il valore **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Impostare tunnel IPsec primari e secondari.

Selezionare **Tunnel** > **IPsec di** > rete**Aggiungi**, configurare i seguenti campi e fare clic su **OK**.

Scheda Generale:

* Name. Immettere un nome per il tunnel IPSEC primario di cui eseguire il peer di peer CloudSimple con peer VPN CloudSimple primario.
* Interfaccia tunnel. Selezionare l'interfaccia del tunnel primario.
* Digitare  Lasciare il valore predefinito.
* Tipo di indirizzo. Selezionare **IPv4**.
* Gateway IKE. Selezionare il gateway IKE primario.
* Profilo di crittografia IPsec. Selezionare il profilo IPsec primario. Selezionare **Mostra opzioni avanzate**.
* Abilitare la protezione da riproduzione. Lasciare il valore predefinito.
* Copia intestazione TOS. Lasciare la casella deselezionata.
* Tunnel Monitor. Seleziona la casella.
* IP di destinazione. Immettere qualsiasi indirizzo IP appartenente alla subnet CloudSimple Private Cloud consentito tramite la connessione da sito a sito. Assicurarsi che le interfacce del tunnel (ad esempio tunnel.20 - 10.64.5.2/32 e tunnel.30 - 10.64.6.2/32) su Palo Alto siano autorizzate a raggiungere l'indirizzo IP CloudSimple Private Cloud tramite la VPN da sito a sito. Vedere la configurazione seguente per gli ID proxy.
* Profilo. Selezionare il profilo del monitor.

Scheda Url proxy: fare clic su **IPv4** > **Aggiungi** e configura quanto segue:

* ID proxy. Immettere un nome per il traffico interessante. Potrebbero essere presenti più ID proxy trasportati all'interno di un tunnel IPsec.
* Local. Specificare le subnet locali locali a cui è consentito comunicare con le subnet del cloud privato tramite la VPN da sito a sito.
* Remoto. Specificare le subnet remote del cloud privato che possono comunicare con le subnet locali.
* Protocollo. Selezionare **un file**.

Ripetere i passaggi precedenti per creare un altro tunnel IPsec da utilizzare per il peer VPN CloudSimple secondario.

## <a name="references"></a>Riferimenti

Configurazione di NAT su Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Guida alla configurazione della serie Cisco ASA 5500</a>

Attributi IKEv1 e IKEv2 supportati in Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Guida alla configurazione di Cisco ASA Series CLI</a>

Configurazione della VPN da sito a sito IPsec su Cisco ASA con la versione 8.4 e successive:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Configurare i tunnel da sito a sito IKEv1 IPsec con ASDM o CLI nell'ASA</a>

Configurazione di Cisco Adaptive Security Appliance virtual (ASAv) in Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Guida introduttiva di Cisco Adaptive Security Virtual Appliance (ASAv)</a>

Configurazione della VPN da sito a sito con gli URL proxy su Palo Alto:

[Configurare la VPN da sito a sito](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Configurazione del monitor del tunnel:

[Impostare il monitoraggio del tunnel](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Gateway IKE o operazioni tunnel IPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Abilitare/disabilitare, aggiornare o riavviare un gateway IKE o un tunnel IPsec</a>
