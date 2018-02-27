---
title: Stack di Azure di connettersi a Azure tramite ExpressRoute
description: Come connettere reti virtuali in Azure Stack alle reti virtuali in Azure tramite ExpressRoute.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 8ec733c01b19f01951d0b53265daaef32c3afe73
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Stack di Azure di connettersi a Azure tramite ExpressRoute

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Esistono due metodi supportati per la connessione di reti virtuali in Azure Stack alle reti virtuali in Azure:
   * **Da sito a sito**

     Una connessione VPN tramite IPsec (IKE v1 e IKE v2). Per questo tipo di connessione è necessario un dispositivo VPN o RRAS. Per ulteriori informazioni, vedere [dello Stack di Azure connettersi a Azure tramite VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     Una connessione diretta a Azure nella distribuzione di Azure Stack. ExpressRoute è **non** una connessione VPN sulla rete Internet pubblica. Per ulteriori informazioni su Azure ExpressRoute, vedere [panoramica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

In questo articolo viene illustrato un esempio di utilizzo di ExpressRoute per connettersi a Azure Stack a Azure.
## <a name="requirements"></a>Requisiti
Di seguito sono indicati i requisiti specifici per la connessione di Stack di Azure e Azure tramite ExpressRoute:
* Una sottoscrizione di Azure per creare un circuito ExpressRoute e reti virtuali in Azure.
* Provisioning di un circuito ExpressRoute tramite un [provider di connettività](../expressroute/expressroute-locations.md).
* Un router che ha il circuito ExpressRoute connesso alle relative porte WAN.
* Il lato LAN del router è collegato a Azure Stack Multitenant Gateway.
* Il router deve supportare le connessioni VPN da sito a sito tra l'interfaccia LAN e un Gateway multi-tenant di Azure dello Stack.
* Se la distribuzione di Azure Stack viene aggiunta più di un tenant, il router deve essere in grado di creare più VRF (Routing virtuale e inoltro).

Dopo aver completato la configurazione, il diagramma seguente mostra una visualizzazione concettuale di rete:

![Diagramma concettuale](media/azure-stack-connect-expressroute/Conceptual.png)

**Diagramma 1**

Il diagramma dell'architettura seguente mostra come più tenant connettersi dall'infrastruttura di Azure Stack attraverso il router di ExpressRoute ad Azure in Microsoft edge:

![Diagramma dell'architettura](media/azure-stack-connect-expressroute/Architecture.png)

**Diagramma 2**

Nell'esempio viene illustrato in questo articolo utilizza la stessa architettura per connettersi ad Azure tramite ExpressRoute di peering privato. Operazione viene eseguita tramite una connessione Site-to-Site VPN da gateway di rete virtuale nello Stack di Azure a un router di ExpressRoute. I passaggi seguenti in questo articolo viene illustrato come creare una connessione end-to-end tra due reti virtuali da due diversi tenant in Azure Stack per le rispettive reti virtuali in Azure. È possibile scegliere di aggiungere come molte reti virtuali tenant e replicare i passaggi per ogni tenant o usare questo esempio per distribuire solo un singolo tenant di rete virtuale.

## <a name="configure-azure-stack"></a>Configurare Azure Stack
Ora possibile creare le risorse che necessarie per impostare l'ambiente dello Stack di Azure come tenant. La procedura descritta di seguito è necessario. Queste istruzioni viene illustrato come creare le risorse tramite il portale di Azure Stack, ma è anche possibile utilizzare PowerShell.

![Procedura per la risorsa di rete](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Prima di iniziare
Prima di iniziare la configurazione, è necessario:
* Una distribuzione di Azure Stack.

   Per informazioni sulla distribuzione di Azure Stack Development Kit, vedere [Guida rapida distribuzione di Azure Stack Development Kit](azure-stack-deploy-overview.md).
* Un'offerta nello Stack di Azure che può sottoscrivere l'utente.

  Per istruzioni, vedere [rendere disponibili agli utenti di Azure Stack macchine virtuali](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Creare risorse di rete nello Stack di Azure

Utilizzare le procedure seguenti per creare le risorse di rete richiesta nello Stack di Azure per ogni tenant:

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale
1. Accedere al portale utenti con un account utente (tenant).

2. Nel portale, fare clic su **New**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Selezionare **Rete** dal menu Marketplace.

4. Fare clic su **Rete virtuale** nel menu.

5. Digitare i valori nei campi appropriati utilizzando la tabella seguente:

   |Campo  |Valore  |
   |---------|---------|
   |NOME     |Tenant1VNet1         |
   |Spazio degli indirizzi     |10.1.0.0/16|
   |Nome della subnet     |Tenant1-Sub1|
   |Intervallo di indirizzi subnet     |10.1.1.0/24|

6. La sottoscrizione creata in precedenza verrà popolata nel campo **Sottoscrizione**.

    a. Per il gruppo di risorse, è possibile creare un gruppo di risorse o se si dispone già di uno, selezionare **utilizzare esistente**.

    b. Verificare la località predefinita.

    c. Fare clic su **Aggiungi al dashboard**.

    d. Fare clic su **Crea**.



#### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Aprire la risorsa di rete virtuale creata (Tenant1VNet1) dal dashboard.
2. Nella sezione Impostazioni, selezionare **subnet**.
3. Fare clic su **Subnet del gateway** per aggiungere una subnet del gateway alla rete virtuale.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. Il nome predefinito della subnet è **GatewaySubnet**.
   Questo nome specifico è necessario per il corretto funzionamento delle subnet del gateway.
5. Nel **intervallo di indirizzi** campo, verificare l'indirizzo sia **10.1.0.0/24**.
6. Fare clic su **OK** per creare la subnet del gateway.

#### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale per gli utenti dello Stack di Azure, fare clic su **New**.
   
2. Selezionare **Rete** dal menu Marketplace.
3. Selezionare **Gateway di rete virtuale** dall'elenco di risorse di rete.
4. Nel campo **Nome** digitare **GW1**.
5. Fare clic su **Rete virtuale** per scegliere una rete virtuale.
   Selezionare **Tenant1VNet1** dall'elenco.
6. Fare clic sulla voce di menu **Indirizzo IP pubblico**. Quando il **scegliere l'indirizzo IP pubblico** sezione viene visualizzata fare clic su **Crea nuovo**.
7. Nel campo **Nome** digitare **GW1-PiP** e fare clic su **OK**.
8. Per **Tipo VPN** deve essere selezionata l'opzione **Basato su route** per impostazione predefinita.
    Mantenere questa impostazione.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. Se si desidera, è possibile aggiungere la risorsa al Dashboard. Fare clic su **Crea**.

#### <a name="create-the-local-network-gateway"></a>Creare il gateway di rete locale

Lo scopo della risorsa di gateway di rete locale è per indicare il gateway remoto a altra estremità della connessione VPN. Per questo esempio, il lato remoto è sottointerfaccia LAN del router di ExpressRoute. Per Tenant 1 in questo esempio, l'indirizzo remoto è 10.60.3.255, come illustrato nel diagramma 2.

1. Accedere al computer fisico di Azure Stack.
2. Accedere al portale per gli utenti con account utente e fare clic su **New**.
3. Selezionare **Rete** dal menu Marketplace.
4. Selezionare **Gateway di rete locale** dall'elenco di risorse.
5. Nel **nome** il tipo di campo **ER-Router-GW**.
6. Per il **indirizzo IP** campo, fare riferimento al diagramma 2. L'indirizzo IP del subinterface LAN del router ExpressRoute per Tenant 1 è 10.60.3.255. Per l'ambiente, digitare l'indirizzo IP dell'interfaccia corrispondente del router.
7. Nel **spazio degli indirizzi** digitare lo spazio degli indirizzi delle reti virtuali che si desidera connettersi in Azure. Per questo esempio, vedere il diagramma 2. Per Tenant 1, si noti che le subnet necessarie sono **192.168.2.0/24** (si tratta della rete virtuale Hub in Azure) e **10.100.0.0/16** (si tratta di rete virtuale in Azure Spoke). Digitare le subnet corrispondenti per il proprio ambiente.
   > [!IMPORTANT]
   > Questo esempio si presuppone il che uso route statiche per la connessione VPN da sito a sito tra il gateway di Stack di Azure e il router di ExpressRoute.

8. Verificare che il **sottoscrizione**, **gruppo di risorse**, e **percorso** siano tutti corretti e fare clic su **crea**.

#### <a name="create-the-connection"></a>Creare la connessione
1. Nel portale per gli utenti dello Stack di Azure, fare clic su **New**.
2. Selezionare **Rete** dal menu Marketplace.
3. Selezionare **Connessione** dall'elenco di risorse.
4. Nel **nozioni di base** sezione Impostazioni, scegliere **Site-to-site (IPSec)** come il **tipo di connessione**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **percorso** e fare clic su **OK**.
6. Nel **impostazioni** fare clic su **gateway di rete virtuale** fare clic su **GW1**.
7. Fare clic su **gateway di rete locale**, fare clic su **ER Router GW**.
8. Nel **nome connessione** digitare **ConnectToAzure**.
9. Nel **chiave condivisa (PSK)** digitare **abc123** e fare clic su **OK**.
10. Nel **riepilogo** fare clic su **OK**.

    Dopo aver creata la connessione, è possibile visualizzare l'indirizzo IP pubblico usato dal gateway di rete virtuale. Per trovare l'indirizzo nel portale di Azure Stack, individuare il gateway di rete virtuale. In **Panoramica**, trovare il **indirizzo IP pubblico**. Tenere presente questo indirizzo. sarà necessario utilizzarlo come il *indirizzo IP interno* nella sezione successiva (se applicabile per la distribuzione).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Per convalidare i dati che passano attraverso la connessione VPN, è necessario inviare e ricevere dati nella rete virtuale Azure Stack delle macchine virtuali. Creare ora una macchina virtuale e inserirlo nella subnet VM nella rete virtuale.

1. Nel portale per gli utenti dello Stack di Azure, fare clic su **New**.
2. Selezionare **Macchine virtuali** dal menu Marketplace.
3. Nell'elenco di immagini di macchina virtuale, selezionare il **Eval di Data Center di Windows Server 2016** immagine e fare clic su **crea**.
4. Nel **nozioni di base** nella sezione di **nome** il tipo di campo **VM01**.
5. Immettere un nome utente e una password validi. Questo account verrà usato per accedere alla macchina virtuale, dopo averla creata.
6. Fornire un **sottoscrizione**, **gruppo di risorse**, e **percorso** e quindi fare clic su **OK**.
7. Nel **dimensioni** sezione, fare clic su una dimensione di macchina virtuale per questa istanza e quindi fare clic su **selezionare**.
8. Nel **impostazioni** sezione, è possibile accettare i valori predefiniti. Ma occorre verificare che la rete virtuale selezionata è **Tenant1VNet1** e la subnet è impostata su **10.1.1.0/24**. Fare clic su **OK**.
9. Esaminare le impostazioni nella **riepilogo** sezione e fare clic su **OK**.

Per ogni tenant di rete virtuale che si desidera connettersi, ripetere i passaggi precedenti da **creare la rete virtuale e una subnet VM** tramite **creare una macchina virtuale** sezioni.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Configurare la macchina virtuale NAT per l'attraversamento di gateway
> [!IMPORTANT]
> Questa sezione è destinata solo le distribuzioni di Azure Stack Development Kit. NAT non è necessaria per le distribuzioni a più nodi.

Il Kit di sviluppo dello Stack di Azure è indipendente e isolate dalla rete in cui è distribuita l'host fisico. In tal caso, la rete "External" VIP che sono connessi i gateway non è esterna, ma invece è nascosto dietro a un router esegue Network Address Translation (NAT).
 
Il router è una macchina virtuale di Windows Server (**AzS BGPNAT01**) esegue il ruolo servizio Routing e accesso remoto (RRAS) nell'infrastruttura di Azure Stack Development Kit. Nella macchina virtuale AzS BGPNAT01 per consentire la connessione VPN da sito a sito per connettersi a entrambe le estremità, è necessario configurare NAT.

#### <a name="configure-the-nat"></a>Configurare NAT

1. Accedere al computer fisico dello Stack di Azure con l'account administrator.
2. Copiare e modificare il seguente script di PowerShell ed eseguire in un Windows PowerShell ISE con privilegi elevati. Sostituire la password dell'amministratore. L'indirizzo restituito è il *indirizzo esterno BGPNAT*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. Per configurare il protocollo NAT, copiare e modificare il seguente script di PowerShell ed eseguire in un Windows PowerShell ISE con privilegi elevati. Modificare lo script per sostituire il *indirizzo esterno BGPNAT* e *indirizzo IP interno* (che si è preso nota in precedenza nel **creare la connessione** sezione).

   Nei diagrammi di esempio, il *indirizzo esterno BGPNAT* è 10.10.0.62 e *indirizzo IP interno* è 192.168.102.1.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Configurazione di Azure
Ora che è stata completata la configurazione dello Stack di Azure, è possibile distribuire alcune risorse di Azure. Il diagramma seguente mostra un tenant di esempio la rete virtuale in Azure. È possibile utilizzare qualsiasi nome e lo schema di indirizzamento per la rete virtuale in Azure. Tuttavia, l'intervallo di indirizzi delle reti virtuali in Azure e Azure Stack deve essere univoco e non si sovrappongono.

![Reti virtuali di Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Diagramma 3**

Le risorse a cui che si distribuisce in Azure sono simili alle risorse di cui che è stato distribuito nello Stack di Azure. Analogamente, distribuire:
* Le reti virtuali e subnet
* Una subnet del gateway
* Un gateway di rete virtuale
* Una connessione
* Un circuito ExpressRoute

L'infrastruttura di rete di Azure di esempio è configurato nel modo seguente:
* Viene utilizzato un hub standard (192.168.2.0/24) e spoke (10.100.0.0./16) del modello di rete virtuale.
* I carichi di lavoro vengono distribuiti nella rete virtuale del raggio e il circuito ExpressRoute è connesso all'hub di rete virtuale.
* Le due reti virtuali sono collegate tramite la funzionalità di peering reti virtuali.

### <a name="configure-vnets"></a>Configurare le reti virtuali
1. Accedere al portale di Azure con le credenziali di Azure.
2. Creare una rete virtuale usando lo spazio degli indirizzi 192.168.2.0/24 l'hub. Creare una subnet con l'intervallo di indirizzi 192.168.2.0/25 e aggiungere una subnet del gateway con l'intervallo di indirizzi 192.168.2.128/27.
3. Creare spoke intervallo di indirizzi di rete virtuale e subnet utilizzando la 10.100.0.0/16.


Per ulteriori informazioni sulla creazione di reti virtuali in Azure, vedere [creare una rete virtuale con più subnet](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

### <a name="configure-an-expressroute-circuit"></a>Configurare un circuito ExpressRoute

1. Esaminare i prerequisiti di ExpressRoute in [ExpressRoute prerequisiti & elenco di controllo](../expressroute/expressroute-prerequisites.md).
2. Seguire i passaggi in [creare e modificare un circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) per creare un circuito ExpressRoute tramite la sottoscrizione di Azure.
3. Condividere la chiave del servizio nel passaggio precedente con l'host o il provider per eseguire il provisioning del circuito ExpressRoute le estremità.
4. Seguire i passaggi in [creare e modificare peering per un circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) per configurare il peer privato nel circuito ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale

* Seguire i passaggi in [configurare un gateway di rete virtuale per ExpressRoute con PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) per creare un gateway di rete virtuale per ExpressRoute nell'hub di rete virtuale.

### <a name="create-the-connection"></a>Creare la connessione

* Per collegare il circuito ExpressRoute all'hub di rete virtuale, seguire i passaggi in [connettere una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Le reti virtuali peer

* Peer di Hub e Spoke reti virtuali utilizzando la procedura descritta in [creare una rete virtuale peering tramite il portale di Azure](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Quando si configura peering reti virtuali, assicurarsi di che selezionare le opzioni seguenti:
   * Dall'hub per spoke: **consentire transito gateway**
   * Da spoke all'hub: **Usa gateway remoto**

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

* Distribuire le macchine virtuali del carico di lavoro in spoke rete virtuale.

Ripetere questi passaggi per qualsiasi tenant altre reti virtuali che si desidera connettersi in Azure tramite li rispettivi circuiti ExpressRoute.

## <a name="configure-the-router"></a>Configurare il router

È possibile utilizzare il seguente diagramma infrastruttura end-to-end per la configurazione del ExpressRoute Router. Questo diagramma mostra due tenant (Tenant 1 e 2 Tenant) con i rispettivi circuiti Expressroute. Ogni collegato i propri VRF (Routing virtuale e inoltro) sul lato LAN e WAN del router di ExpressRoute per garantire l'isolamento end-to-end tra i due tenant. Si noti gli indirizzi IP usati nelle interfacce di router mentre si esegue la configurazione di esempio.

![Fine alla fine di diagramma](media/azure-stack-connect-expressroute/EndToEnd.png)

**Diagramma 4**

È possibile utilizzare qualsiasi router che supporta BGP e VPN IKEv2 per terminare la connessione VPN Site-to-Site dallo Stack di Azure. Il router stesso viene utilizzato per connettersi a Azure tramite un circuito ExpressRoute. 

Di seguito è riportato un esempio di configurazione da un 1000 ASR Cisco che supporta l'infrastruttura di rete illustrato nel diagramma 4:

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Testare la connessione

Verificare la connessione dopo avere stabilito la connessione da sito a sito e il circuito ExpressRoute. Questa attività è semplice.  Accedere a una macchina virtuale creata in una rete virtuale di Azure ed effettuare il ping la macchina virtuale creata nell'ambiente di Azure Stack o viceversa. 

Per garantire che si stanno inviando il traffico attraverso il sito a sito e le connessioni ExpressRoute, è necessario effettuare il ping l'indirizzo IP (DIP) dedicato della macchina virtuale a entrambe le estremità e non l'indirizzo VIP della macchina virtuale. In tal caso, è necessario trovare e prendere nota dell'indirizzo a altra estremità della connessione.

### <a name="allow-icmp-in-through-the-firewall"></a>Consentire il protocollo ICMP in attraverso il firewall
Per impostazione predefinita, Windows Server 2016 non consente ai pacchetti ICMP in attraverso il firewall. In tal caso, per ogni macchina virtuale che è utilizzare nel test, eseguire il cmdlet seguente in una finestra di PowerShell con privilegi elevata:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Eseguire il ping della macchina virtuale di Azure Stack

1. Accedere al portale per gli utenti dello Stack di Azure utilizzando un account del tenant.
2. Nel riquadro di spostamento a sinistra fare clic su **Macchine virtuali**.
3. Trovare la macchina virtuale creata in precedenza e farvi clic sopra.
4. Nella sezione per la macchina virtuale, fare clic su **Connetti**.
5. Aprire un PowerShell con privilegi elevati finestra e digitare **ipconfig /all**.
6. Individuare l'indirizzo IPv4 nell'output e annotarlo. Eseguire il ping di questo indirizzo dalla macchina virtuale nella rete virtuale di Azure. Nell'ambiente di esempio, l'indirizzo è dalla subnet 10.1.1.x/24. Nell'ambiente in uso, l'indirizzo potrebbe essere diverso. Tuttavia, deve essere all'interno della subnet creato per la subnet di rete virtuale di Tenant.


### <a name="view-data-transfer-statistics"></a>Visualizzare le statistiche di trasferimento di dati

Se si desidera conoscere la quantità di traffico passa attraverso la connessione, è possibile trovare queste informazioni nella sezione di connessione nel portale utenti Azure Stack. Queste informazioni sono anche un altro metodo per verificare che il ping che appena stata inviata parlato effettivamente le connessioni ExpressRoute e VPN.

1. Accedere al portale per gli utenti dello Stack di Microsoft Azure con l'account tenant.
2. Passare al gruppo di risorse in cui è stato creato il Gateway VPN e selezionare il **connessioni** tipo di oggetto.
3. Fare clic su di **ConnectToAzure** connessione nell'elenco.
4. Nel **connessione** sezione, è possibile visualizzare le statistiche di **dati** e **i dati in uscita**. Dovrebbero essere presenti valori diversi da zero.

   ![Dati In dati in uscita](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Passaggi successivi
[Distribuire le App in Azure e Azure Stack](azure-stack-solution-pipeline.md)
