---
title: Risolvere i problemi di peering di rete virtuale
description: Passaggi per risolvere la maggior parte dei problemi relativi al peering di rete virtuale.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 0898a65323957cbab4c2ab5278e9970cf0c16a90
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219232"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Risolvere i problemi di peering di rete virtuale

Questa guida alla risoluzione dei problemi illustra la procedura per risolvere la maggior parte dei problemi relativi al [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![Diagramma del peering di rete virtuale](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configurare il peering di rete virtuale tra due reti virtuali

Le reti virtuali si trovano nella stessa sottoscrizione o in sottoscrizioni diverse?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Le reti virtuali si trovano nella stessa sottoscrizione

Per configurare il peering di rete virtuale per le reti virtuali che si trovano nella stessa sottoscrizione, usare i metodi descritti negli articoli seguenti:

* Se le reti virtuali si trovano nella *stessa area*, vedere [creare un peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Se le reti virtuali si trovano nelle *diverse aree*, vedere [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> La connettività non funziona sul peering di rete virtuale globale per le risorse seguenti: 
>
> * Macchine virtuali (VM) dietro lo SKU del servizio di bilanciamento del carico interno Basic (ILB)
> * Cache Redis (USA lo SKU ILB Basic)
> * Gateway applicazione (USA lo SKU ILB Basic)
> * Set di scalabilità di macchine virtuali (USA lo SKU ILB Basic)
> * Cluster di Azure Service Fabric (USA lo SKU ILB Basic)
> * SQL Server Always On (USA lo SKU ILB Basic)
> * Ambiente del servizio app di Azure per PowerApps (USA lo SKU ILB Basic)
> * Gestione API di Azure (USA lo SKU ILB Basic)
> * Azure Active Directory Domain Services (Azure AD DS) (USA lo SKU ILB Basic)

Per ulteriori informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Le reti virtuali si trovano in sottoscrizioni o Active Directory tenant diversi

Per configurare il peering di rete virtuale per le reti virtuali in sottoscrizioni diverse o Active Directory tenant, vedere [creare un peering in sottoscrizioni diverse per l'interfaccia](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)della riga di comando di Azure.

> [!Note]
> Per configurare il peering di rete, è necessario disporre delle autorizzazioni di **collaboratore rete** in entrambe le sottoscrizioni. Per altre informazioni, vedere [autorizzazioni per il peering](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configurare il peering di rete virtuale con la topologia hub-spoke che usa le risorse locali

![Diagramma del peering di rete virtuale con spoke locale](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Per una connessione da sito a sito o una connessione ExpressRoute

Attenersi alla procedura descritta in: [Configurare il transito del gateway VPN per il peering di rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>Per le connessioni da punto a sito

1. Attenersi alla procedura descritta in: [Configurare il transito del gateway VPN per il peering di rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Dopo la creazione o la modifica del peering di rete virtuale, scaricare e reinstallare il pacchetto da punto a sito in modo che i client da punto a sito ottengano le route aggiornate alla rete virtuale spoke.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configurare il peering di rete virtuale con la rete virtuale della topologia hub-spoke

![Diagramma del peering di rete virtuale con una rete virtuale spoke](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Le reti virtuali si trovano nella stessa area


1. Nella rete virtuale Hub configurare un'appliance virtuale di rete.
1. Nelle reti virtuali spoke, avere route definite dall'utente con il tipo di hop successivo applicato "appliance virtuale di rete".

Per ulteriori informazioni, vedere [concatenamento dei servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Se è necessaria assistenza per configurare un'appliance virtuale di sistema, [contattare il fornitore dell'](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)appliance virtuale di appliance.

Per informazioni sulla risoluzione dei problemi relativi all'installazione e al routing del dispositivo di [appliance virtuale di rete, vedere problemi di appliance virtuali di rete in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>Le reti virtuali si trovano in aree geografiche diverse

Il transito sul peering di rete virtuale globale è ora supportato. La connettività non funziona sul peering di rete virtuale globale per le risorse seguenti:

* VM dietro lo SKU Basic ILB
* Cache Redis (USA lo SKU ILB Basic)
* Gateway applicazione (USA lo SKU ILB Basic)
* Set di scalabilità (USA lo SKU ILB Basic)
* Cluster Service Fabric (USA lo SKU ILB Basic)
* SQL Server Always On (USA lo SKU ILB Basic)
* Ambiente del servizio app (USA lo SKU ILB Basic)
* Gestione API (USA lo SKU ILB Basic)
* Azure AD DS (USA lo SKU ILB Basic)

Per ulteriori informazioni sui requisiti e i vincoli di peering globali, vedere [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Risolvere un problema di connettività tra due reti virtuali con peering

Accedere al [portale di Azure](https://portal.azure.com/) con un account che disponga dei [ruoli e delle autorizzazioni](virtual-network-manage-peering.md#permissions)necessari. Selezionare la rete virtuale, selezionare **peering**, quindi controllare il campo **stato** . Qual è lo stato?

### <a name="the-peering-status-is-connected"></a>Lo stato del peering è "Connected"

Per risolvere il problema:

1. Controllare i flussi del traffico di rete:

   Usare la [risoluzione dei problemi di connessione](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) e la [Verifica del flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) dalla VM di origine alla macchina virtuale di destinazione per determinare se esiste un NSG o UdR che causa un'interferenza nei flussi di traffico.

   Se si usa un firewall o un appliance virtuale di dispositivo: 
   1. Documentare i parametri UDR in modo che sia possibile ripristinarli al termine di questo passaggio.
   2. Rimuovere il UDR dalla subnet VM di origine o dalla scheda di interfaccia di rete che fa riferimento all'appliance virtuale di rete come hop successivo. Verificare la connettività dalla VM di origine direttamente alla destinazione che ignora l'appliance virtuale di dispositivo. Se questo passaggio non funziona, vedere la pagina relativa alla [risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)di appliance virtuale di.

2. Eseguire una traccia di rete: 
   1. Avviare una traccia di rete nella macchina virtuale di destinazione. Per Windows, è possibile usare **netsh**. Per Linux, usare **TCPDump**.
   2. Eseguire **TcpPing** o **PsPing** dall'origine all'indirizzo IP di destinazione.

      Di seguito è riportato un esempio di comando **TcpPing** :`tcping64.exe -t <destination VM address> 3389`

   3. Al termine del **TcpPing** , arrestare la traccia di rete nella destinazione.
   4. Se i pacchetti arrivano dall'origine, non si verifica alcun problema di rete. Esaminare il firewall della macchina virtuale e l'applicazione in ascolto su tale porta per individuare il problema di configurazione.

   > [!Note]
   > Non è possibile connettersi ai tipi di risorse seguenti tramite il peering di rete virtuale globale (reti virtuali in aree diverse):
   >
   > * VM dietro lo SKU Basic ILB
   > * Cache Redis (USA lo SKU ILB Basic)
   > * Gateway applicazione (USA lo SKU ILB Basic)
   > * Set di scalabilità (USA lo SKU ILB Basic)
   > * Cluster Service Fabric (USA lo SKU ILB Basic)
   > * SQL Server Always On (USA lo SKU ILB Basic)
   > * Ambiente del servizio app (USA lo SKU ILB Basic)
   > * Gestione API (USA lo SKU ILB Basic)
   > * Azure AD DS (USA lo SKU ILB Basic)

Per ulteriori informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale.

### <a name="the-peering-status-is-disconnected"></a>Lo stato del peering è "Disconnected"

Per risolvere questo problema, eliminare il peering da entrambe le reti virtuali e quindi ricrearli.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Risolvere un problema di connettività tra una rete virtuale hub-spoke e una risorsa locale

La rete usa un'appliance virtuale di rete di terze parti o un gateway VPN?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>La rete usa un'appliance virtuale di rete di terze parti o un gateway VPN

Per risolvere i problemi di connettività che interessano un'appliance virtuale di rete o un gateway VPN di terze parti, vedere gli articoli seguenti:

* [Risoluzione dei problemi di appliance appliance di dispositivo](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Concatenamento di servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>La rete non usa un'appliance virtuale di rete o un gateway VPN di terze parti

La rete virtuale dell'hub e la rete virtuale spoke hanno un gateway VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Sia la rete virtuale Hub che la rete virtuale spoke hanno un gateway VPN

L'uso di un gateway remoto non è supportato.

Se la rete virtuale spoke dispone già di un gateway VPN, l'opzione **use Remote Gateway** non è supportata nella rete virtuale spoke. A causa di una limitazione del peering di rete virtuale.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Sia la rete virtuale Hub che la rete virtuale spoke non dispongono di un gateway VPN

Per le connessioni da sito a sito o Azure ExpressRoute, verificare le seguenti cause principali di problemi di connettività alla rete virtuale remota da locale:

* Nella rete virtuale con un gateway verificare che sia selezionata la casella di controllo **Consenti traffico trasmesso** .
* Nella rete virtuale che non dispone di un gateway, verificare che la casella di controllo **Usa gateway remoto** sia selezionata.
* Chiedere all'amministratore di rete di controllare i dispositivi locali per verificare che tutti dispongano dello spazio di indirizzi della rete virtuale remota.

Per le connessioni da punto a sito:

* Nella rete virtuale con un gateway verificare che sia selezionata la casella di controllo **Consenti traffico trasmesso** .
* Nella rete virtuale che non dispone di un gateway, verificare che la casella di controllo **Usa gateway remoto** sia selezionata.
* Scaricare e reinstallare il pacchetto client da punto a sito. Le route di rete virtuale di cui è stato appena creato il peering non aggiungono automaticamente le route ai client da punto a sito.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Risolvere un problema di connettività di rete hub-spoke tra reti virtuali spoke nella stessa area

Una rete hub deve includere un'appliance virtuale di rete. Configurare UdR in spoke che hanno un appliance virtuale di rete impostato come hop successivo e abilitare **Consenti il traffico in avanti** nella rete virtuale dell'hub.

Per altre informazioni, vedere [concatenamento dei servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)e discutere di questi requisiti con il [Fornitore](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) dell'appliance virtuale di rete di propria scelta.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Risolvere un problema di connettività di rete hub-spoke tra reti virtuali spoke in aree diverse

Il transito sul peering di rete virtuale globale è ora supportato. La connettività non funziona sul peering di rete virtuale globale per le risorse seguenti:

* VM dietro lo SKU Basic ILB
* Cache Redis (USA lo SKU ILB Basic)
* Gateway applicazione (USA lo SKU ILB Basic)
* Set di scalabilità (USA lo SKU ILB Basic)
* Cluster Service Fabric (USA lo SKU ILB Basic)
* SQL Server Always On (USA lo SKU ILB Basic)
* Ambiente del servizio app (USA lo SKU ILB Basic)
* Gestione API (USA lo SKU ILB Basic)
* Azure AD DS (USA lo SKU ILB Basic)

Per ulteriori informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale e delle [diverse topologie VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Risolvere un problema di connettività di rete hub-spoke tra un'app Web e la rete virtuale spoke

Per risolvere il problema:

1. Accedere al portale di Azure. 
1. Nell'app Web selezionare **rete**e quindi selezionare **integrazione VNet**.
1. Controllare se è possibile visualizzare la rete virtuale remota. Immettere manualmente lo spazio di indirizzi della rete virtuale remota (**sincronizzazione rete** e **Aggiungi route**).

Per altre informazioni, vedere gli articoli seguenti:

* [Integrare l'app con una rete virtuale di Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Informazioni sul routing VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Risolvere i problemi di un messaggio di errore di configurazione del peering di rete virtuale 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Il tenant `<TENANT ID>` corrente non è autorizzato ad accedere alla sottoscrizione collegata

Per risolvere questo problema, vedere [creare un peering-interfaccia](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)della riga di comando di Azure.

### <a name="not-connected"></a>Non connesso

Per risolvere questo problema, eliminare il peering da entrambe le reti virtuali e quindi ricrearli.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Non è stato possibile eseguire il peering di una rete virtuale databricks

Per risolvere questo problema, configurare il peering di rete virtuale in **Azure Databricks**e quindi specificare la rete virtuale di destinazione usando l' **ID risorsa**. Per ulteriori informazioni, vedere la pagina relativa [alla rete virtuale peer a databricks in una rete virtuale remota](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di connettività tra macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
