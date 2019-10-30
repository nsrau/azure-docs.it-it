---
title: Soluzione VMware di Azure di CloudSimple-estendere una rete di livello 2 da locale a cloud privato
description: Viene descritto come configurare una VPN di livello 2 tra NSX-T in un cloud privato di CloudSimple e un client periferico di NSX autonomo locale
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 37f337f158c3ca53170d963f1304801a12b732da
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053896"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Eseguire la migrazione di carichi di lavoro usando reti estese di livello 2

In questa guida si apprenderà come usare la VPN di livello 2 (L2VPN) per estendere una rete di livello 2 dall'ambiente locale al cloud privato CloudSimple. Questa soluzione consente la migrazione dei carichi di lavoro in esecuzione nell'ambiente VMware locale al cloud privato in Azure nello stesso spazio di indirizzi della subnet senza dover reindirizzare i carichi di lavoro.

L'estensione basata su L2VPN di reti di livello 2 può funzionare con o senza reti basate su NSX nell'ambiente VMware locale. Se non si dispone di reti basate su NSX per i carichi di lavoro in locale, è possibile usare un gateway di servizi perimetrali NSX autonomo.

> [!NOTE]
> Questa guida illustra lo scenario in cui i data center locali e del cloud privato sono connessi tramite VPN da sito a sito.

## <a name="deployment-scenario"></a>Scenario di distribuzione

Per estendere la rete locale usando L2VPN, è necessario configurare un server L2VPN (destinazione NSX-T tier0 router) e un client L2VPN (source Standalone Client).  

In questo scenario di distribuzione, il cloud privato è connesso all'ambiente locale tramite un tunnel VPN da sito a sito che consente la gestione locale e le subnet vMotion per comunicare con le subnet di gestione del cloud privato e vMotion. Questa disposizione è necessaria per Cross vCenter vMotion (xVC-vMotion). Un router NSX-T tier0 viene distribuito come server L2VPN nel cloud privato.

Il dispositivo perimetrale NSX autonomo viene distribuito nell'ambiente locale come client L2VPN e successivamente associato al server L2VPN. Un endpoint del tunnel GRE viene creato su ogni lato e configurato per "estendere" la rete locale di livello 2 al cloud privato. Questa configurazione è illustrata nella figura seguente.

![Scenario di distribuzione](media/l2vpn-deployment-scenario.png)

Per ulteriori informazioni sulla migrazione mediante la VPN L2, vedere la pagina relativa alle [reti private virtuali](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) nella documentazione di VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Prerequisiti per la distribuzione della soluzione

Prima di distribuire e configurare la soluzione, verificare che siano presenti gli elementi seguenti:

* La versione di vSphere locale è 6.7 U1 + o 6.5 P03 +.
* La licenza di vSphere locale è al livello Enterprise Plus (per vSphere Distributed Switch).
* Identificare la rete del carico di lavoro di livello 2 da estendere al cloud privato.
* Identificare una rete di livello 2 nell'ambiente locale per la distribuzione del dispositivo client L2VPN.
* [Un cloud privato è già stato creato](create-private-cloud.md).
* La versione del dispositivo autonomo NSX-T Edge è compatibile con la versione NSX-T Manager (NSX-t 2.3.0) usata nell'ambiente cloud privato.
* In vCenter locale è stato creato un gruppo di porte trunk con trasmissioni falsificate abilitate.
* Un indirizzo IP pubblico è stato riservato da usare per l'indirizzo IP del client autonomo NSX-T e 1:1 NAT per la conversione tra i due indirizzi.
* L'invio DNS viene impostato nei server DNS locali per il dominio az.cloudsimple.io in modo che punti ai server DNS del cloud privato.
* La latenza RTT è inferiore o uguale a 150 ms, come richiesto per vMotion per lavorare nei due siti.

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

La tabella seguente elenca le versioni di vSphere supportate e i tipi di adattatore di rete.  

| versione di vSphere | Tipo di vSwitch di origine | Driver NIC virtuale | Tipo di vSwitch di destinazione | Supportato? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Tutto | DVS | Tutto | DVS | SÌ |
| vSphere 6.7 UI o versione successiva, 6.5 P03 o versione successiva | DVS | VMXNET3 | N-VDS | SÌ |
| vSphere 6.7 UI o versione successiva, 6.5 P03 o versione successiva | DVS | E1000 | N-VDS | [Non supportato per VWware](https://kb.vmware.com/s/article/56991) |
| interfaccia utente vSphere 6.7 o 6.5 P03, NSX-V o versioni inferiori a NSX-T 2.2, 6.5 P03 o versione successiva | Tutto | Tutto | N-VDS | [Non supportato per VWware](https://kb.vmware.com/s/article/56991) |

A partire dalla versione VMware NSX-T 2,3:

* Il Commuter logico sul lato cloud privato che si estende in locale tramite L2VPN non può essere instradato nello stesso momento. Il Commuter logico esteso non può essere connesso a un router logico.
* L2VPN e le VPN IPSEC basate su route possono essere configurate solo tramite chiamate API.

Per ulteriori informazioni, vedere [reti private virtuali](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) nella documentazione di VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Esempio di indirizzamento della distribuzione VPN L2

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Rete locale in cui è distribuito il ESG autonomo (client VPN L2)

| **Elemento** | **Valore** |
|------------|-----------------|
| Nome di rete | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| Indirizzo IP dell'appliance Edge autonomo | 10.250.0.111 |
| Indirizzo IP NAT dell'appliance Edge autonomo | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Rete locale da estendere

| **Elemento** | **Valore** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Schema IP del cloud privato per il router NSX-T tier0 (per la VPN L2)

| **Elemento** | **Valore** |
|------------|-----------------|
| Interfaccia loopback | 192.168.254.254/32 |
| Interfaccia tunnel | 5.5.5.1/29 |
| Commuter logico (esteso) | Stretch_LS |
| Interfaccia loopback (indirizzo IP NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Rete cloud privata da mappare alla rete estesa

| **Elemento** | **Valore** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Recuperare l'ID del router logico necessario per L2VPN

Nei passaggi seguenti viene illustrato come recuperare l'ID del router logico dell'istanza di tier0 di ripristino di emergenza logica per i servizi IPsec e L2VPN. L'ID del router logico è necessario in seguito durante l'implementazione di L2VPN.

1. Accedere a NSX-T Manager https://*NSX-t-Manager-IP-address* e selezionare **Network** > **routers** > **provider-LR** > **Overview**. Per la **modalità a disponibilità elevata**, selezionare **attivo-standby**. Questa azione apre una finestra popup che mostra la macchina virtuale perimetrale in cui è attualmente attivo il router tier0.

    ![Selezionare attivo-standby](media/l2vpn-fetch01.png)

2. Selezionare **infrastruttura** > **nodi** > **bordi**. Prendere nota dell'indirizzo IP di gestione della macchina virtuale perimetrale attiva (Edge VM1) identificato nel passaggio precedente.

    ![Nota IP di gestione](media/l2vpn-fetch02.png)

3. Aprire una sessione SSH per l'indirizzo IP di gestione della macchina virtuale perimetrale. Eseguire il comando ```get logical-router``` con username **admin** e password **CloudSimple 123!** .

    ![ottenere l'output del router logico](media/l2vpn-fetch03.png)

4. Se non viene visualizzata una voce "DR-provider-LR", completare i passaggi seguenti.

5. Creare due commutatori logici con supporto sovrapposto. Un compartitore logico viene esteso in locale in cui risiedono i carichi di lavoro migrati. Un altro commutire logico è un'opzione fittizia. Per istruzioni, vedere [creare un Commutire logico](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) nella documentazione di VMware.

    ![Crea commutire logico](media/l2vpn-fetch04.png)

6. Collegare il compassatore fittizio al router Tier1 con un indirizzo IP locale collegamento o qualsiasi subnet non sovrapposta da locale o dal cloud privato. Vedere [aggiungere una porta di collegamento in un router logico di livello 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) nella documentazione di VMware.

    ![Connetti opzione fittizia](media/l2vpn-fetch05.png)

7. Eseguire di nuovo il comando `get logical-router` nella sessione SSH della macchina virtuale perimetrale. Viene visualizzato l'UUID del router logico "DR-provider-LR". Prendere nota dell'UUID, che è necessario quando si configura L2VPN.

    ![ottenere l'output del router logico](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Recuperare l'ID del comcambio logico necessario per L2VPN

1. Accedere a [NSX-T Manager](https://nsx-t-manager-ip-address).
2. Selezionare **rete** > **cambiare** > **Opzioni** >  **< commutatore \Logical\>** **Panoramica** > .
3. Prendere nota dell'UUID del Commuter logico stretch, che è necessario quando si configura L2VPN.

    ![ottenere l'output del router logico](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Considerazioni sulla sicurezza e sul routing per L2VPN

Per stabilire una VPN basata su Route IPsec tra il router NSX-T tier0 e il client perimetrale NSX autonomo, l'interfaccia di loopback del router NSX-T tier0 deve essere in grado di comunicare con l'indirizzo IP pubblico del client autonomo NSX in locale tramite UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Consenti UDP 500/4500 per IPsec

1. [Creare un indirizzo IP pubblico](public-ips.md) per l'interfaccia di loopback NSX-T tier0 nel portale di CloudSimple.

2. [Creare una tabella del firewall](firewall.md) con regole con stato che consentono il traffico in ingresso UDP 500/4500 e allegare la tabella del firewall alla subnet NSX-T HostTransport.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Annunciare l'IP dell'interfaccia loopback alla rete sottostante

1. Creare una route null per la rete dell'interfaccia di loopback. Accedere a NSX-T Manager e selezionare **rete** > **routing** > **router** > **Provider-LR** > **routing** >  route**statiche**. Fare clic su **Aggiungi**. Per **rete**, immettere l'indirizzo IP dell'interfaccia di loopback. Per gli hop **successivi**, fare clic su **Aggiungi**, specificare ' null ' per l'hop successivo e il valore predefinito è 1 per distanza amministratore.

    ![Aggiungi route statica](media/l2vpn-routing-security01.png)

2. Creare un elenco di prefisso IP. Accedere a NSX-T Manager e selezionare **rete** > **routing** > **router** > **provider-LR** > **routing**degli**elenchi di prefisso IP** > . Fare clic su **Aggiungi**. Immettere un nome per identificare l'elenco. Per i **prefissi**, fare clic su **Aggiungi** due volte. Nella prima riga immettere ' 0.0.0.0/0' per la **rete** è Deny ' per l' **azione**. Nella seconda riga selezionare **any** per **rete** e **Consenti** **azione**.
3. Alleghi l'elenco di prefisso IP a entrambi i router BGP (TOR). Il collegamento dell'elenco di prefisso IP al router adiacente BGP impedisce che la route predefinita venga annunciata in BGP ai commutatori TOR. Tuttavia, qualsiasi altra route che includa la route null annuncia l'indirizzo IP dell'interfaccia loopback ai commutatori TOR.

    ![Crea elenco di prefisso IP](media/l2vpn-routing-security02.png)

4. Accedere a NSX-T Manager e selezionare **rete** > **routing** > **router** > **Provider-LR** > **routing** > **BGP**1**neighbors**. Selezionare il primo elemento adiacente. Fare clic su **modifica** > **Indirizzo famiglie**. Per la famiglia IPv4, modificare la colonna **filtro di uscita** e selezionare l'elenco di prefisso IP creato. Fare clic su **Salva** Ripetere questo passaggio per il secondo router adiacente.

    ![alleghi l'elenco di prefisso IP 1](media/l2vpn-routing-security03.png) ![alleghino l'elenco di prefisso IP 2](media/l2vpn-routing-security04.png)

5. Ridistribuire la route statica null in BGP. Per annunciare la route dell'interfaccia di loopback al sottoposto, è necessario ridistribuire la route statica null in BGP. Accedere a NSX-T Manager e selezionare **rete** > **routing** > **router** > **Provider-LR** >  routing  > **routing** **ridistribuzione**1 router**adiacenti**. Selezionare **provider-LR-Route_Redistribution** e fare clic su **modifica**. Selezionare la casella di controllo **static** e fare clic su **Salva**.

    ![Ridistribuire la route statica null in BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Configurare una VPN basata su route nel router NSX-T tier0

Usare il modello seguente per compilare tutti i dettagli per la configurazione di una VPN basata su route nel router NSX-T tier0. Gli UUID in ogni chiamata POST sono necessari nelle successive chiamate POST. Gli indirizzi IP per le interfacce di loopback e tunnel per L2VPN devono essere univoci e non sovrapporsi alle reti cloud locali o private.

Gli indirizzi IP scelti per il loopback e l'interfaccia del tunnel usati per L2VPN devono essere univoci e non sovrapporsi alle reti cloud locali o private. La rete dell'interfaccia di loopback deve sempre essere/32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Per tutte le chiamate API seguenti, sostituire l'indirizzo IP con l'indirizzo IP della gestione NSX-T. È possibile eseguire tutte queste chiamate API dal client di POST o usando i comandi `curl`.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Abilitare il servizio VPN IPSec sul router logico

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Creazione di profili: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Crea profili: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Creare profili: tunnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Creare un endpoint locale

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Creare un endpoint peer

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Creare una sessione VPN basata su Route

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Configurare L2VPN in NSX-T tier0 router

Immettere le informazioni seguenti dopo ogni chiamata POST. Gli ID sono necessari nelle successive chiamate POST.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Creare il servizio L2VPN

L'output del comando GET seguente sarà vuoto, perché la configurazione non è ancora stata completata.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Per il seguente comando POST, l'ID del router logico è l'UUID del router logico di ripristino di emergenza tier0 ottenuto in precedenza.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Creare la sessione L2VPN

Per il seguente comando POST, l'ID del servizio L2VPN è l'ID appena ottenuto e l'ID della sessione VPN IPsec è l'ID ottenuto nella sezione precedente.

``` 
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Queste chiamate creano un endpoint del tunnel GRE. Per controllare lo stato, eseguire il comando seguente.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Crea porta logica con l'ID tunnel specificato

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Ottenere il codice peer per L2VPN sul lato NSX-T

Ottenere il codice peer dell'endpoint NSX-T. Il codice peer è obbligatorio quando si configura l'endpoint remoto. Il > L2VPN < Session-ID può essere ottenuto dalla sezione precedente. Per ulteriori informazioni, vedere la [Guida all'API NSX-T 2,3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Distribuire il client autonomo NSX-T (locale)

Prima di distribuire, verificare che le regole del firewall locale consentano il traffico UDP 500/4500 in ingresso e in uscita da/verso l'indirizzo IP pubblico di CloudSimple riservato in precedenza per l'interfaccia di loopback del router NSX-T T0. 

1. [Scaricare il client perimetrale NSX autonomo](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF ed Estrai i file dal bundle scaricato in una cartella.

    ![Scaricare autonomo NSX Edge client](media/l2vpn-deploy-client01.png)

2. Passare alla cartella con tutti i file estratti. Selezionare tutti i file VMDK (NSX-I2t-client-large. MF e NSX-l2t-client-large. ovf per le dimensioni dell'appliance di grandi dimensioni o NSX-I2t-client-XLarge. MF e NSX-l2t-client-Xlarge. ovf per le dimensioni del dispositivo con dimensioni molto elevate). Fare clic su **Next** (Avanti).

    ![selezionare](media/l2vpn-deploy-client02.png) modello ![selezionare il modello](media/l2vpn-deploy-client03.png)

3. Immettere un nome per il client autonomo NSX-T e fare clic su **Avanti**.

    ![Immettere il nome del modello](media/l2vpn-deploy-client04.png)

4. Fare clic su **Avanti** secondo le necessità per raggiungere le impostazioni dell'archivio dati. Selezionare l'archivio dati appropriato per il client autonomo NSX-T e fare clic su **Avanti**.

    ![Seleziona archivio dati](media/l2vpn-deploy-client06.png)

5. Selezionare i gruppi di porte corretti per trunk (trunk PG), Public (uplink PG) e l'interfaccia a disponibilità elevata (uplink PG) per il client autonomo NSX-T. Fare clic su **Next** (Avanti).

    ![Selezione gruppi di porte](media/l2vpn-deploy-client07.png)

6. Inserire i dettagli seguenti nella schermata di **personalizzazione del modello** e fare clic su **Avanti**:

    Espandere I2t:

    * **Indirizzo peer**. Immettere l'indirizzo IP riservato nel portale di Azure CloudSimple per l'interfaccia di loopback NSX-T tier0.
    * **Codice peer**. Incollare il codice peer ottenuto dall'ultimo passaggio della distribuzione del server L2VPN.
    * **Interfacce secondarie VLAN (ID tunnel)** . Immettere l'ID VLAN da estendere. In parentesi () immettere l'ID del tunnel configurato in precedenza.

    Espandi interfaccia uplink:

    * **Indirizzo IP DNS**. Immettere l'indirizzo IP DNS locale.
    * **Gateway predefinito**.  Immettere il gateway predefinito della VLAN che fungerà da gateway predefinito per il client.
    * **Indirizzo IP**. Immettere l'indirizzo IP uplink del client autonomo.
    * **Lunghezza del prefisso**. Immettere la lunghezza del prefisso della VLAN/subnet uplink.
    * **Amministratore dell'interfaccia della riga di comando/Abilita/password utente root**. Impostare la password per l'account amministratore/Enable/root.

      ![personalizzare il modello](media/l2vpn-deploy-client08.png)
      ![personalizzare il modello](media/l2vpn-deploy-client09.png)

7. Verificare le impostazioni e fare clic su **fine**.

    ![Configurazione completa](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Configurare una porta di sink locale

Se per uno dei siti VPN non è stata distribuita la versione NSX, è possibile configurare una VPN L2 distribuendo un bordo di NSX autonomo in quel sito. Un perimetro NSX autonomo viene distribuito usando un file OVF in un host non gestito da NSX. Questa operazione consente di distribuire un appliance del gateway di servizi perimetrali di NSX per funzionare come client VPN L2.

Se un vNIC trunk periferico autonomo è connesso a un compartitore vSphere distribuito, per la funzione VPN L2 è necessario usare la modalità promiscua o la porta del sink. L'uso della modalità promiscua può causare ping duplicati e risposte duplicate. Per questo motivo, usare la modalità porta sink nella configurazione del perimetro della VPN L2 autonoma. Vedere la pagina relativa alla [configurazione di una porta di sink](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) nella documentazione di VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Verifica VPN IPsec e L2VPN

Usare i comandi seguenti per verificare le sessioni IPsec e L2VPN da standalone NSX-T Edge.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Usare i comandi seguenti per verificare le sessioni IPsec e L2VPN dal router NSX-T tier0.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Usare i comandi seguenti per verificare la porta di sink nell'host ESXi in cui si trova la macchina virtuale client autonoma NSX-T nell'ambiente locale.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
