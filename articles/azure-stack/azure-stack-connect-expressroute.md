---
title: Stack di Azure Connect ad Azure tramite ExpressRoute
description: Informazioni su come connettere reti virtuali in Azure Stack per le reti virtuali in Azure tramite ExpressRoute.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 0ebd17eca363d7fc02daeb851bb24b8d1d307efc
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339602"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Connettere Azure Stack per Azure tramite Azure ExpressRoute

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questo articolo descrive come connettere una rete virtuale di Azure Stack per una rete virtuale di Azure usando un [Microsoft Azure ExpressRoute](/azure/expressroute/) connessione diretta.

È possibile usare questo articolo come un'esercitazione e usare gli esempi per configurare l'ambiente di test stesso. In alternativa, è possibile usare l'articolo come una procedura dettagliata che consente di configurare il proprio ambiente di ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Panoramica e delle assunzioni e prerequisiti

Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Non ExpressRoute è una connessione VPN attraverso la rete internet pubblica.

Per altre informazioni su Azure ExpressRoute, vedere la [Panoramica di ExpressRoute](../expressroute/expressroute-introduction.md).

### <a name="assumptions"></a>Presupposti

Questo articolo presuppone che:

* L'utente abbia una conoscenza di Azure.
* Si dispone di una conoscenza di base di Azure Stack.
* Si dispone di una conoscenza di base delle reti.

### <a name="prerequisites"></a>Prerequisiti

Per connettere Azure Stack e Azure tramite ExpressRoute, è necessario soddisfare i requisiti seguenti:

* Una credenziale [circuito ExpressRoute](../expressroute/expressroute-circuit-peerings.md) tramite un [provider di connettività](../expressroute/expressroute-locations.md).
* Una sottoscrizione di Azure per creare un circuito ExpressRoute e le reti virtuali in Azure.
* Un router che è necessario:
  * Supporta le connessioni VPN Site-to-Site tra l'interfaccia LAN e un Gateway multi-tenant di Azure Stack.
  * Supporta la creazione di più VRF (Routing virtuale e l'inoltro) se nella distribuzione di Azure Stack è presente più di un tenant.
* Un router con:
  * Una porta di rete WAN connessa al circuito ExpressRoute.
  * Una porta LAN connessa al gateway multi-tenant di Azure Stack.

### <a name="expressroute-network-architecture"></a>Architettura di rete di ExpressRoute

Il diagramma seguente mostra gli ambienti Azure e Azure Stack dopo aver configurato ExpressRoute usando gli esempi in questo articolo:

*Figura 1. Rete di ExpressRoute*

![Rete di ExpressRoute](media/azure-stack-connect-expressroute/Conceptual.png)

Il diagramma seguente mostra come più i tenant si connettono dall'infrastruttura di Azure Stack tramite il router di ExpressRoute in Azure in Microsoft edge:

*Figura 2. Connessioni di multi-tenant*

![Connessioni di multi-tenant con ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

L'esempio in questo articolo usa la stessa architettura multi-tenant illustrata nella figura 2 per la connessione di Azure Stack in Azure tramite il peering privato di ExpressRoute. La connessione avviene tramite una connessione VPN site-to-site dal gateway di rete virtuale in Azure Stack per un router di ExpressRoute.

I passaggi descritti in questo articolo mostrano come creare una connessione end-to-end tra due reti virtuali alle reti virtuali corrispondenti in Azure da due diversi tenant in Azure Stack. Configurazione di due tenant è facoltativo. è anche possibile usare questi passaggi per un singolo tenant.

## <a name="configure-azure-stack"></a>Configurare Azure Stack

Per configurare l'ambiente Azure Stack per il primo tenant, usare i passaggi descritti nel diagramma seguente come guida. Se si sta configurando più di un tenant, ripetere questi passaggi:

>[!NOTE]
>Questi passaggi illustrano come creare le risorse usando il portale di Azure Stack, ma è anche possibile usare PowerShell.

![Configurazione di rete di Azure Stack](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione di Azure Stack, è necessario:

* Distribuzione di un sistema integrato Azure Stack o una distribuzione di Azure Stack Development Kit (ASDK). Per informazioni sulla distribuzione di ASDK, vedere la [Guida introduttiva alla distribuzione Azure Stack Development Kit](azure-stack-deploy-overview.md).
* Un'offerta in Azure Stack che gli utenti possono sottoscrivere. Per altre informazioni, vedere [piani, offerte e sottoscrizioni](azure-stack-plan-offer-quota-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Creare le risorse di rete in Azure Stack

Usare le procedure seguenti per creare le risorse di rete necessarie in Azure Stack per un tenant.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale

1. Accedere al portale per gli utenti con un account utente (tenant).

2. Nel portale, selezionare **+ crea una risorsa**.

3. Sotto **Azure Marketplace**, selezionare **Networking**.

4. Sotto **in primo piano**, selezionare **rete virtuale**.

5. Sotto **crea rete virtuale**, immettere i valori mostrati nei campi appropriati nella tabella seguente:

   |Campo  |Valore  |
   |---------|---------|
   |NOME     |Tenant1VNet1         |
   |Spazio degli indirizzi     |10.1.0.0/16|
   |Nome della subnet     |Tenant1-Sub1|
   |Intervallo di indirizzi subnet     |10.1.1.0/24|

6. Dovrebbe essere la sottoscrizione creata in precedenza è stato popolato nel **sottoscrizione** campo. Per i campi rimanenti:

    * Sotto **gruppo di risorse**, selezionare **Crea nuovo** per creare un nuovo gruppo di risorse o se già presente, selezionare **Usa esistente**.
    * Verificare il valore predefinito **posizione**.
    * Fare clic su **Create**(Crea).
    * (Facoltativo) Fare clic su **Aggiungi al dashboard**.

#### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway

1. Sotto **rete virtuale**, selezionare **Tenant1VNet1**.
1. In **Impostazioni** selezionare **Subnet**.
1. Selezionare **+ subnet del Gateway** per aggiungere una subnet del gateway alla rete virtuale.
1. Il nome predefinito della subnet è **GatewaySubnet**. Subnet del gateway sono un caso speciale e deve usare questo nome per funzionare correttamente.
1. Verificare che il **intervallo indirizzi** viene **10.1.0.0/24**.
1. Fare clic su **OK** per creare la subnet del gateway.

#### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale

1. Nel portale per gli utenti Azure Stack, fare clic su **+ crea una risorsa**.
1. Sotto **Azure Marketplace**, selezionare **Networking**.
1. Selezionare **Gateway di rete virtuale** dall'elenco di risorse di rete.
1. Nel **Name** immettere **GW1**.
1. Selezionare **Rete virtuale**.
1. Selezionare **Tenant1VNet1** nell'elenco a discesa.
1. Selezionare **indirizzo IP pubblico**, quindi **Scegli indirizzo IP pubblico**, quindi fare clic su **Crea nuovo**.
1. Nel **Name** , digitare **GW1-PiP**, quindi fare clic su **OK**.
1. Per **Tipo VPN** deve essere selezionata l'opzione **Basato su route** per impostazione predefinita. Mantenere questa impostazione.
1. Verificare che la **Sottoscrizione** e la **Località** siano corrette. Fare clic su **Create**(Crea).

#### <a name="create-the-local-network-gateway"></a>Creare il gateway di rete locale

La risorsa gateway di rete locale identifica il gateway remoto a altra estremità della connessione VPN. Per questo esempio, l'estremità della connessione remota è il sotto-interfaccia LAN del router ExpressRoute. Per Tenant 1, illustrato nella figura 2, l'indirizzo remoto è 10.60.3.255.

1. Accedere al portale utenti di Azure Stack con l'account utente e selezionare **+ crea una risorsa**.
1. Sotto **Azure Marketplace**, selezionare **Networking**.
1. Selezionare **Gateway di rete locale** dall'elenco di risorse.
1. Nel **Name** , digitare **ER-Router-GW**.
1. Per il **indirizzo IP** campo, vedere la figura 2. L'indirizzo IP dell'interfaccia secondarie LAN router ExpressRoute per Tenant 1 è 10.60.3.255. Per il proprio ambiente, immettere l'indirizzo IP dell'interfaccia corrispondente del router.
1. Nel **spazio degli indirizzi** immettere lo spazio degli indirizzi delle reti virtuali che si desidera connettersi a in Azure. Le subnet per Tenant 1 nella *figura 2* sono i seguenti:

   * 192.168.2.0/24 è l'hub di rete virtuale in Azure.
   * 10.100.0.0/16 è lo spoke di rete virtuale in Azure.

   > [!IMPORTANT]
   > Questo esempio si presuppone che si utilizza route statiche per la connessione VPN Site-to-Site tra il gateway di Azure Stack e il router di ExpressRoute.

1. Verificare che il **abbonamento**, **gruppo di risorse**, e **percorso** siano corretti. Selezionare quindi **Crea**.

#### <a name="create-the-connection"></a>Creare la connessione

1. Nel portale per gli utenti Azure Stack, selezionare **+ crea una risorsa**.
1. Sotto **Azure Marketplace**, selezionare **Networking**.
1. Selezionare **Connessione** dall'elenco di risorse.
1. Sotto **nozioni di base**, scegliere **(IPSec) Site-to-site** come il **tipo di connessione**.
1. Selezionare il **abbonamento**, **gruppo di risorse**, e **percorso**. Fare clic su **OK**.
1. Sotto **le impostazioni**, selezionare **gateway di rete virtuale**, quindi selezionare **GW1**.
1. Selezionare **gateway di rete locale**, quindi selezionare **ER Router GW**.
1. Nel **nome connessione** immettere **ConnectToAzure**.
1. Nel **chiave condivisa (PSK)** immettere **abc123** e quindi selezionare **OK**.
1. Sotto **Summary**, selezionare **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Ottenere l'indirizzo IP pubblico del gateway rete virtuale

Dopo aver creato il gateway di rete virtuale è possibile ottenere l'indirizzo IP pubblico del gateway. Prendere nota di questo indirizzo nel caso in cui è necessario in un secondo momento per la distribuzione. A seconda della distribuzione, questo indirizzo viene usato come le **indirizzo IP interno**.

1. Nel portale per gli utenti Azure Stack, selezionare **tutte le risorse**.
1. Sotto **tutte le risorse**, selezionare il gateway di rete virtuale, ovvero **GW1** nell'esempio.
1. Sotto **gateway di rete virtuale**, selezionare **Panoramica** dall'elenco di risorse. In alternativa, è possibile selezionare **proprietà**.
1. L'indirizzo IP che si desidera tenere presente è elencato in **indirizzo IP pubblico**. Per la configurazione di esempio, questo indirizzo è 192.68.102.1.

#### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Per testare il traffico dei dati tramite una connessione VPN, è necessario le macchine virtuali per inviare e ricevere i dati nella rete virtuale di Azure Stack. Creare una macchina virtuale e distribuirlo alla subnet VM per la rete virtuale.

1. Nel portale per gli utenti Azure Stack, selezionare **+ crea una risorsa**.
1. Sotto **Azure Marketplace**, selezionare **Compute**.
1. Nell'elenco di immagini di macchine virtuali, selezionare la **Windows Server 2016 Datacenter Eval** immagine.

   >[!NOTE]
   >Se l'immagine usata per questo articolo non è disponibile, richiedere l'operatore di Azure Stack per fornire un'immagine di Windows Server diversi.

1. Nelle **crea macchina virtuale**, selezionare **nozioni di base**, quindi digitare **VM01** come il **nome**.
1. Immettere un nome utente valido e una password. Si userà questo account per accedere alla macchina virtuale dopo che è stato creato.
1. Fornire una **abbonamento**, **gruppo di risorse**e un **percorso**. Selezionare **OK**.
1. Sotto **Scegli una dimensione**, selezionare una dimensione di macchina virtuale per questa istanza e quindi selezionare **seleziona**.
1. Sotto **impostazioni**, verificare che:

   * La rete virtuale sia **Tenant1VNet1**.
   * La subnet sia impostata su **10.1.1.0/24**.

   Usare le impostazioni predefinite e fare clic su **OK**.

1. Sotto **Summary**, esaminare la configurazione della macchina virtuale e quindi fare clic su **OK**.

Per aggiungere più tenant, ripetere i passaggi eseguiti in queste sezioni:

* [Creare la rete virtuale e subnet VM](#create-the-virtual-network-and-vm-subnet)
* [Creare la subnet del gateway](#create-the-gateway-subnet)
* [Creare il gateway di rete virtuale](#create-the-virtual-network-gateway)
* [Creare il gateway di rete locale](#create-the-local-network-gateway)
* [Creare la connessione](#create-the-connection)
* [Creare una macchina virtuale](#create-a-virtual-machine)

Se si usa Tenant 2, ad esempio, ricordarsi di modificare gli indirizzi IP per evitare sovrapposizioni.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Configurare la macchina virtuale NAT per l'attraversamento gateway

> [!IMPORTANT]
> In questa sezione è Azure Stack Development Kit (ASDK) solo per le distribuzioni. NAT non è necessaria per le distribuzioni a più nodi.

Azure Stack Development Kit è indipendente e isolato dalla rete in cui viene distribuito l'host fisico. La rete VIP che sono connessi i gateway non è esterna; è nascosta dietro un router esecuzione Network Address Translation (NAT).

Il router è una macchina virtuale di Windows Server (AzS-BGPNAT01) che esegue il ruolo di Routing e accesso remoto (RRAS). È necessario configurare NAT nella macchina virtuale per abilitare la connessione VPN site-to-site per connettersi a entrambe le estremità AzS-BGPNAT01.

#### <a name="configure-the-nat"></a>Configurare la NAT

1. Accedere al computer host Azure Stack con l'account amministratore.
1. Copiare e modificare lo script di PowerShell seguente. Sostituire `your administrator password` con la password dell'amministratore e quindi eseguire lo script in un ISE di PowerShell con privilegi elevati. Questo script restituisce i **indirizzi esterni BGPNAT**.

   ```PowerShell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. Per configurare la NAT, copiare e modificare lo script di PowerShell seguente. Modificare lo script per sostituire il `External BGPNAT address` e `Internal IP address` con i valori di esempio seguenti:

   * Per la *indirizzi esterni BGPNAT* usare 10.10.0.62
   * Per la *indirizzo IP interno* usare 192.168.102.1

   Eseguire lo script seguente da un esempio di PowerShell con privilegi elevati ISE:

   ```PowerShell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

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
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
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

Dopo aver completato la configurazione di Azure Stack, è possibile distribuire le risorse di Azure. Nella figura seguente illustra un esempio di una rete virtuale tenant in Azure. È possibile usare qualsiasi nome e lo schema di indirizzamento per la rete virtuale in Azure. Tuttavia, l'intervallo di indirizzi delle reti virtuali in Azure e Azure Stack deve essere univoco e non deve sovrapporsi:

*Figura 3. Reti virtuali di Azure*

![Reti virtuali di Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Le risorse distribuite in Azure sono simili per le risorse distribuite in Azure Stack. Distribuire i componenti seguenti:

* Reti virtuali e subnet
* Una subnet del gateway
* Un gateway di rete virtuale
* Una connessione
* Un circuito ExpressRoute

L'infrastruttura di rete di Azure di esempio è configurato come indicato di seguito:

* Un standard (192.168.2.0/24) modello hub e spoke (10.100.0.0./16) della rete virtuale. Per altre informazioni sulla topologia di rete hub-spoke, vedere [implementare una topologia di rete hub-spoke in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* I carichi di lavoro vengono distribuiti nella rete virtuale spoke e il circuito ExpressRoute è connesso alla rete virtuale dell'hub.
* Le due reti virtuali sono connesse tramite peering reti virtuali.

### <a name="configure-the-azure-vnets"></a>Configurare le reti virtuali di Azure

1. Accedere al portale di Azure con le credenziali di Azure.
1. Creare la rete virtuale hub con l'intervallo di indirizzi 192.168.2.0/24.
1. Creare una subnet con l'intervallo di indirizzi 192.168.2.0/25 e aggiungere una subnet del gateway con l'intervallo di indirizzi 192.168.2.128/27.
1. Creare lo spoke intervallo di indirizzi della rete virtuale e subnet usando il 10.100.0.0/16.

Per altre informazioni sulla creazione di reti virtuali in Azure, vedere [creare una rete virtuale](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Configurare un circuito ExpressRoute

1. Esaminare i prerequisiti di ExpressRoute in [ExpressRoute prerequisiti & elenco di controllo](../expressroute/expressroute-prerequisites.md).

1. Seguire i passaggi descritti in [creare e modificare un circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) per creare un circuito ExpressRoute usando la sottoscrizione di Azure.

   >[!NOTE]
   >Assegnare alla chiave di servizio per il circuito al servizio in modo che è possibile configurare il circuito ExpressRoute alla fine.

1. Seguire i passaggi descritti in [creare e modificare i peering per un circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) per configurare il peering privato sul circuito ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale

Seguire i passaggi descritti in [configurare un gateway di rete virtuale per ExpressRoute usando PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) per creare un gateway di rete virtuale per ExpressRoute nella rete virtuale dell'hub.

### <a name="create-the-connection"></a>Creare la connessione

Per creare un collegamento al circuito ExpressRoute alla rete virtuale dell'hub, seguire i passaggi descritti in [connettere una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Eseguire il peering delle reti virtuali

Eseguire il peering di hub e spoke di reti virtuali usando la procedura descritta in [creare un peering reti virtuali usando il portale di Azure](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Quando si configura il peering della rete virtuale, assicurarsi di che usare le opzioni seguenti:

* Dall'hub per lo spoke **Consenti transito gateway**.
* Dallo spoke all'hub **Usa gateway remoti**.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Distribuire le macchine virtuali del carico di lavoro nella rete virtuale spoke.

Ripetere questi passaggi per qualsiasi tenant aggiuntivo, le reti virtuali si vuole connettere in Azure tramite i rispettivi circuiti di ExpressRoute.

## <a name="configure-the-router"></a>Configurare il router

È possibile utilizzare il diagramma di configurazione di router ExpressRoute seguente come guida per la configurazione di ExpressRoute Router. Questo diagramma mostra due tenant (Tenant 1 e 2 Tenant) con i rispettivi circuiti di ExpressRoute. Ogni tenant è collegato al proprio VRF (Routing virtuale e l'inoltro) sul lato LAN e WAN del router ExpressRoute. Questa configurazione garantisce un isolamento end-to-end tra i due tenant. Prendere nota degli indirizzi IP usati nelle interfacce di router mentre si esegue l'esempio di configurazione.

*Figura 4. Configurazione di router ExpressRoute*

![Configurazione di router ExpressRoute](media/azure-stack-connect-expressroute/EndToEnd.png)

È possibile usare qualsiasi router che supporta VPN IKEv2 e BGP per terminare la connessione VPN site-to-site dallo Stack di Azure. Il router stesso viene usato per connettersi ad Azure tramite un circuito ExpressRoute.

Il seguente esempio di configurazione di Router servizi aggregazione di serie 1000 ASR Cisco supporta l'infrastruttura di rete visualizzato nel *configurazione del router ExpressRoute* diagramma.

```shell
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
 description PRIMARY ExpressRoute Link to AZURE over Equinix
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
 description BACKUP ExpressRoute Link to AZURE over Equinix
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

Testare la connessione dopo aver stabilito la connessione site-to-site e il circuito ExpressRoute.

Eseguire i test di ping seguenti:

* Accedere a una delle macchine virtuali nella rete virtuale di Azure ed effettuare il ping alla macchina virtuale creata in Azure Stack.
* Accedere a una delle macchine virtuali che è stato creato in Azure Stack e ping alla macchina virtuale creata nella rete virtuale di Azure.

>[!NOTE]
>Per assicurarsi che si sta inviando traffico sul Site-to-Site e le connessioni ExpressRoute, è necessario effettuare il ping non l'indirizzo VIP della macchina virtuale e l'indirizzo IP (DIP) dedicato della macchina virtuale su entrambe le estremità.

### <a name="allow-icmp-in-through-the-firewall"></a>Consentire il traffico ICMP in attraverso il firewall

Per impostazione predefinita, Windows Server 2016 non supporta i pacchetti ICMP in entrata attraverso il firewall. Per ogni macchina virtuale che usi per i test di ping, è necessario consentire i pacchetti ICMP in ingresso. Per creare una regola del firewall per ICMP, eseguire il cmdlet seguente in una finestra di PowerShell con privilegi elevata:

```PowerShell
# Create ICMP firewall rule.
New-NetFirewallRule `
  –DisplayName “Allow ICMPv4-In” `
  –Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Effettuare il ping la macchina virtuale di Azure Stack

1. Accedere al portale utenti Azure Stack usando un account tenant.

1. Trovare la macchina virtuale che è stato creato e selezionare la macchina virtuale.

1. Selezionare **Connessione**.

1. Da un prompt con privilegi elevati Windows o PowerShell, immettere **ipconfig /all**. Annotare l'indirizzo IPv4 restituito nell'output.

1. Effettuare il ping dell'indirizzo IPv4 della macchina virtuale nella rete virtuale di Azure.

   Nell'ambiente di esempio, l'indirizzo IPv4 è dalla subnet 10.1.1.x/24. Nell'ambiente in uso, l'indirizzo potrebbe essere diverso, ma deve essere nella subnet creata per il tenant di subnet della rete virtuale.

### <a name="view-data-transfer-statistics"></a>Visualizzare le statistiche di trasferimento dei dati

Se si desidera conoscere la quantità di traffico che passa attraverso la connessione, è possibile trovare queste informazioni nel portale utenti Azure Stack. Questo è un buon metodo per sapere se i dati di test di ping è verificato un errore tramite le connessioni ExpressRoute e VPN:

1. Accedere al portale utenti Azure Stack usando l'account tenant e selezionare **tutte le risorse**.
1. Passare al gruppo di risorse per il Gateway VPN e selezionare il **connessione** tipo di oggetto.
1. Selezionare il **ConnectToAzure** connessione dall'elenco.
1. Sotto **connessioni** > **Panoramica**, è possibile visualizzare le statistiche per **dati** e **dati in uscita**. Si noterà che alcuni valori diversi da zero.

   ![I dati In e i dati in uscita](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire le App in Azure e Azure Stack](azure-stack-solution-pipeline.md)
