---
title: Soluzione Azure VMware di CloudSimple - Estendere una rete di livello 2 locale al cloud privato
description: Descrive come configurare una VPN di livello 2 tra NSX-T in un cloud privato CloudSimple e un client NSX Edge autonomo locale
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f524bf6af66d44bc13b7c0957de7977968cbef28
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427251"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Eseguire la migrazione di carichi di lavoro usando reti estese di livello 2

In questa guida verrà illustrato come usare la VPN di livello 2 (L2VPN) per estendere una rete di livello 2 dall'ambiente locale al cloud privato CloudSimple. Questa soluzione consente la migrazione dei carichi di lavoro in esecuzione nell'ambiente VMware locale verso il cloud privato in Azure nello stesso spazio indirizzi della subnet senza dover usare un nuovo IP per i carichi di lavoro.

L'estensione basata su L2VPN di reti di livello 2 può funzionare con o senza le reti basate su NSX nell'ambiente VMware locale. Se non si dispone di reti basate su NSX per i carichi di lavoro locali, è possibile usare un gateway di servizi NSX Edge autonomo.

> [!NOTE]
> Questa guida descrive lo scenario in cui i data center locali e del cloud privato sono connessi tramite VPN da sito a sito.

## <a name="deployment-scenario"></a>Scenario di distribuzione

Per estendere la rete locale con L2VPN, è necessario configurare un server L2VPN (destinazione router NSX-T di livello 0) e un client L2VPN (client di origine autonomo).  

In questo scenario di distribuzione, il cloud privato è connesso all'ambiente locale tramite un tunnel VPN da sito a sito che consente alle subnet di gestione e vMotion locali di comunicare con le subnet di gestione e vMotion del cloud privato. Questa disposizione è necessaria per Cross vCenter vMotion (xVC-vMotion). Un router NSX-T di livello 0 viene distribuito come server L2VPN nel cloud privato.

NSX Edge autonomo viene distribuito nell'ambiente locale come client L2VPN e successivamente associato al server L2VPN. Un endpoint del tunnel GRE viene creato su ogni lato e configurato per "estendere" la rete locale di livello 2 al cloud privato. Questa configurazione è illustrata nella figura seguente.

![Scenario di distribuzione](media/l2vpn-deployment-scenario.png)

Per altre informazioni sulla migrazione tramite VPN L2, vedere [Reti private virtuali](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) nella documentazione di VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Prerequisiti per distribuire la soluzione

Prima di distribuire e configurare la soluzione, verificare che le affermazioni seguenti siano confermate:

* La versione di vSphere locale è 6.7U1+ o 6.5P03+.
* La licenza di vSphere locale è al livello Enterprise Plus (per vSphere Distributed Switch).
* Identificare la rete di livello 2 del carico di lavoro da estendere al cloud privato.
* Identificare una rete di livello 2 nell'ambiente locale per distribuire l'appliance client L2VPN.
* [Un cloud privato è già stato creato](create-private-cloud.md).
* La versione dell'appliance NSX-T Edge autonomo è compatibile con la versione di NSX-T Manager (NSX-T 2.3.0) usata nell'ambiente cloud privato.
* In vCenter locale è stato creato un gruppo di porte trunk con trasmissioni falsificate abilitate.
* Un indirizzo IP pubblico è stato riservato per essere usato per l'indirizzo IP uplink del client NSX-T autonomo ed è presente 1:1 NAT per la conversione tra i due indirizzi.
* L'inoltro DNS è impostato nei server DNS locali per il dominio az.cloudsimple.io in modo che punti ai server DNS del cloud privato.
* La latenza RTT è inferiore o uguale a 150 ms, in base a quanto previsto affinché vMotion funzioni nei due siti.

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

La tabella seguente elenca le versioni di vSphere supportate e i tipi di adattatori di rete.  

| Versione di vSphere | Tipo di vSwitch di origine | Driver della scheda di interfaccia di rete virtuale | Tipo di vSwitch di destinazione | Supportato? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Tutti | DVS | Tutti | DVS | Sì |
| vSphere 6.7UI o versione successiva, 6.5P03 o versione successiva | DVS | VMXNET3 | N-VDS | Sì |
| vSphere 6.7UI o versione successiva, 6.5P03 o versione successiva | DVS | E1000 | N-VDS | [Non supportata per VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI o 6.5P03, NSX-V o versioni precedenti a NSX-T2.2, 6.5P03 o versione successiva | Tutti | Tutti | N-VDS | [Non supportata per VWware](https://kb.vmware.com/s/article/56991) |

A partire dalla versione VMware NSX-T 2.3:

* Il commutatore logico sul lato cloud privato che si estende in locale tramite L2VPN non può essere instradato nello stesso momento. Il commutatore logico esteso non può essere connesso a un router logico.
* L2VPN e le VPN IPSEC basate su route possono essere configurate solo con le chiamate API.

Per altre informazioni, vedere [Reti private virtuali](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) nella documentazione di VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Esempio di indirizzamento della distribuzione VPN L2

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Rete locale in cui è distribuito l'ESG (client VPN L2) autonomo

| **Elemento** | **Valore** |
|------------|-----------------|
| Nome della rete | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| Indirizzo IP dell'appliance Edge autonoma | 10.250.0.111 |
| Indirizzo IP NAT dell'appliance Edge autonoma | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Rete locale da estendere

| **Elemento** | **Valore** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Schema IP del cloud privato per il router NSX-T di livello 0 (per la VPN L2)

| **Elemento** | **Valore** |
|------------|-----------------|
| Interfaccia di loopback | 192.168.254.254/32 |
| Interfaccia del tunnel | 5.5.5.1/29 |
| Commutatore logico (esteso) | Stretch_LS |
| Interfaccia di loopback (indirizzo IP NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Rete cloud privata da mappare alla rete estesa

| **Elemento** | **Valore** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Recuperare l'ID del router logico necessario per L2VPN

La procedura seguente illustra come recuperare l'ID dell'istanza del router logico DR di livello 0 per i servizi IPsec e L2VPN. L'ID del router logico è necessario in seguito, durante l'implementazione di L2VPN.

1. Accedere a `https://*nsx-t-manager-ip-address*` di NSX-T Manager e selezionare **Networking (Rete)**  > **Routers (Router)**  > **Provider-LR** > **Overview (Panoramica)** . Per **High Availability Mode** (Modalità a disponibilità elevata), selezionare **Active-Standby** (Attivo-Standby). Questa azione apre una finestra popup che mostra la macchina virtuale perimetrale in cui è attualmente attivo il router di livello 0.

    ![Selezionare Active-Standby (Attivo-Standby)](media/l2vpn-fetch01.png)

2. Selezionare **Fabric (Infrastruttura)**  > **Nodes (Nodi)**  > **Edges (Archi)** . Prendere nota dell'indirizzo IP di gestione della macchina virtuale perimetrale (Edge VM1) attiva identificato nel passaggio precedente.

    ![Annotare l'IP di gestione](media/l2vpn-fetch02.png)

3. Aprire una sessione SSH per l'indirizzo IP di gestione della macchina virtuale perimetrale. Eseguire il comando ```get logical-router``` con username **admin** and password **CloudSimple 123!** .

    ![Screenshot che mostra una sessione SSH aperta.](media/l2vpn-fetch03.png)

4. Se non viene visualizzata una voce "DR-provider-LR", completare la procedura seguente.

5. Creare due commutatori logici sovrapposti. Un commutatore logico viene esteso in locale, dove si trovano i carichi di lavoro migrati. Un altro commutatore logico è un commutatore fittizio. Per istruzioni, vedere [Creare un commutatore logico](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) nella documentazione di VMware.

    ![Creare un commutatore logico](media/l2vpn-fetch04.png)

6. Collegare il commutatore fittizio al router di livello 1 con un indirizzo IP locale di collegamento o qualsiasi subnet non sovrapposta da locale o dal cloud privato. Vedere [Aggiungere una porta di downlink in un router logico di livello 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) nella documentazione di VMware.

    ![Collegare un commutatore fittizio](media/l2vpn-fetch05.png)

7. Eseguire di nuovo il comando `get logical-router` nella sessione SSH della macchina virtuale perimetrale. Viene visualizzato l'UUID del router logico "DR-provider-LR". Prendere nota dell'UUID necessario nella configurazione di L2VPN.

    ![Screenshot che mostra l'UUID per il router logico.](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Recuperare l'ID del commutatore logico necessario per L2VPN

1. Accedere a NSX-T Manager (`https://nsx-t-manager-ip-address`).
2. Selezionare **Networking (Rete)**  > **Switching (Commutazione)**  > **Switches (Commutatori)**  >  **<\Logical switch (Commutatore logico)\>**  > **Overview (Panoramica)** .
3. Prendere nota dell'UUID del commutatore logico di estensione, che è necessario quando si configura L2VPN.

    ![ottenere l'output del router logico](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Considerazioni relative alla rete e alla sicurezza per L2VPN

Per stabilire una VPN basata su route IPsec tra il router NSX-T di livello 0 e il client NSX Edge autonomo, l'interfaccia di loopback del router NSX-T di livello 0 deve essere in grado di comunicare con l'indirizzo IP pubblico del client autonomo NSX in locale tramite UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Consentire l'UDP 500/4500 per IPsec

1. [Creare un indirizzo IP pubblico](public-ips.md) per l'interfaccia di loopback NSX-T di livello 0 nel portale di CloudSimple.

2. [Creare una tabella firewall](firewall.md) con regole con stato che consentono il traffico in ingresso tramite UDP 500/4500 e collegare la tabella firewall alla subnet NSX-T HostTransport.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Annunciare l'IP dell'interfaccia di loopback alla rete sottostante

1. Creare una route null per la rete dell'interfaccia di loopback. Accedere a NSX-T Manager e selezionare **Networking (Rete)**  > **Routing** > **Routers (Router)**  > **Provider-LR** > **Routing** > **Static Routes (Route statiche)** . Scegliere **Aggiungi**. Per **Network** (Rete), immettere l'indirizzo IP dell'interfaccia di loopback. Per **Next Hops** (Hop successivi), fare clic su **Add** (Aggiungi), specificare "Null" per l'hop successivo e mantenere il valore predefinito di 1 per Admin Distance (Distanza di amministrazione).

    ![Aggiungere una route statica](media/l2vpn-routing-security01.png)

2. Creare un elenco di prefissi IP. Accedere a NSX-T Manager e selezionare **Networking (Rete)**  > **Routing** > **Routers (Router)**  > **Provider-LR** > **Routing** > **IP Prefix Lists (Elenco di prefissi IP)** . Scegliere **Aggiungi**. Immettere un nome per identificare l'elenco. Per **Prefixes** (Prefissi), fare clic due volte su **Add** (Aggiungi). Nella prima riga immettere "0.0.0.0/0" per **Network** (Rete) e "Deny" (Nega) per **Action** (Azione). Nella seconda riga selezionare **Any** (Qualsiasi) per **Network** (Rete) e **Permit** (Consenti) per **Action** (Azione).
3. Collegare l'elenco di prefissi IP a entrambi i router adiacenti BGP (TOR). Il collegamento dell'elenco di prefissi IP al router adiacente BGP impedisce che la route predefinita venga annunciata nel BGP ai commutatori TOR. Tuttavia, qualsiasi altra route che includa la route null annuncia l'indirizzo IP dell'interfaccia di loopback ai commutatori TOR.

    ![Creare un elenco di prefissi IP](media/l2vpn-routing-security02.png)

4. Accedere a NSX-T Manager e selezionare **Networking (Rete)**  > **Routing** > **Routers (Router)**  > **Provider-LR** > **Routing** > **BGP** > **Neighbors (Router adiacenti)** . Selezionare il primo router adiacente. Fare clic su **Edit (Modifica)**  > **Address Families** (Famiglie di indirizzi). Per la famiglia IPv4, modificare la colonna **Out Filter** (Filtro esterno) e selezionare l'elenco di prefissi IP creato. Fare clic su **Salva**. Ripetere questo passaggio per il secondo router adiacente.

    ![Collegare l'elenco di prefissi IP 1](media/l2vpn-routing-security03.png) ![Collegare l'elenco di prefissi IP 2](media/l2vpn-routing-security04.png)

5. Ridistribuire la route statica null in BGP. Per annunciare la route dell'interfaccia di loopback alla rete sottostante, è necessario ridistribuire la route statica null in BGP. Accedere a NSX-T Manager e selezionare **Networking (Rete)**  > **Routing** > **Routers (Router)**  > **Provider-LR** > **Routing** > **Redistribution (Ridistribuzione)**  > **Neighbors (Router adiacenti)** . Selezionare **Provider-LR-Route_Redistribution** e fare clic su **Edit** (Modifica). Selezionare la casella di controllo **Static** (Statico) e fare clic su **Save** (Salva).

    ![Ridistribuire la route statica null in BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Configurare una VPN basata su route nel router NSX-T di livello 0

Usare il modello seguente per inserire tutti i dettagli per la configurazione di una VPN basata su route nel router NSX-T di livello 0. Gli UUID in ogni chiamata POST sono necessari per le successive chiamate POST. Gli indirizzi IP per le interfacce di loopback e del tunnel per L2VPN devono essere univoci e non sovrapporsi alle reti locali o cloud private.

Gli indirizzi IP scelti per l'interfaccia del tunnel o di loopback usati per L2VPN devono essere univoci e non sovrapporsi alle reti locali o cloud private. La rete dell'interfaccia di loopback deve essere sempre /32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
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

Per tutte le chiamate API seguenti, sostituire l'indirizzo IP con l'indirizzo IP di NSX-T Manager. È possibile eseguire tutte queste chiamate API dal client POSTMAN oppure usando i comandi `curl`.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Abilitare il servizio VPN di IPSec sul router logico

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

### <a name="create-profiles-ike"></a>Creare profili: IKE

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

### <a name="create-profiles-dpd"></a>Creare profili: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Creare profili: Tunnel

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

### <a name="create-a-route-based-vpn-session"></a>Creare un gateway VPN basato su route

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

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Configurare L2VPN nel router NSX-T di livello 0

Specificare le informazioni seguenti dopo ogni chiamata POST. Gli ID sono necessari nelle successive chiamate POST.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Creare il servizio L2VPN

L'output del comando GET seguente sarà vuoto, perché la configurazione non è stata ancora completata.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Per il comando POST seguente, l'ID del router logico è l'UUID del router logico DR di livello 0 ottenuto in precedenza.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Creare la sessione L2VPN

Per il comando POST seguente, l'ID del servizio L2VPN è l'ID appena ottenuto e l'ID della sessione VPN IPsec è l'ID ottenuto nella sezione precedente.

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

Queste chiamate creano un endpoint del tunnel GRE. Eseguire il comando seguente per verificare lo stato.

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

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Creare la porta logica con l'ID del tunnel specificato

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

Ottenere il codice peer dell'endpoint NSX-T. Il codice peer è obbligatorio quando si configura l'endpoint remoto. <session-id> di L2VPN può essere ottenuto nella sezione precedente. Per altre informazioni, vedere la [guida all'API di NSX-T 2.3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Distribuire il client NSX-T autonomo (locale)

Prima di eseguire la distribuzione, verificare che le regole del firewall locale consentano il traffico UDP 500/4500 in ingresso e in uscita da/verso l'indirizzo IP pubblico di CloudSimple riservato in precedenza per l'interfaccia di loopback del router NSX-T T0. 

1. [Scaricare l'OVF del client NSX Edge autonomo](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) ed estrarre i file dal bundle scaricato in una cartella.

    ![Scaricare il client NSX Edge autonomo](media/l2vpn-deploy-client01.png)

2. Passare alla cartella con tutti i file estratti. Selezionare tutti i vmdk (NSX-l2t-client-large.mf e NSX-l2t-client-large.ovf per appliance di grandi dimensioni o NSX-l2t-client-Xlarge.mf e NSX-l2t-client-Xlarge.ovf per appliance di dimensioni molto grandi). Fare clic su **Avanti**.

    ![Selezionare ](media/l2vpn-deploy-client02.png) ![ la schermata modello che mostra i file VMDK selezionati.](media/l2vpn-deploy-client03.png)

3. Immettere un nome per il client NSX-T autonomo e fare clic su **Next** (Avanti).

    ![Immettere il nome del modello](media/l2vpn-deploy-client04.png)

4. In base alle esigenze, fare clic su **Next** (Avanti) per raggiungere le impostazioni dell'archivio dati. Selezionare l'archivio dati appropriato per il client NSX-T autonomo e fare clic su **Next** (Avanti).

    ![Selezionare l'archivio dati](media/l2vpn-deploy-client06.png)

5. Selezionare i gruppi di porte corretti per Trunk, ovvero gruppi di porte trunk, Public (Pubblico) e HA interface (Interfaccia HA), ovvero gruppi di porte di uplink, per il client NSX-T autonomo. Fare clic su **Avanti**.

    ![Selezione i gruppi di porte](media/l2vpn-deploy-client07.png)

6. Inserire i dettagli seguenti nella schermata **Customize template** (Personalizza modello) e fare clic su **Next** (Avanti):

    Espandere L2T:

    * **Peer Address** (Indirizzo peer). Immettere l'indirizzo IP riservato nel portale di Azure CloudSimple per l'interfaccia di loopback NSX-T di livello 0.
    * **Peer Code** (Peer del codice). Incollare il codice peer ottenuto dall'ultimo passaggio della distribuzione del server L2VPN.
    * **Sub Interfaces VLAN (Tunnel ID)** (VLAN delle sotto-interfacce - ID tunnel). Immettere l'ID VLAN da estendere. Tra parentesi () immettere l'ID del tunnel configurato in precedenza.

    Espandere l'interfaccia di uplink:

    * **DNS IP Address** (Indirizzo IP DNS). Immettere l'indirizzo IP DNS locale.
    * **Default Gateway** (Gateway predefinito).  Immettere il gateway predefinito della VLAN che fungerà da gateway predefinito per il client.
    * **IP Address** (Indirizzo IP). Immettere l'indirizzo IP di uplink del client autonomo.
    * **Prefix Length** (Lunghezza del prefisso). Immettere la lunghezza del prefisso della VLAN/subnet di uplink.
    * **CLI admin/enable/root User Password** (Password utente di amministrazione/abilitazione/radice dell'interfaccia della riga di comando). Impostare la password per l'account di amministrazione/abilitazione/radice.

      ![Personalizzare il modello](media/l2vpn-deploy-client08.png)
      ![Personalizzare il modello - altro](media/l2vpn-deploy-client09.png)

7. Rivedere le impostazioni e fare clic su **Finish** (Fine).

    ![Completare la configurazione](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Configurare una porta sink locale

Se per uno dei siti VPN, NSX non è stato distribuito è possibile configurare una VPN L2 distribuendo NSX Edge autonomo in quel sito. NSX Edge autonomo viene distribuito usando un file OVF in un host non gestito da NSX. Questa operazione consente di distribuire un'appliance del gateway di servizi NSX Edge che funzioni come client VPN L2.

Se un vNIC trunk periferico autonomo è connesso a un commutatore distribuito vSphere, per la funzione VPN L2 è necessario usare la modalità promiscua o la porta sink. L'uso della modalità promiscua può causare ping e risposte duplicati. Per questo motivo, usare la modalità della porta sink nella configurazione di NSX Edge autonoma per VPN L2. Vedere [Configurare una porta sink](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) nella documentazione di VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Verifica di VPN IPsec e L2VPN

Usare i comandi seguenti per verificare le sessioni di IPsec e L2VPN in NSX-T Edge autonomo.

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

Usare i comandi seguenti per verificare le sessioni IPsec e L2VPN dal router NSX-T di livello 0.

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

Usare i comandi seguenti per verificare la porta sink nell'host ESXi in cui si trova la macchina virtuale del client NSX-T autonomo nell'ambiente locale.

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
