---
title: Domande frequenti sulle Network Watcher di Azure | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti sul servizio Network Watcher di Azure.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: 959062d493d9eb47204be2488f216b70804b3605
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965766"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Domande frequenti su Azure Network Watcher
Il servizio [Azure Network Watcher](./network-watcher-monitoring-overview.md) offre una suite di strumenti per monitorare, diagnosticare, visualizzare le metriche e abilitare o disabilitare i log per le risorse in una rete virtuale di Azure. Questo articolo risponde a domande comuni sul servizio.

## <a name="general"></a>Generale

### <a name="what-is-network-watcher"></a>Che cos'è Network Watcher?
Network Watcher è progettato per monitorare e ripristinare lo stato di integrità della rete dei componenti IaaS (Infrastructure-as-a-Service), che include macchine virtuali, reti virtuali, gateway applicazione, servizi di bilanciamento del carico e altre risorse in una rete virtuale di Azure. Non si tratta di una soluzione per il monitoraggio dell'infrastruttura PaaS (piattaforma distribuita come servizio) o di analisi Web/per dispositivi mobili.

### <a name="what-tools-does-network-watcher-provide"></a>Quali strumenti Network Watcher fornire?
Network Watcher offre tre principali set di funzionalità
* Monitoraggio
  * La [visualizzazione topologia](./view-network-topology.md) Mostra le risorse nella rete virtuale e le relazioni tra di esse.
  * Il [monitoraggio della connessione](./connection-monitor.md) consente di monitorare la connettività e la latenza tra una macchina virtuale e un'altra risorsa di rete.
  * [Monitoraggio prestazioni rete](../azure-monitor/insights/network-performance-monitor.md) consente di monitorare la connettività e le latenze tra architetture di rete ibride, circuiti Expressroute e endpoint di servizio/applicazione.  
* Diagnostica
  * La [Verifica del flusso IP](./network-watcher-ip-flow-verify-overview.md) consente di rilevare i problemi di filtro del traffico a livello di macchina virtuale.
  * L' [hop successivo](./network-watcher-next-hop-overview.md) consente di verificare le route del traffico e di rilevare i problemi di routing.
  * La [risoluzione dei problemi di connessione](./network-watcher-connectivity-portal.md) consente una connettività unica e il controllo della latenza tra una macchina virtuale e un'altra risorsa di rete.
  * L' [acquisizione di pacchetti](./network-watcher-packet-capture-overview.md) consente di acquisire tutto il traffico in una macchina virtuale nella rete virtuale.
  * La [risoluzione dei problemi relativi alla VPN](./network-watcher-troubleshoot-overview.md) esegue più controlli di diagnostica sui gateway VPN e sulle connessioni per facilitare il debug dei problemi.
* Registrazione
  * [NSG Flow log](./network-watcher-nsg-flow-logging-overview.md) consente di registrare tutto il traffico nei [gruppi di sicurezza di rete (gruppi)](../virtual-network/network-security-groups-overview.md)
  * [Analisi del traffico](./traffic-analytics.md) elabora i dati del log di flusso NSG che consentono di visualizzare, eseguire query, analizzare e comprendere il traffico di rete.


Per informazioni più dettagliate, vedere la [pagina di panoramica Network Watcher](./network-watcher-monitoring-overview.md).


### <a name="how-does-network-watcher-pricing-work"></a>Come funzionano i prezzi Network Watcher?
Visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/network-watcher/) per Network Watcher componenti e i relativi prezzi.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Quali aree sono Network Watcher supportate/disponibili in?
È possibile visualizzare l'ultima disponibilità a livello di area nella [pagina disponibilità dei servizi di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Quali autorizzazioni sono necessarie per usare Network Watcher?
Vedere l'elenco delle [autorizzazioni RBAC di Azure necessarie per usare Network Watcher](./required-rbac-permissions.md). Per la distribuzione delle risorse, sono necessarie le autorizzazioni di collaboratore per NetworkWatcherRG (vedere di seguito).

### <a name="how-do-i-enable-network-watcher"></a>Come si abilita Network Watcher?
Il servizio Network Watcher viene [abilitato automaticamente](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) per ogni sottoscrizione.

### <a name="what-is-the-network-watcher-deployment-model"></a>Che cos'è il modello di distribuzione Network Watcher?
La risorsa padre Network Watcher viene distribuita con un'istanza univoca in ogni area. Formato di denominazione: NetworkWatcher_RegionName. Esempio: NetworkWatcher_centralus è la risorsa Network Watcher per l'area "Stati Uniti centrali".

### <a name="what-is-the-networkwatcherrg"></a>Che cos'è NetworkWatcherRG?
Network Watcher risorse si trovano nel gruppo di risorse **NetworkWatcherRG** nascoste creato automaticamente. Ad esempio, la risorsa log dei flussi NSG è una risorsa figlio di Network Watcher ed è abilitata in NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Perché è necessario installare l'estensione Network Watcher? 
L'estensione Network Watcher è obbligatoria per qualsiasi funzionalità che deve generare o intercettare il traffico da una macchina virtuale. 

### <a name="which-features-require-the-network-watcher-extension"></a>Quali funzionalità richiedono l'estensione Network Watcher?
L'acquisizione di pacchetti, la risoluzione dei problemi di connessione e le funzionalità di monitoraggio della connessione richiedono che sia presente l'estensione Network Watcher.

### <a name="what-are-resource-limits-on-network-watcher"></a>Che cosa sono i limiti delle risorse per Network Watcher?
Per tutti i limiti, vedere la pagina relativa ai [limiti del servizio](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits) .  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Perché è consentita una sola istanza di Network Watcher per area? 
Network Watcher deve essere abilitata una sola volta per una sottoscrizione per il funzionamento delle funzionalità, non si tratta di un limite di servizio.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Come è possibile gestire la risorsa Network Watcher? 
La risorsa Network Watcher rappresenta il servizio back-end per Network Watcher ed è completamente gestita da Azure. I clienti non devono gestirlo. Operazioni come lo spostamento non sono supportate nella risorsa. Tuttavia, [la risorsa può essere eliminata](./network-watcher-create.md#delete-a-network-watcher-in-the-portal). 

## <a name="service-availability-and-redundancy"></a>Disponibilità e ridondanza del servizio 

### <a name="is-the-network-watcher-service-zone-resilient"></a>La resilienza della zona di servizio Network Watcher? 
Sì. Per impostazione predefinita, il servizio Network Watcher è resiliente per la zona. 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>Ricerca per categorie configurare il servizio Network Watcher per la resilienza della zona? 
Non è necessaria alcuna configurazione del cliente per abilitare la resilienza della zona. La resilienza della zona per Network Watcher risorse è disponibile per impostazione predefinita e viene gestita dal servizio stesso. 

## <a name="nsg-flow-logs"></a>Log di flusso NSG

### <a name="what-does-nsg-flow-logs-do"></a>Quali sono i registri dei flussi di NSG?
Le risorse di rete di Azure possono essere combinate e gestite tramite [gruppi di sicurezza di rete (gruppi)](../virtual-network/network-security-groups-overview.md). I log di flusso NSG consentono di registrare le informazioni sul flusso di 5 tuple relative a tutto il traffico attraverso gruppi. I log dei flussi non elaborati vengono scritti in un account di archiviazione di Azure da cui possono essere ulteriormente elaborati, analizzati, sottoposti a query o esportati in base alle esigenze.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Ricerca per categorie usare i log di flusso NSG con un account di archiviazione dietro un firewall?

Per usare un account di archiviazione dietro un firewall, è necessario fornire un'eccezione affinché i servizi Microsoft attendibili accedano all'account di archiviazione:

* Passare all'account di archiviazione digitando il nome dell'account di archiviazione nella ricerca globale nel portale o dalla [pagina account di archiviazione](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* Nella sezione **IMPOSTAZIONI** selezionare **Firewall e reti virtuali**
* In "Consenti accesso da" selezionare **reti selezionate**. Quindi, in **eccezioni**, seleziona la casella accanto a **"Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione"** 
* Se l'opzione è già selezionata, non occorre modificare niente.  
* Individuare il NSG di destinazione nella [pagina Panoramica dei log di flusso di NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e abilitare i log di flusso di NSG con l'account di archiviazione selezionato.

È possibile controllare i log di archiviazione dopo alcuni minuti. Dovrebbe essere visualizzato un TimeStamp aggiornato o un nuovo file JSON creato.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Ricerca per categorie usare i log di flusso NSG con un account di archiviazione dietro a un endpoint di servizio?

I log di flusso NSG sono compatibili con gli endpoint di servizio senza richiedere alcuna configurazione aggiuntiva. Vedere l' [esercitazione sull'abilitazione degli endpoint di servizio](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) nella rete virtuale.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual è la differenza tra i log dei flussi versioni 1 & 2?
Log dei flussi versione 2 introduce il concetto di *stato del flusso* & archivia le informazioni su byte e pacchetti trasmessi. [Altre informazioni](./network-watcher-nsg-flow-logging-overview.md#log-format).

## <a name="next-steps"></a>Passaggi successivi
 - Passare alla [pagina Panoramica della documentazione](./index.yml) per alcune esercitazioni per iniziare a usare Network Watcher.