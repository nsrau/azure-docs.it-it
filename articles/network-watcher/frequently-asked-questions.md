---
title: Domande frequenti (FAQ) su Azure Network Watcher Documenti Microsoft
description: Questo articolo risponde alle domande frequenti sul servizio Watcher rete di Azure.This article answers frequently asked questions about the Azure Network Watcher service.
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
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471857"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Domande frequenti su Azure Network Watcher
Il servizio Watcher rete di Azure offre una suite di strumenti per monitorare, diagnosticare, visualizzare le metriche e abilitare o disabilitare i log per le risorse in una rete virtuale di Azure.The [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) service provides a suite of tools for monitor, diagnose, view metrics, and enable or disable logs for resources in an Azure virtual network. Questo articolo risponde alle domande più comuni sul servizio.

## <a name="general"></a>Generale

### <a name="what-is-network-watcher"></a>Che cos'è Network Watcher?
Network Watcher è progettato per monitorare e ripristinare l'integrità della rete dei componenti IaaS (Infrastructure-as-a-Service), che include Macchine virtuali, Reti virtuali, Gateway applicazione, servizi di bilanciamento del carico e altre risorse in una rete virtuale di Azure.Network Watcher is designed to monitor and repair the network health of IaaS (Infrastructure-as-a-Service) components, which includes Virtual Machines, Virtual Networks, Application Gateways, Load balancers, and other resources in an Azure virtual network. Non è una soluzione per monitorare l'infrastruttura PaaS (Platform-as-a-Service) o ottenere analisi web/mobile.

### <a name="what-tools-does-network-watcher-provide"></a>Quali strumenti fornisce Network Watcher?
Network Watcher fornisce tre set principali di funzionalità
* Monitoraggio
  * [La visualizzazione Topologia](https://docs.microsoft.com/azure/network-watcher/view-network-topology) mostra le risorse nella rete virtuale e le relazioni tra di esse.
  * [Connection Monitor](https://docs.microsoft.com/azure/network-watcher/connection-monitor) consente di monitorare la connettività e la latenza tra una macchina virtuale e un'altra risorsa di rete.
  * [Il monitoraggio delle prestazioni](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) di rete consente di monitorare la connettività e le latenze nelle architetture di rete ibride, nei circuiti Expressroute e negli endpoint di servizio/applicazione.  
* Diagnostica
  * [Ip Flow Verify](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) consente di rilevare i problemi di filtraggio del traffico a livello di macchina virtuale.
  * [L'hop successivo](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) consente di verificare le route di traffico e di rilevare i problemi di routing.
  * [Risoluzione](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) problemi di connessione consente un controllo di connettività e latenza una tantando tra una macchina virtuale e un'altra risorsa di rete.
  * [L'acquisizione di pacchetti](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) consente di acquisire tutto il traffico in una macchina virtuale nella rete virtuale.
  * [Risoluzione dei problemi VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) esegue più controlli di diagnostica nei gateway VPN e nelle connessioni per risolvere i problemi di debug.
* Registrazione
  * [Registri](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) di flusso dei gruppi di sicurezza di rete consente di registrare tutto il traffico nei gruppi di sicurezza di [rete](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) elabora i dati del log di flusso del gruppo di sicurezza di rete, consentendo di visualizzare, eseguire query, analizzare e comprendere il traffico di rete.


Per informazioni più dettagliate, vedere la [pagina di panoramica](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)di Network Watcher .


### <a name="how-does-network-watcher-pricing-work"></a>Come funziona noto il prezzo di Network Watcher?
Visita la [pagina Prezzi](https://azure.microsoft.com/pricing/details/network-watcher/) per i componenti di Network Watcher e i relativi prezzi.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>In quali aree è supportato/disponibile Network Watcher?
È possibile visualizzare la disponibilità regionale più recente nella [pagina Disponibilità del servizio di AzureYou](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) can view the latest regional availability on the Azure Service availability page

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Quali autorizzazioni sono necessarie per utilizzare Network Watcher?
Vedere l'elenco delle [autorizzazioni RBAC necessarie per utilizzare Network Watcher](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). Per la distribuzione delle risorse, sono necessarie le autorizzazioni di collaboratore per NetworkWatcherRG (vedere di seguito).

### <a name="how-do-i-enable-network-watcher"></a>Come si abilita Network Watcher?
Il servizio Network Watcher viene [abilitato automaticamente](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) per ogni sottoscrizione.

### <a name="what-is-the-network-watcher-deployment-model"></a>Che cos'è il modello di distribuzione di Network Watcher?
La risorsa padre di Network Watcher viene distribuita con un'istanza univoca in ogni area. Formato di denominazione: NetworkWatcher_RegionName. Esempio: NetworkWatcher_centralus è la risorsa Network Watcher per l'area "Stati Uniti centrali".

### <a name="what-is-the-networkwatcherrg"></a>Che cos'è NetworkWatcherRG?
Le risorse di Network Watcher si trovano nel gruppo di risorse **nascosto NetworkWatcherRG** che viene creato automaticamente. Ad esempio, la risorsa Registri di flusso del gruppo di sicurezza di rete è una risorsa figlio di Network Watcher ed è abilitata in NetworkWatcherRG.For example, the NSG Flow Logs resource is a child resource of Network Watcher and is enabled in the NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Perché è necessario installare l'estensione Network Watcher? 
L'estensione Network Watcher è necessaria per qualsiasi funzionalità che deve generare o intercettare il traffico da una macchina virtuale. 

### <a name="which-features-require-the-network-watcher-extension"></a>Quali funzionalità richiedono l'estensione Network Watcher?
Le funzionalità Acquisizione pacchetti, Risoluzione dei problemi di connessione e Monitoraggio connessione richiedono la presenza dell'estensione Network Watcher.

### <a name="what-are-resource-limits-on-network-watcher"></a>Quali sono i limiti di risorse in Network Watcher?
Vedere la pagina [Limiti del servizio](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) per tutti i limiti.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Perché è consentita una sola istanza di Network Watcher per ogni area? 
Network Watcher deve solo essere abilitato una volta per una sottoscrizione per il funzionamento delle funzionalità, non è un limite di servizio.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Come posso gestire la risorsa Network Watcher? 
The Network Watcher resource represents the backend service for Network Watcher and is fully managed by Azure. I clienti non hanno bisogno di gestirlo. Operazioni come lo spostamento non sono supportate nella risorsa. Tuttavia, [la risorsa può essere eliminata](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal). 

## <a name="nsg-flow-logs"></a>Registri di flusso del gruppo di sicurezza di rete

### <a name="what-does-nsg-flow-logs-do"></a>Che cosa fa NSG Flow Logs do?
Le risorse di rete di Azure possono essere combinate e gestite tramite i gruppi di sicurezza di [rete.](https://docs.microsoft.com/azure/virtual-network/security-overview) I registri di flusso del gruppo di sicurezza di rete consentono di registrare informazioni sul flusso di 5 tuple su tutto il traffico attraverso i gruppi di sicurezza di rete. I log di flusso non elaborati vengono scritti in un account di Archiviazione di Azure da cui possono essere ulteriormente elaborati, analizzati, sottoposti a query o esportati in base alle esigenze.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Come si usano i registri di flusso del gruppo di sicurezza di rete con un account di archiviazione protetto da un firewall?

Per usare un account di archiviazione dietro un firewall, è necessario fornire un'eccezione affinché i servizi Microsoft attendibili per accedere all'account di archiviazione:To use a Storage account behind a firewall, you have to provide an exception for Trusted Microsoft Services to access your storage account:

* Passare all'account di archiviazione digitando il nome dell'account di archiviazione nella ricerca globale nel portale o nella [pagina Account di archiviazione](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* Nella sezione **IMPOSTAZIONI** selezionare **Firewall e reti virtuali**
* In "Consenti accesso da", selezionare **Reti selezionate**. Quindi, in **Eccezioni**, selezionare la casella accanto a **"Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione"** 
* Se l'opzione è già selezionata, non occorre modificare niente.  
* Individuare il gruppo di sicurezza di rete di destinazione nella [pagina di panoramica dei registri di flusso](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) del gruppo di sicurezza di rete e abilitare i registri di flusso del gruppo di sicurezza di rete con l'account di archiviazione precedente selezionato.

È possibile controllare i log di archiviazione dopo alcuni minuti. Dovrebbe essere visualizzato un TimeStamp aggiornato o un nuovo file JSON creato.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Come si usano i registri di flusso del gruppo di sicurezza di rete con un account di archiviazione dietro un endpoint del servizio?

I log di flusso del gruppo di sicurezza di rete sono compatibili con gli endpoint del servizio senza richiedere alcuna configurazione aggiuntiva. Vedere [l'esercitazione sull'abilitazione degli endpoint](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) di servizio nella rete virtuale.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual è la differenza tra i log di flusso versioni 1 & 2?
Flow Logs versione 2 introduce il concetto di *Flow State* & archivia le informazioni sui byte e sui pacchetti trasmessi. [Per saperne](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)di più .

## <a name="next-steps"></a>Passaggi successivi
 - Vai alla nostra pagina di panoramica della [documentazione](https://docs.microsoft.com/azure/network-watcher/) per alcune esercitazioni per iniziare a usare Network Watcher.
