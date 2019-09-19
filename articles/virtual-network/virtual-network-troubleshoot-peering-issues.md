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
ms.openlocfilehash: 68d4f55d4a382f59386e72779a5f60cfc2a65338
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091109"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Risolvere i problemi di peering di rete virtuale

Questa guida alla risoluzione dei problemi illustra la procedura per risolvere la maggior parte dei problemi relativi al [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![IMMAGINE](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>Scenario 1: Configurare il peering di rete virtuale tra due reti virtuali

Le reti virtuali si trovano nella stessa sottoscrizione o in sottoscrizioni diverse?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>Tipo di connessione 1: Le reti virtuali si trovano nella stessa sottoscrizione

Per configurare il peering di rete virtuale per le reti virtuali che si trovano nella stessa sottoscrizione, usare i metodi forniti negli articoli seguenti, in base alle esigenze:

* Se le reti virtuali si trovano nella **stessa area**, seguire i passaggi per [creare un peering per le reti virtuali nella stessa sottoscrizione](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Se le reti virtuali si trovano nelle **diverse aree**, seguire i passaggi per configurare il [peering di rete virtuale globale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> La connettività non funzionerà sul peering VNet globale per le risorse seguenti: 
>
> * VM dietro lo SKU Basic ILB
> * Cache Redis (USA lo SKU ILB Basic)
> * Gateway applicazione (USA lo SKU ILB Basic)
> * Set di scalabilità (USA lo SKU ILB Basic)
> * Cluster Service Fabric (USA lo SKU ILB Basic)
> * SQL always-on (USA lo SKU ILB Basic)
> * Ambienti del servizio app (con SKU ILB Basic)
> * Gestione API (USA lo SKU ILB Basic)
> * Azure Active Directory Domain Service (aggiunge) (USA lo SKU ILB Basic)

Per ulteriori informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale.

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>Tipo di connessione 2: Le reti virtuali si trovano in sottoscrizioni o tenant di Active Directory diversi

Per configurare il peering di rete virtuale per le reti virtuali in sottoscrizioni o Active Directory tenant diversi, seguire i passaggi descritti in [creare un peering in sottoscrizioni diverse per l'interfaccia](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)della riga di comando di Azure.

> [!Note]
> Per configurare il peering di rete, è necessario disporre delle autorizzazioni di **collaboratore rete** in entrambe le sottoscrizioni. Per altre informazioni, vedere [autorizzazioni per il peering](virtual-network-manage-peering.md#permissions).

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Scenario 2: Configurare il peering di rete virtuale con la topologia hub-spoke che usa le risorse locali

![IMMAGINE](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>Tipo di connessione 1: Per la connessione da sito a sito o ExpressRoute

Attenersi alla procedura descritta in: [Configurare il transito del gateway VPN per il peering di rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="connection-type-2-for-point-to-site-connections"></a>Tipo di connessione 2: Per le connessioni da punto a sito

1. Attenersi alla procedura descritta in: [Configurare il transito del gateway VPN per il peering di rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Dopo la creazione o la modifica del peering di rete virtuale, è necessario scaricare e installare di nuovo il pacchetto da punto a sito per consentire ai client da punto a sito di ottenere le route aggiornate alla rete virtuale spoke.

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>Scenario 3: Configurare il peering di rete virtuale con la topologia hub-spoke per la rete virtuale di Azure

![IMMAGINE](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>Tipo di connessione 1: Le reti virtuali si trovano nella stessa area

È necessario configurare un'appliance virtuale di rete nella rete virtuale dell'hub e le route definite dall'utente con l'hop successivo "appliance virtuale di rete" applicata nelle reti virtuali spoke. Per ulteriori informazioni, vedere [concatenamento dei servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Se è necessaria assistenza per configurare un'appliance virtuale di sistema, [contattare il fornitore dell'](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)appliance virtuale di appliance.

Per informazioni sulla risoluzione dei problemi relativi all'installazione e al routing del dispositivo di [appliance virtuale di rete, vedere problemi di appliance virtuali di rete in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>Tipo di connessione 2: Le reti virtuali si trovano in aree geografiche diverse

Il transito sul peering VNet globale è ora supportato. La connettività non funziona sul peering VNet globale per le risorse seguenti:

* VM dietro lo SKU Basic ILB
* Cache Redis (USA lo SKU ILB Basic)
* Gateway applicazione (USA lo SKU ILB Basic)
* Set di scalabilità (USA lo SKU ILB Basic)
* Cluster Service Fabric (USA lo SKU ILB Basic)
* SQL always-on (USA lo SKU ILB Basic)
* Ambienti del servizio app (con SKU ILB Basic)
* Gestione API (USA lo SKU ILB Basic)
* Azure Active Directory Domain Service (aggiunge) (USA lo SKU ILB Basic)

Per ulteriori informazioni sui requisiti e i vincoli di peering globali, vedere [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>Scenario 4: Si è riscontrato un problema di connettività tra due reti virtuali con peering

Accedere al [portale di Azure](https://portal.azure.com/) con un account che disponga dei [ruoli e delle autorizzazioni](virtual-network-manage-peering.md#permissions)necessari. Selezionare la rete virtuale, selezionare **peering**, quindi controllare il campo **stato** . Qual è lo stato?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>Tipo di connessione 1: Lo stato del peering Mostra ' Connected '

Per risolvere il problema, attenersi alla seguente procedura:

1. Controllare i flussi del traffico di rete:

   Usare la [risoluzione dei problemi di connessione](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) e la [Verifica del flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) dalla VM di origine alla macchina virtuale di destinazione per determinare se esiste un NSG o UdR che causa un'interferenza nei flussi di traffico.

   Se si usa un firewall o un appliance di appliance virtuale di dispositivi, seguire questa procedura: 
   1. Documentare i parametri UDR in modo da poterli ripristinare dopo il completamento di questo passaggio.
   2. Rimuovere il UDR dalla subnet VM di origine o dalla scheda di interfaccia di rete che fa riferimento all'appliance virtuale di rete come hop successivo. Verificare la connettività dalla VM di origine direttamente alla destinazione che ignora l'appliance virtuale di dispositivo. Se questo passaggio funziona, vedere la pagina relativa alla [risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)di appliance virtuale di dispositivo.

2. Eseguire una traccia di rete: 
   1. Avviare una traccia di rete nella macchina virtuale di destinazione. Per Windows, è possibile usare **netsh**. Per Linux, usare **TCPDump**.
   2. Eseguire **TcpPing** o **PsPing** dall'origine all'indirizzo IP di destinazione.

   * Di seguito è riportato un esempio di comando **TcpPing** :`tcping64.exe -t <destination VM address> 3389`

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
   > * SQL always-on (USA lo SKU ILB Basic)
   > * Ambienti del servizio app (con SKU ILB Basic)
   > * Gestione API (USA lo SKU ILB Basic)
   > * Azure Active Directory Domain Service (aggiunge) (USA lo SKU ILB Basic)

Per ulteriori informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale.

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>Tipo di connessione 2: Lo stato del peering Mostra "Disconnected"

È necessario eliminare i peering da reti virtuali e ricrearli.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>Scenario 5: Si è riscontrato un problema di connettività tra una rete virtuale hub-spoke e una risorsa locale

Si usa un'appliance virtuale di rete di terze parti o un gateway VPN?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Tipo di connessione 1: La rete usa un'appliance virtuale di rete di terze parti o un gateway VPN

Per risolvere i problemi di connettività che interessano un'appliance virtuale di rete o un gateway VPN di terze parti, vedere gli articoli seguenti:

* [Risoluzione dei problemi di appliance appliance di dispositivo](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Concatenamento di servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>Tipo di connessione 2: La rete non è un gateway VPN o di terze parti

Entrambe le reti virtuali Hub e spoke hanno un gateway VPN?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>Entrambe le reti virtuali Hub e spoke hanno un gateway VPN

L'uso di un gateway remoto non è supportato.

A causa di una limitazione del peering VNet, **usare il gateway remoto** non è supportato nel VNet spoke se il VNet spoke dispone già di un gateway VPN.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>Entrambe le reti virtuali Hub e spoke non dispongono di un gateway VPN

Per le connessioni da sito a sito o ExpressRoute, verificare le cause principali dei problemi di connettività alla rete virtuale remota da locale.

* Verificare che la casella di controllo **Consenti traffico trasmesso** sia selezionata nella rete virtuale con un gateway.
* Verificare che la casella di controllo **Usa gateway remoto** sia selezionata nella rete virtuale che non dispone di un gateway.
* Chiedere all'amministratore di rete di controllare i dispositivi locali per verificare che tutti dispongano dello spazio di indirizzi della rete virtuale remota.

Per le connessioni da punto a sito:

* Verificare che la casella di controllo **Consenti traffico trasmesso** sia selezionata nella rete virtuale con un gateway.
* Verificare che la casella di controllo **Usa gateway remoto** sia selezionata nella rete virtuale che non dispone di un gateway.
* Scaricare e installare di nuovo il pacchetto client da punto a sito. Le route di rete virtuale di cui è stato appena creato il peering non aggiungono automaticamente le route ai client da punto a sito.

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Scenario 6: Si è riscontrato un problema di connettività di rete hub-spoke tra reti virtuali spoke nella stessa area

È necessario disporre di un appliance virtuale di rete in una rete Hub, configurare UdR in spoke che hanno un appliance virtuale di rete impostato come hop successivo e abilitare **Consenti traffico con inoltri** nella rete virtuale dell'hub.

Per altre informazioni, vedere [concatenamento dei servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)e discutere di questi requisiti con il [Fornitore](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) dell'appliance virtuale di rete di propria scelta.

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Scenario 7: Si è riscontrato un problema di connettività di rete hub-spoke tra reti virtuali spoke in aree diverse

Il transito sul peering VNet globale è ora supportato. La connettività non funzionerà sul peering VNet globale per le risorse seguenti:

* VM dietro lo SKU Basic ILB
* Cache Redis (USA lo SKU ILB Basic)
* Gateway applicazione (USA lo SKU ILB Basic)
* Set di scalabilità (USA lo SKU ILB Basic)
* Cluster Service Fabric (USA lo SKU ILB Basic)
* SQL always-on (USA lo SKU ILB Basic)
* Ambienti del servizio app (con SKU ILB Basic)
* Gestione API (USA lo SKU ILB Basic)
* Azure Active Directory Domain Service (aggiunge) (USA lo SKU ILB Basic)

Per ulteriori informazioni, vedere i [requisiti e i vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale e le [diverse topologie VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Scenario 8: Si è riscontrato un problema di connettività di rete hub-spoke tra un'app Web e la rete virtuale spoke

Per risolvere il problema, attenersi alla seguente procedura:

1. Accedere al portale di Azure. Passare all'app Web, selezionare **rete**e quindi selezionare **integrazione VNet**.
2. Controllare se è possibile visualizzare la rete virtuale remota. Immettere manualmente lo spazio di indirizzi della rete virtuale remota (**sincronizzazione rete** e **Aggiungi route**).

Per altre informazioni, vedere i seguenti articoli:

* [Integrare un'app in una rete virtuale di Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Informazioni sul routing VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>Scenario 9: Viene visualizzato un errore durante la configurazione del peering di rete virtuale

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Errore 1: Il tenant `<TENANT ID>` corrente non è autorizzato ad accedere alla sottoscrizione collegata

Per risolvere questo problema, seguire la procedura illustrata in [creare un peering-interfaccia](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)della riga di comando di Azure.

### <a name="error-2-not-connected"></a>Errore 2: Connessione non attivata

È necessario eliminare i peering da entrambi reti virtuali e ricrearli.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>Errore 3: Non è stato possibile eseguire il peering di una rete virtuale databricks

Per risolvere questo problema, configurare il peering di rete virtuale dal pannello **Azure Databricks** , quindi specificare la rete virtuale di destinazione usando l' **ID risorsa**. Per ulteriori informazioni, vedere la pagina relativa [alla rete virtuale peer a databricks in una rete virtuale remota](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di connettività tra macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)