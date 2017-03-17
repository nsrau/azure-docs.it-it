---
title: Introduzione ad Azure Network Watcher | Microsoft Docs
description: Questa pagina fornisce una panoramica del servizio Network Watcher per il monitoraggio e la visualizzazione delle risorse connesse alla rete in Azure
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 3e7595baa26ba9eebfcb8a2fd5c2744d9c0fbfcb
ms.openlocfilehash: 03dbde64aefc12145a589d8b741c67865162b9b5
ms.lasthandoff: 02/23/2017

---

# <a name="azure-network-monitoring-overview"></a>Panoramica del monitoraggio della rete in Azure

Per creare una rete end-to-end in Azure è possibile orchestrare e comporre varie risorse di rete individuali, quali rete virtuale, ExpressRoute, gateway applicazione, servizi di bilanciamento del carico e altro ancora. Il monitoraggio è disponibile in ognuna delle risorse di rete. Questo tipo di monitoraggio è detto a livello di risorsa.

La rete end-to-end può avere interazioni tra le risorse e configurazioni complesse. Network Watcher consente il monitoraggio a livello di scenario per questi scenari complessi.

Questo articolo illustra il monitoraggio a livello di risorsa e di scenario. Il monitoraggio della rete in Azure comprende due categorie generali:

* [**Network Watcher**](#network-watcher): tra le funzionalità del servizio Network Watcher è incluso il monitoraggio basato su scenari. Il servizio include l'acquisizione pacchetti, l'hop successivo, la verifica del flusso IP, la visualizzazione dei gruppi di sicurezza e i registri dei flussi dei gruppi di sicurezza di rete. A differenza del monitoraggio a livello di singole risorse di rete, il monitoraggio a livello di scenario consente una visualizzazione completa delle risorse di rete.
* [**Monitoraggio delle risorse**](#network-resource-level-monitoring): il monitoraggio a livello di risorsa include funzionalità di log di diagnostica, metriche, risoluzione dei problemi e integrità delle risorse. Tutte queste funzionalità vengono compilate a livello di risorsa di rete.

## <a name="network-watcher"></a>Network Watcher

Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure.

Di seguito sono elencate le funzionalità attualmente disponibili in Network Watcher.

* **[Topologia](network-watcher-topology-overview.md)**: offre una visualizzazione a livello di rete che mostra le varie interconnessioni e le associazioni tra le risorse di rete in un gruppo di risorse.
* **[Acquisizione pacchetti variabile](network-watcher-packet-capture-overview.md)**: acquisisce i dati dei pacchetti all'interno e all'esterno di una macchina virtuale. La versatilità è data dalle opzioni di filtro avanzato e dai controlli ottimizzati, come la possibilità di impostare l'ora e il limite di dimensioni. È possibile memorizzare i dati dei pacchetti in un archivio BLOB o nel disco locale in formato CAP.
* **[Verifica flusso IP](network-watcher-ip-flow-verify-overview.md)**: controlla se un pacchetto viene accettato o rifiutato in base ai parametri di pacchetto costituiti da informazioni a 5 tuple sul flusso, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo. Se il pacchetto viene rifiutato da un gruppo di sicurezza, vengono restituiti la regola e il gruppo che hanno rifiutato il pacchetto.
* **[Hop successivo](network-watcher-next-hop-overview.md)**: determina l'hop successivo per i pacchetti indirizzati nell'infrastruttura di rete di Azure, permettendo così di diagnosticare eventuali route definite dall'utente non configurate in modo corretto.
* **[Visualizzazione dei gruppi di sicurezza](network-watcher-security-group-view-overview.md)**: ottiene le regole di sicurezza valide e applicate in una macchina virtuale.
* **[Registrazione dei flussi dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md)**: la registrazione dei flussi dei gruppi di sicurezza di rete permette di acquisire i log relativi al traffico consentito o negato dalle regole di sicurezza nel gruppo. Il flusso è definito da informazioni a 5 tuple, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo.
* **[Risoluzione dei problemi di connessione e gateway di rete virtuale](network-watcher-troubleshoot-manage-rest.md)**: permette di risolvere i problemi relativi al gateway di rete virtuale e alle connessioni.
* **[Limite sottoscrizioni di rete](#network-subscription-limits)**: permette di visualizzare l'utilizzo delle risorse di rete rispetto ai limiti.
* **[Configurazione del log di diagnostica](#diagnostic-logs)**: permette di abilitare o disabilitare i log di diagnostica per le risorse di rete in un gruppo di risorse da un unico riquadro.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Controllo degli accessi in base al ruolo in Network Watcher

Network Watcher fa uso del [modello di controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md). Di seguito sono indicate le autorizzazioni necessarie per Network Watcher in anteprima. È importante assicurarsi che il ruolo usato per avviare le API di Network Watcher o per usare Network Watcher dal portale abbia l'accesso necessario.

|Risorsa| Autorizzazione|
|---|---|
|Microsoft.Storage/ |Lettura|
|Microsoft.Authorization/| Lettura|
|Microsoft.Resources/subscriptions/resourceGroups/| Lettura|
|Microsoft.Storage/storageAccounts/listServiceSas/ | Azione|
|Microsoft.Storage/storageAccounts/listAccountSas/ |Azione|
|Microsoft.Storage/storageAccounts/listKeys/ | Azione|
|Microsoft.Compute/virtualMachines/ |Lettura|
|Microsoft.Compute/virtualMachines/ |Scrittura|
|Microsoft.Compute/virtualMachineScaleSets/ |Lettura|
|Microsoft.Compute/virtualMachineScaleSets/ |Scrittura|
|Microsoft.Network/networkWatchers/packetCaptures/| Lettura|
|Microsoft.Network/networkWatchers/packetCaptures/| Scrittura|
|Microsoft.Network/networkWatchers/packetCaptures/| Elimina|
|Microsoft.Network/networkWatchers/ |Scrittura|
|Microsoft.Network/networkWatchers/| Lettura|
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/| *|

### <a name="network-subscription-limits"></a>Limite sottoscrizioni di rete

Limite sottoscrizioni di rete fornisce informazioni dettagliate sull'utilizzo delle singole risorse di rete di una sottoscrizione in un'area rispetto al numero massimo di risorse disponibili.

![Limite sottoscrizioni di rete][nsl]

## <a name="network-resource-level-monitoring"></a>Monitoraggio a livello di risorsa di rete

Per il monitoraggio a livello di risorsa sono disponibili le funzionalità seguenti:

### <a name="audit-log"></a>Log di controllo

Le operazioni eseguite come parte della configurazione delle reti vengono registrate. È possibile visualizzare i relativi log nel portale di Azure o recuperarli usando strumenti Microsoft, come Power BI, o strumenti di terze parti. I log di controllo sono disponibili tramite il portale, PowerShell, l'interfaccia della riga di comando e l'API REST. Per altre informazioni sui log di controllo, vedere l'articolo relativo alle [operazioni di controllo con Resource Manager](../resource-group-audit.md).

I log di controllo sono disponibili per le operazioni eseguite su tutte le risorse di rete.

### <a name="metrics"></a>Metrica

Le metriche sono costituite da contatori e misurazioni delle prestazioni raccolti in un determinato periodo di tempo. Attualmente le metriche sono disponibili per il gateway applicazione. Le metriche possono essere usate per attivare avvisi in base a una soglia. Per informazioni su come usare le metriche per creare avvisi, vedere l'articolo relativo alla [diagnostica del gateway applicazione](../application-gateway/application-gateway-diagnostics.md).

![Visualizzazione metriche][metrics]

### <a name="diagnostic-logs"></a>Log di diagnostica

Le risorse di rete creano eventi periodici e spontanei, che vengono registrati negli account di archiviazione e inviati a un hub eventi oppure a Log Analytics. Questi log contengono informazioni dettagliate sull'integrità delle singole risorse e possono essere visualizzati con strumenti quali Power BI e Log Analytics. Per informazioni su come visualizzare i log di diagnostica, vedere l'articolo relativo a [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

Sono disponibili log di diagnostica per il [servizio di bilanciamento del carico](../load-balancer/load-balancer-monitor-log.md), i [gruppi di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md), le route e il [gateway applicazione](../application-gateway/application-gateway-diagnostics.md).

Network Watcher offre una visualizzazione dei log di diagnostica contenente tutte le risorse di rete che supportano la registrazione diagnostica. Da questa visualizzazione è possibile abilitare e disabilitare le risorse di rete in modo facile e veloce.

![logs][logs]

### <a name="troubleshooting"></a>Risoluzione dei problemi

Il pannello di risoluzione dei problemi è un'esperienza del portale ora disponibile per le risorse di rete che permette di diagnosticare i problemi comuni associati a una singola risorsa. L'esperienza è disponibile per le risorse di rete seguenti: ExpressRoute, gateway VPN, gateway applicazione, log di sicurezza di rete, route, servizio DNS, servizio di bilanciamento del carico e Gestione traffico. Per altre informazioni sulla risoluzione dei problemi a livello di risorsa, vedere [Diagnose and resolve issues with Azure Troubleshooting](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/) (Diagnosticare e risolvere problemi con la risoluzione dei problemi di Azure).

![Informazioni sulla risoluzione dei problemi][TS]

### <a name="resource-health"></a>Integrità delle risorse

Il controllo dell'integrità delle risorse di rete è disponibile su base periodica. Le risorse includono il gateway VPN e il tunnel VPN. Il controllo dell'integrità delle risorse è accessibile nel portale di Azure. Per altre informazioni sull'integrità delle risorse, vedere [Panoramica su Integrità risorse di Azure](../resource-health/resource-health-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce Network Watcher è possibile imparare a:

Eseguire un'acquisizione pacchetti nella macchina virtuale. Vedere in proposito l'articolo relativo all'[acquisizione pacchetti variabile nel portale di Azure](network-watcher-packet-capture-manage-portal.md).

Eseguire il monitoraggio attivo e la diagnostica usando l'[acquisizione pacchetti attivata da avvisi](network-watcher-alert-triggered-packet-capture.md).

Rilevare vulnerabilità della sicurezza con l'[analisi dell'acquisizione pacchetti tramite Wireshark](network-watcher-deep-packet-inspection.md), usando strumenti open source.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png












