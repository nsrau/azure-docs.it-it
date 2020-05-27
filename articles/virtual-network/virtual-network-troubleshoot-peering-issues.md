---
title: Risolvere i problemi di peering di rete virtuale
description: Procedure per risolvere la maggior parte dei problemi del peering di reti virtuali.
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
ms.openlocfilehash: 9685c1739a00788a974c200ddabb8cc975696b62
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587732"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Risolvere i problemi di peering di rete virtuale

Questa guida alla risoluzione dei problemi illustra la procedura per risolvere la maggior parte dei problemi di [peering di reti virtuali](virtual-network-peering-overview.md).

![Diagramma del peering di reti virtuali](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configurare il peering tra due reti virtuali

Le reti virtuali si trovano nella stessa sottoscrizione o in sottoscrizioni diverse?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Le reti virtuali si trovano nella stessa sottoscrizione

Per configurare il peering tra reti virtuali che si trovano nella stessa sottoscrizione, usare i metodi descritti negli articoli seguenti:

* Se le reti virtuali si trovano nella *stessa area*, vedere [Creare un peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Se le reti virtuali si trovano in *aree diverse*, vedere [Peering di reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> La connettività non funziona sul peering globale di reti virtuali per le risorse seguenti: 
>
> * Macchine virtuali (VM) dietro SKU Basic del servizio di bilanciamento del carico interno (ILB, internal load balancer)
> * Cache Redis (usa lo SKU Basic di ILB)
> * Gateway applicazione (usa lo SKU Basic di ILB)
> * Set di scalabilità di macchine virtuali (usa lo SKU Basic di ILB)
> * Cluster di Azure Service Fabric (usa lo SKU Basic di ILB)
> * SQL Server Always On (usa lo SKU Basic di ILB)
> * Ambiente del servizio app di Azure per PowerApps (usa lo SKU Basic di ILB)
> * Gestione API di Azure (usa lo SKU Basic di ILB)
> * Azure Active Directory Domain Service (Azure AD DS) (usa lo SKU Basic di ILB)

Per altre informazioni, vedere [Requisiti e vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Le reti virtuali si trovano in sottoscrizioni o tenant di Active Directory diversi

Per configurare il peering di reti virtuali che si trovano in sottoscrizioni o tenant di Active Directory diversi, vedere [Creare il peering in sottoscrizioni diverse per l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Per configurare il peering di reti, è necessario avere le autorizzazioni di **Collaboratore Rete** in entrambe le sottoscrizioni. Per altre informazioni, vedere [Autorizzazioni per il peering](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configurare il peering di reti virtuali con topologia hub-spoke in base a risorse locali

![Diagramma del peering di reti virtuali con spoke locale](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Per una connessione da sito a sito o ExpressRoute

Seguire la procedura descritta in [Configurare il transito nel gateway VPN per il peering di reti virtuali](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>Per le connessioni da punto a sito

1. Seguire la procedura descritta in [Configurare il transito nel gateway VPN per il peering di reti virtuali](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Dopo aver stabilito o cambiato il peering di reti virtuali, scaricare e reinstallare il pacchetto da punto a sito in modo che i client da punto a sito ottengano le route aggiornate alla rete virtuale spoke.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configurare il peering di reti virtuali con topologia hub-spoke

![Diagramma del peering di reti virtuali con uno spoke di rete virtuale](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Le reti virtuali si trovano nella stessa area


1. Nella rete virtuale hub configurare un'appliance virtuale di rete.
1. Nelle reti virtuali spoke specificare route definite dall'utente con il tipo di hop successivo "appliance virtuale di rete" applicato.

Per altre informazioni, vedere [Concatenamento di servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Se è necessaria assistenza per configurare un'appliance virtuale di rete, [contattare il fornitore dell'appliance](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Per informazioni sulla risoluzione dei problemi relativi alla configurazione e al routing di appliance virtuali di rete, vedere [Problemi delle appliance virtuali di rete in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>Le reti virtuali si trovano in aree diverse

Il transito su peering globale di reti virtuali è ora supportato. La connettività non funziona sul peering globale di reti virtuali per le risorse seguenti:

* Macchina virtuale dietro lo SKU Basic ILB
* Cache Redis (usa lo SKU Basic di ILB)
* Gateway applicazione (usa lo SKU Basic di ILB)
* Set di scalabilità (usa lo SKU Basic di ILB)
* Cluster Service Fabric (usa lo SKU Basic ILB)
* SQL Server Always On (usa lo SKU Basic di ILB)
* Ambienti del servizio app (usa lo SKU Basic di ILB)
* Gestione API (usa lo SKU Basic ILB)
* Azure AD DS (usa lo SKU Basic di ILB)

Per altre informazioni su requisiti e vincoli del peering globale, vedere [Peering di reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Risolvere un problema di connettività tra due reti virtuali con peering

Accedere al [portale di Azure](https://portal.azure.com/) con un account che abbia [ruoli e autorizzazioni](virtual-network-manage-peering.md#permissions) necessari. Selezionare la rete virtuale, selezionare **Peering**, quindi controllare il campo **Stato**. Qual è lo stato?

### <a name="the-peering-status-is-connected"></a>Lo stato del peering è "Connesso".

Per risolvere il problema:

1. Controllare i flussi del traffico di rete:

   Usare le opzioni [Risoluzione dei problemi di connessione](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) e [Verifica flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) dalla VM di origine a quella di destinazione per determinare se è presente un gruppo di sicurezza di rete o una route definita dall'utente che genera interferenza nel flusso del traffico.

   Se si usa un firewall o un appliance virtuale di rete: 
   1. Prendere nota dei parametri della route definita dall'utente in modo da poterli ripristinare al termine di questo parametro.
   2. Rimuovere la route definita dall'utente dalla subnet della VM di origine o dalla scheda di interfaccia di rete che punta all'appliance virtuale di rete come hop successivo. Verificare la connettività dalla VM di origine direttamente alla destinazione che ignora l'appliance virtuale di rete. Se questo passaggio non funziona, vedere [Strumento di risoluzione dei problemi dell'appliance virtuale di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Eseguire una traccia di rete: 
   1. Avviare una traccia di rete nella VM di destinazione. Per Windows, è possibile usare **Netsh**. Per Linux, usare **TCPDump**.
   2. Eseguire **TcpPing** o **PsPing** dall'IP di origine a quello di destinazione.

      Ecco un esempio di comando **TcpPing**: `tcping64.exe -t <destination VM address> 3389`

   3. Una volta completato il comando **TcpPing**, arrestare la traccia di rete nella destinazione.
   4. Se i pacchetti arrivano dall'origine, non è presente alcun problema di rete. Esaminare il firewall della VM e l'applicazione in ascolto su tale porta per individuare il problema di configurazione.

   > [!Note]
   > Non è possibile connettersi ai tipi di risorse seguenti tramite peering globale di reti virtuali (reti virtuali in aree diverse):
   >
   > * Macchina virtuale dietro lo SKU Basic ILB
   > * Cache Redis (usa lo SKU Basic di ILB)
   > * Gateway applicazione (usa lo SKU Basic di ILB)
   > * Set di scalabilità (usa lo SKU Basic di ILB)
   > * Cluster Service Fabric (usa lo SKU Basic ILB)
   > * SQL Server Always On (usa lo SKU Basic di ILB)
   > * Ambienti del servizio app (usa lo SKU Basic di ILB)
   > * Gestione API (usa lo SKU Basic ILB)
   > * Azure AD DS (usa lo SKU Basic di ILB)

Per altre informazioni, vedere [Requisiti e vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale.

### <a name="the-peering-status-is-disconnected"></a>Lo stato del peering è "Disconnesso"

Per risolvere questo problema, eliminare il peering da entrambe le reti virtuali e quindi ricrearlo.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Risolvere un problema di connettività tra una rete virtuale hub-spoke e una risorsa locale

La rete usa un'appliance virtuale di rete o un gateway VPN di terze parti?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>La rete usa un'appliance virtuale di rete o un gateway VPN di terze parti

Per risolvere i problemi di connettività che interessano appliance virtuali di rete o gateway VPN di terze parti, vedere gli articoli seguenti:

* [Strumento di risoluzione dei problemi delle appliance virtuali di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Concatenamento dei servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>La rete non usa un'appliance virtuale di rete o un gateway VPN di terze parti

La rete virtuale hub e la rete virtuale spoke hanno un gateway VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Sia la rete virtuale hub che la rete virtuale spoke hanno un gateway VPN

L'uso di un gateway remoto non è supportato.

Se la rete virtuale spoke ha già un gateway VPN, l'opzione **Usa gateway remoti** non è supportata in tale rete. Il motivo è dovuto a una limitazione del peering di reti virtuali.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>La rete virtuale hub e la rete virtuale spoke non hanno un gateway VPN

Per le connessioni da sito a sito o Azure ExpressRoute, verificare le seguenti cause principali di problemi di connettività alla rete virtuale remota da locale:

* Nella rete virtuale con un gateway, verificare che la casella di controllo **Consenti traffico inoltrato** sia selezionata.
* Nella rete virtuale senza un gateway, verificare che la casella di controllo **Usa gateway remoti** sia selezionata.
* Chiedere all'amministratore di rete di controllare i dispositivi locali per verificare che lo spazio di indirizzi della rete virtuale remota sia stato aggiunto a tutti.

Per le connessioni da punto a sito:

* Nella rete virtuale con un gateway, verificare che la casella di controllo **Consenti traffico inoltrato** sia selezionata.
* Nella rete virtuale senza un gateway, verificare che la casella di controllo **Usa gateway remoti** sia selezionata.
* Scaricare e reinstallare il pacchetto client da punto a sito. Le route di reti virtuali di cui è stato appena creato il peering non aggiungono automaticamente route ai client da punto a sito.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Risolvere un problema di connettività di rete hub-spoke tra reti virtuali spoke nella stessa area

Una rete hub deve includere un'appliance virtuale di rete. Configurare le route definite dall'utente negli spoke per cui è impostata un'appliance virtuale di rete come hop successivo e abilitare l'opzione **Consenti traffico inoltrato**  nella rete virtuale hub.

Per altre informazioni, vedere [Concatenamento dei servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining) e rivolgersi al [fornitore dell'appliance virtuale di rete](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) per discutere dei requisiti.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Risolvere un problema di connettività di rete hub-spoke tra reti virtuali spoke in aree diverse

Il transito su peering globale di reti virtuali è ora supportato. La connettività non funziona sul peering globale di reti virtuali per le risorse seguenti:

* Macchina virtuale dietro lo SKU Basic ILB
* Cache Redis (usa lo SKU Basic di ILB)
* Gateway applicazione (usa lo SKU Basic di ILB)
* Set di scalabilità (usa lo SKU Basic di ILB)
* Cluster Service Fabric (usa lo SKU Basic ILB)
* SQL Server Always On (usa lo SKU Basic di ILB)
* Ambienti del servizio app (usa lo SKU Basic di ILB)
* Gestione API (usa lo SKU Basic ILB)
* Azure AD DS (usa lo SKU Basic di ILB)

Per altre informazioni, vedere [Requisiti e vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) del peering globale e [Topologie diverse di VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Risolvere un problema di connettività di rete hub-spoke tra un'app Web e la rete virtuale spoke

Per risolvere il problema:

1. Accedere al portale di Azure. 
1. Nell'app Web selezionare **Rete** e quindi **Integrazione rete virtuale**.
1. Verificare se è possibile vedere la rete virtuale remota. Immettere manualmente lo spazio di indirizzi della rete virtuale remota (**Sincronizza rete** e **Aggiungi route**).

Per altre informazioni, vedere gli articoli seguenti:

* [Integrare un'app in una rete virtuale di Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Informazioni sul routing VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Risolvere un messaggio di errore sulla configurazione del peering di reti virtuali 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Il tenant corrente `<TENANT ID>` non è autorizzato ad accedere alla sottoscrizione collegata

Per risolvere questo problema, vedere [creare un peering-](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)dell'interfaccia della riga di comando di Azure.

### <a name="not-connected"></a>Non connesso

Per risolvere questo problema, eliminare il peering da entrambe le reti virtuali e quindi ricrearlo.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Non è possibile eseguire il peering di una rete virtuale Databricks

Per risolvere questo problema, configurare il peering di reti virtuali in **Azure Databricks**, quindi specificare la rete virtuale di destinazione usando **ID risorsa**. Per altre informazioni, vedere [Peering di una rete virtuale Databricks con una rete virtuale remota](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>La rete virtuale remota non ha un gateway

Questo problema si verifica quando si esegue il peering di reti virtuali da tenant diversi e in un secondo momento si vuole configurare `Use Remote Gateways`. Una limitazione del portale di Azure è che non è in grado di verificare la presenza di un gateway di rete virtuale nella rete virtuale di un altro tenant.

È possibile risolvere questo problema in due modi:

 * Eliminare i peering e attivare l'opzione `Use Remote Gateways` quando si crea un nuovo peering.
 * Usare PowerShell o l'interfaccia della riga di comando, invece del portale di Azure, per abilitare `Use Remote Gateways`.

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di connettività tra macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
