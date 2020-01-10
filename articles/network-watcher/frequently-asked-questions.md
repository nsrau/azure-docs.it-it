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
ms.openlocfilehash: 0eea6700b8b248a87666071ee02572d356110cd0
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830174"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Domande frequenti su Azure Network Watcher
Il servizio [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) offre una suite di strumenti per monitorare, diagnosticare, visualizzare le metriche e abilitare o disabilitare i log per le risorse in una rete virtuale di Azure. Questo articolo risponde a domande comuni sul servizio.

## <a name="general"></a>Informazioni di carattere generale

### <a name="what-is-network-watcher"></a>Che cos'è Network Watcher?
Network Watcher è progettato per monitorare e ripristinare lo stato di integrità della rete dei componenti IaaS (Infrastructure-as-a-Service), che include macchine virtuali, reti virtuali, gateway applicazione, servizi di bilanciamento del carico e altre risorse in una rete virtuale di Azure. Non si tratta di una soluzione per il monitoraggio dell'infrastruttura PaaS (piattaforma distribuita come servizio) o di analisi Web/per dispositivi mobili.

### <a name="what-tools-does-network-watcher-provide"></a>Quali strumenti Network Watcher fornire?
Network Watcher offre tre principali set di funzionalità
* Monitorare
  * La [visualizzazione topologia](https://docs.microsoft.com/azure/network-watcher/view-network-topology) Mostra le risorse nella rete virtuale e le relazioni tra di esse.
  * Il [monitoraggio della connessione](https://docs.microsoft.com/azure/network-watcher/connection-monitor) consente di monitorare la connettività e la latenza tra una macchina virtuale e un'altra risorsa di rete.
  * [Monitoraggio prestazioni rete](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) consente di monitorare la connettività e le latenze tra architetture di rete ibride, circuiti Expressroute e endpoint di servizio/applicazione.  
* Diagnostica
  * La [Verifica del flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) consente di rilevare i problemi di filtro del traffico a livello di macchina virtuale.
  * L' [hop successivo](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) consente di verificare le route del traffico e di rilevare i problemi di routing.
  * La [risoluzione dei problemi di connessione](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) consente una connettività unica e il controllo della latenza tra una macchina virtuale e un'altra risorsa di rete.
  * L' [acquisizione di pacchetti](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) consente di acquisire tutto il traffico in una macchina virtuale nella rete virtuale.
  * La [risoluzione dei problemi relativi alla VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) esegue più controlli di diagnostica sui gateway VPN e sulle connessioni per facilitare il debug dei problemi.
* Registrazione
  * [NSG Flow log](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) consente di registrare tutto il traffico nei [gruppi di sicurezza di rete (gruppi)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) elabora i dati del log di flusso NSG che consentono di visualizzare, eseguire query, analizzare e comprendere il traffico di rete.


Per informazioni più dettagliate, vedere la [pagina di panoramica Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Come funzionano i prezzi Network Watcher?
Visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/network-watcher/) per Network Watcher componenti e i relativi prezzi.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Quali aree sono Network Watcher supportate/disponibili in?
È possibile visualizzare l'ultima disponibilità a livello di area nella [pagina disponibilità dei servizi di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="what-are-resource-limits-on-network-watcher"></a>Che cosa sono i limiti delle risorse per Network Watcher?
Per tutti i limiti, vedere la pagina relativa ai [limiti del servizio](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) .  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Perché è consentita una sola istanza di Network Watcher per area?
Network Watcher deve essere abilitata una sola volta per una sottoscrizione per il funzionamento delle funzionalità, non si tratta di un limite di servizio.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Perché è necessario installare l'estensione Network Watcher? 
L'estensione Network Watcher è obbligatoria per qualsiasi funzionalità che deve generare o intercettare il traffico da una macchina virtuale. 

### <a name="which-features-require-the-network-watcher-extension"></a>Quali funzionalità richiedono l'estensione Network Watcher?
Solo l'acquisizione di pacchetti, la risoluzione dei problemi di connessione e il monitoraggio della connessione richiedono che sia presente l'estensione Network Watcher.

## <a name="nsg-flow-logs"></a>Log di flusso NSG

### <a name="what-does-nsg-flow-logs-do"></a>Quali sono i registri dei flussi di NSG?
Le risorse di rete di Azure possono essere combinate e gestite tramite [gruppi di sicurezza di rete (gruppi)](https://docs.microsoft.com/azure/virtual-network/security-overview). I log di flusso NSG consentono di registrare le informazioni sul flusso di 5 tuple relative a tutto il traffico attraverso gruppi. I log dei flussi non elaborati vengono scritti in un account di archiviazione di Azure da cui possono essere ulteriormente elaborati, analizzati, sottoposti a query o esportati in base alle esigenze.

### <a name="how-do-i-use-nsg-flow-logs-on-a-storage-account-with-a-firewall-or-through-a-service-endpoints"></a>Ricerca per categorie usare i log dei flussi di NSG in un account di archiviazione con un firewall o tramite endpoint di servizio?

Per usare un account di archiviazione con un firewall o tramite un endpoint del servizio, è necessario consentire ai servizi Microsoft attendibili di accedere all'account di archiviazione:

* Trovare il nome dell'account di archiviazione individuando il gruppo di sicurezza di rete nella [pagina di panoramica dei log dei flussi dei gruppi di sicurezza di rete](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)
* Passare all'account di archiviazione digitando il nome dell'account di archiviazione nella ricerca globale nel portale
* Nella sezione **IMPOSTAZIONI** selezionare **Firewall e reti virtuali**
* In "Consenti accesso da" selezionare **reti selezionate**. Quindi, in **eccezioni**, seleziona la casella accanto a **"Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione"** 
* Se l'opzione è già selezionata, non occorre modificare niente.  

È possibile controllare i log di archiviazione dopo alcuni minuti. Dovrebbe essere visualizzato un TimeStamp aggiornato o un nuovo file JSON creato.

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual è la differenza tra i log dei flussi versioni 1 & 2?
Log dei flussi versione 2 introduce il concetto di *stato del flusso* & archivia le informazioni su byte e pacchetti trasmessi. [Altre informazioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Fasi successive
 - Passare alla [pagina Panoramica della documentazione](https://docs.microsoft.com/azure/network-watcher/) per alcune esercitazioni per iniziare a usare Network Watcher.
