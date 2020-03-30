---
title: Risolvere i problemi di peering di rete virtuale
description: Passaggi per risolvere la maggior parte dei problemi di peering della rete virtuale.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 1fddbe908ccebc1384dcccde0810366f1a6d5da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796243"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Risolvere i problemi di peering di rete virtuale

Questa guida alla risoluzione dei problemi illustra la procedura per risolvere la maggior parte dei problemi di peering della [rete virtuale.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)

![Diagramma del peering della rete virtuale](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configurare il peering di rete virtuale tra due reti virtualiConfigure virtual network peering between two virtual networks

Le reti virtuali si trovano nella stessa sottoscrizione o in sottoscrizioni diverse?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Le reti virtuali si trovano nella stessa sottoscrizioneThe virtual networks are in the same subscription

Per configurare il peering di rete virtuale per le reti virtuali che si trovano nella stessa sottoscrizione, usare i metodi negli articoli seguenti:To configure virtual network peering for the virtual networks that are in the same subscription, use the methods in the following articles:

* Se le reti virtuali si trovano nella *stessa area*, vedere Creare [un peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Se le reti virtuali si trovano in *aree diverse,* vedere [Peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> La connettività non funziona su un peering di rete virtuale globale per le risorse seguenti:Connectivity doesn't work over global virtual network peering for the following resources: 
>
> * Macchine virtuali (VM) dietro lo SKU del servizio di bilanciamento del carico interno (ILB) di baseVirtual machines (VMs) behind Basic internal load balancer (ILB) SKU
> * Cache Redis (utilizza lo SKU ILB di base)
> * Gateway applicazione (utilizza lo SKU ILB di base)
> * Set di scalabilità di macchine virtuali (usa sKU ILB di base)Virtual machine scale sets (uses Basic ILB SKU)
> * Azure Service Fabric clusters (uses Basic ILB SKU)
> * SQL Server Always On (utilizza sKU ILB di base)
> * Azure App Service Environment for PowerApps (uses Basic ILB SKU)
> * Azure API Management (uses Basic ILB SKU)
> * Azure Active Directory Domain Services (Azure AD DS) (uses Basic ILB SKU)

Per altre informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Le reti virtuali si trovano in sottoscrizioni diverse o tenant di Active Directory

Per configurare il peering della rete virtuale per le reti virtuali in sottoscrizioni diverse o tenant di Active Directory, vedere [Creare peering in sottoscrizioni diverse per l'interfaccia della riga di comando](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)di Azure.To configure virtual network peering for virtual networks in different subscriptions or Active Directory tenants, see Create peering in different subscriptions for Azure CLI.

> [!Note]
> Per configurare il peering di rete, è necessario disporre delle autorizzazioni **Collaboratore** di rete in entrambe le sottoscrizioni. Per ulteriori informazioni, vedere [Peering permissions](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configurare il peering della rete virtuale con la topologia hub-spoke che usa le risorse localiConfigure virtual network peering with hub-spoke topology that uses on-premises resources

![Diagramma del peering della rete virtuale con spoke locale](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Per una connessione da sito a sito o a una connessione ExpressRouteFor a site-to-site connection or an ExpressRoute connection

Seguire i passaggi descritti in: Configurare il transito del gateway VPN per il [peering della rete virtuale.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)

### <a name="for-point-to-site-connections"></a>Per connessioni da punto a sito

1. Seguire i passaggi descritti in: Configurare il transito del gateway VPN per il [peering della rete virtuale.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)
2. Dopo aver stabilito o modificato il peering della rete virtuale, scaricare e reinstallare il pacchetto da punto a sito in modo che i client da punto a sito ottono le route aggiornate alla rete virtuale spoke.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configurare il peering della rete virtuale con la rete virtuale della topologia hub-spokeConfigure virtual network peering with hub-spoke topology virtual network

![Diagramma del peering di rete virtuale con una rete virtuale spoke](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Le reti virtuali si trovano nella stessa area


1. Nella rete virtuale dell'hub configurare un'appliance virtuale di rete (NVA).
1. Nelle reti virtuali spoke, disporre di route definite dall'utente con il tipo di hop successivo "appliance virtuale di rete" applicata.

Per ulteriori informazioni, vedere [Concatenamento dei](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)servizi .

> [!Note]
> Se è necessaria assistenza per configurare un'applicazione file di rete, [contattare il fornitore dell'evento di rete.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

Per informazioni sulla risoluzione dei problemi relativi alla configurazione e al routing dei dispositivi NVA, vedere Problemi relativi alle appliance virtuali di rete in Azure.For help with troubleshooting the NVA device setup and routing, see [Network virtual appliance issues in Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)

### <a name="the-virtual-networks-are-in-different-regions"></a>Le reti virtuali si trovano in aree diverse

Il transito sulla rete virtuale globale è ora supportato. La connettività non funziona su peering di rete virtuale globale per le risorse seguenti:Connectivity does not work over global virtual network peering for the following resources:

* Macchine virtuali dietro lo SKU ILB di base
* Cache Redis (utilizza lo SKU ILB di base)
* Gateway applicazione (utilizza lo SKU ILB di base)
* Set di scalabilità (utilizza SKU ILB di base)Scale sets (uses Basic ILB SKU)
* Service Fabric clusters (uses Basic ILB SKU)
* SQL Server Always On (utilizza sKU ILB di base)
* App Service Environment (uses Basic ILB SKU)
* Gestione API (utilizza lo SKU ILB di base)
* Azure AD DS (uses Basic ILB SKU)

Per altre informazioni sui requisiti e i vincoli di peering globale, vedere [Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)di rete virtuale .

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Risolvere un problema di connettività tra due reti virtuali con peeredTroubleshoot a connectivity issue between two peered virtual networks

Accedere al [portale](https://portal.azure.com/) di Azure con un account con i [ruoli e le autorizzazioni](virtual-network-manage-peering.md#permissions)necessari. Selezionare la rete virtuale, selezionare **Peering**, quindi controllare il campo **Stato** . Qual è lo stato?

### <a name="the-peering-status-is-connected"></a>Lo stato di peering è "Connesso"

Per risolvere il problema:

1. Controllare i flussi di traffico di rete:

   Usare [risoluzione dei problemi](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) di connessione e la verifica del flusso [IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) dalla macchina virtuale di origine alla macchina virtuale di destinazione per determinare se è presente un gruppo di sicurezza di rete o un UDR che causa interferenze nei flussi di traffico.

   Se si utilizza un firewall o un'unità NVA: 
   1. Documentare i parametri UDR in modo da poterli ripristinare al termine di questo passaggio.
   2. Rimuovere l'UDR dalla subnet della macchina virtuale di origine o dalla scheda di interfaccia di rete che punta all'nVA come hop successivo. Verificare la connettività dalla macchina virtuale di origine direttamente alla destinazione che ignora l'nVA. Se questo passaggio non funziona, vedere lo strumento di [risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)di NVA .

2. Eseguire una traccia di rete:Take a network trace: 
   1. Avviare una traccia di rete nella macchina virtuale di destinazione. Per Windows, è possibile utilizzare **Netsh**. Per Linux, utilizzare **TCPDump**.
   2. Eseguire **TcpPing** o **PsPing** dall'origine all'IP di destinazione.

      Di conseguenza, è un esempio di comando **TcpPing:This** is an example of a TcpPing command:`tcping64.exe -t <destination VM address> 3389`

   3. Al termine del **TcpPing,** arrestare la traccia di rete nella destinazione.
   4. Se i pacchetti arrivano dall'origine, non vi è alcun problema di rete. Esaminare sia il firewall della macchina virtuale che l'applicazione in ascolto su tale porta per individuare il problema di configurazione.

   > [!Note]
   > Non è possibile connettersi ai tipi di risorse seguenti tramite il peering di rete virtuale globale (reti virtuali in aree diverse):You can't connect to the following resource types over global virtual network peering (virtual networks in different regions):
   >
   > * Macchine virtuali dietro lo SKU ILB di base
   > * Cache Redis (utilizza lo SKU ILB di base)
   > * Gateway applicazione (utilizza lo SKU ILB di base)
   > * Set di scalabilità (utilizza SKU ILB di base)Scale sets (uses Basic ILB SKU)
   > * Service Fabric clusters (uses Basic ILB SKU)
   > * SQL Server Always On (utilizza sKU ILB di base)
   > * App Service Environment (uses Basic ILB SKU)
   > * Gestione API (utilizza lo SKU ILB di base)
   > * Azure AD DS (uses Basic ILB SKU)

Per altre informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale.

### <a name="the-peering-status-is-disconnected"></a>Lo stato del peering è "Disconnesso"

Per risolvere questo problema, eliminare il peering da entrambe le reti virtuali e quindi ricrearli.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Risolvere un problema di connettività tra una rete virtuale hub-spoke e una risorsa localeTroubleshoot a connectivity issue between a hub-spoke virtual network and an on-premises resource

La rete utilizza un gateway NVA o VPN di terze parti?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>La rete utilizza un gateway NVA o VPN di terze parti

Per risolvere i problemi di connettività che interessano un gateway NVA o VPN di terze parti, vedere gli articoli seguenti:

* [Risoluzione dei problemi relativi all'NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Concatenamento dei servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>La rete non utilizza un gateway NVA o VPN di terze parti

La rete virtuale hub e la rete virtuale spoke dispongono di un gateway VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Sia la rete virtuale hub che la rete virtuale spoke dispongono di un gateway VPN

L'utilizzo di un gateway remoto non è supportato.

Se la rete virtuale spoke dispone già di un gateway VPN, l'opzione **Usa gateway remoto** non è supportata nella rete virtuale spoke. Ciò è dovuto a una limitazione del peering della rete virtuale.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Sia la rete virtuale hub che la rete virtuale spoke non dispongono di un gateway VPN

Per le connessioni da sito a sito o Azure ExpressRoute, verificare le cause principali seguenti dei problemi di connettività alla rete virtuale remota da locale:For site-to-site or Azure ExpressRoute connections, check the following primary causes of connectivity issues to the remote virtual network from on-premises:

* Nella rete virtuale che dispone di un gateway verificare che la casella di controllo **Consenti traffico inoltrato** sia selezionata.
* Nella rete virtuale che non dispone di un gateway verificare che la casella di controllo **Usa gateway remoto** sia selezionata.
* Fare in modo che l'amministratore di rete controlli i dispositivi locali per verificare che tutti dispongano dello spazio di indirizzi della rete virtuale remota.

Per le connessioni da punto a sito:

* Nella rete virtuale che dispone di un gateway verificare che la casella di controllo **Consenti traffico inoltrato** sia selezionata.
* Nella rete virtuale che non dispone di un gateway verificare che la casella di controllo **Usa gateway remoto** sia selezionata.
* Scaricare e reinstallare il pacchetto client da punto a sito. Le route di rete virtuale di cui viene eseguito il peered di recente non aggiungono automaticamente route ai client da punto a sito.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Risolvere un problema di connettività di rete hub-spoke tra reti virtuali spoke nella stessa areaTroubleshoot a hub-spoke network connectivity issue between spoke virtual networks in the same region

Una rete hub deve includere un'nVA. Configurare gli UDR negli spoke con un'unità di gestione guidata come hop successivo e abilitare **Consenti traffico inoltrato** nella rete virtuale dell'hub.

Per ulteriori informazioni, vedere [Concatenamento dei](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)servizi e discutere di questi requisiti con il [fornitore dell'account di](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) rete NVA desiderato.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Risolvere un problema di connettività di rete hub-spoke tra reti virtuali spoke in aree diverseTroubleshoot a hub-spoke network connectivity issue between spoke virtual networks in different regions

Il transito sulla rete virtuale globale è ora supportato. La connettività non funziona su un peering di rete virtuale globale per le risorse seguenti:Connectivity doesn't work over global virtual network peering for the following resources:

* Macchine virtuali dietro lo SKU ILB di base
* Cache Redis (utilizza lo SKU ILB di base)
* Gateway applicazione (utilizza lo SKU ILB di base)
* Set di scalabilità (utilizza SKU ILB di base)Scale sets (uses Basic ILB SKU)
* Service Fabric clusters (uses Basic ILB SKU)
* SQL Server Always On (utilizza sKU ILB di base)
* App Service Environment (uses Basic ILB SKU)
* Gestione API (utilizza lo SKU ILB di base)
* Azure AD DS (uses Basic ILB SKU)

Per ulteriori informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale e delle [diverse topologie VPN.](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Risolvere un problema di connettività di rete hub-spoke tra un'app Web e la rete virtuale spoke

Per risolvere il problema:

1. Accedere al portale di Azure. 
1. Nell'app Web selezionare **rete**, quindi selezionare **Integrazione della rete virtuale**.
1. Verificare se è possibile visualizzare la rete virtuale remota. Immettere manualmente lo spazio di indirizzi della rete virtuale remota (**Sincronizza rete** e **Aggiungi route**).

Per altre informazioni, vedere gli articoli seguenti:

* [Integrare l'app con una rete virtuale di AzureIntegrate your app with an Azure virtual network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Informazioni sul routing VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Risolvere i problemi relativi a un messaggio di errore di configurazione peering di rete virtualeTroubleshoot a virtual network peering configuration error message 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Il `<TENANT ID>` tenant corrente non è autorizzato ad accedere alla sottoscrizione collegata

Per risolvere questo problema, vedere Creare peering - interfaccia della riga di comando di [Azure.To](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)resolve this issue, see Create peering - Azure CLI .

### <a name="not-connected"></a>Non connesso

Per risolvere questo problema, eliminare il peering da entrambe le reti virtuali e quindi ricrearli.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Impossibile eseguire il peernetworko di una rete virtuale Databricks

Per risolvere questo problema, configurare il peering della rete virtuale in **Azure Databricks**, quindi specificare la rete virtuale di destinazione utilizzando **Resource ID**. Per altre informazioni, vedere Eseguire il [peerare una rete virtuale Databricks a una rete virtuale remota.](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di connettività tra macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
