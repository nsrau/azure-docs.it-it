---
title: Panoramica di monitoraggio e diagnostica di Azure Service Fabric | Microsoft Docs
description: Informazioni sul monitoraggio e la diagnostica per i cluster, le applicazioni e i servizi di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 571c31b9e6514b44d6a8a69fe8a6a0806e4b80e3
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoraggio e diagnostica in Azure Service Fabric

Il monitoraggio e la diagnostica sono essenziali per lo sviluppo, il test e la distribuzione di applicazioni e servizi in qualsiasi ambiente. Le soluzioni di Service Fabric funzionano meglio quando si pianifica e si implementa il monitoraggio e la diagnostica che consentono di verificare che le applicazioni e i servizi funzionino come previsto in un ambiente di sviluppo locale o in fase di produzione.

Gli obiettivi principali di monitoraggio e diagnostica sono:
* Rilevare e diagnosticare i problemi di hardware e infrastruttura
* Rilevare i problemi di software e app, ridurre i tempi di inattività del servizio
* Comprendere l'utilizzo delle risorse e supportare le decisioni operative
* Ottimizzare le prestazioni delle applicazioni, dei servizi e delle infrastrutture
* Generare informazioni aziendali dettagliate e identificare le aree di miglioramento


Il flusso di lavoro generale di monitoraggio e diagnostica è costituito da tre passaggi:

1. **Generazione di eventi**: sono inclusi eventi, ovvero log, tracce, eventi personalizzati, sia a livello di infrastruttura, cioè cluster, che a livello di applicazione/servizio
2. **Aggregazione di eventi**: gli eventi generati devono essere raccolti e aggregati prima di poter essere visualizzati
3. **Analisi**: gli eventi devono essere visualizzati e devono essere accessibili in un formato, per consentirne l'analisi e la visualizzazione in base alle esigenze specifiche

Sono disponibili molti prodotti che coprono le tre aree. Gli utenti sono liberi di scegliere tecnologie diverse per ognuno di essi. Per disporre di una soluzione di monitoraggio end-to-end per il cluster, è importante assicurarsi che queste tecnologie funzionino in combinazione.

## <a name="event-generation"></a>Generazione di eventi

Il primo passaggio nel flusso di lavoro del monitoraggio e della diagnostica è la creazione e la generazione di eventi e log. Gli eventi, i log e le tracce possono essere generati da due livelli: dal livello dell'infrastruttura, ovvero elementi del cluster, computer o azioni di Service Fabric, o dal livello dell'applicazione, ovvero qualsiasi strumentazione aggiunta alle app e ai servizi distribuiti nel cluster. Gli eventi di ognuno di questi livelli sono personalizzabili, anche se Service Fabric offre alcuni strumenti per impostazione predefinita.

Per comprendere ciò che viene offerto e come aggiungere altri strumenti leggere altre informazioni su [eventi a livello di infrastruttura](service-fabric-diagnostics-event-generation-infra.md) ed [eventi a livello di applicazione](service-fabric-diagnostics-event-generation-app.md).

Dopo aver deciso quale provider di log usare, è necessario assicurarsi che i log vengono aggregati e archiviati correttamente.

## <a name="event-aggregation"></a>Aggregazione di eventi

Per raccogliere i log e gli eventi generati dalle applicazioni e dal cluster, in genere è consigliabile usare [Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md), più simile alla raccolta di log basata su agenti, o [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md), una raccolta di log nel processo.

Nei servizi di Service Fabric è possibile raccogliere log di applicazioni tramite l'estensione Diagnostica di Azure se il set di origini e destinazioni dei log non cambia spesso e se il mapping tra le origini e le relative destinazioni è semplice. Questo perché la configurazione di Diagnostica di Azure avviene a livello di Resource Manager, pertanto per apportare modifiche importanti alla configurazione è necessario l'aggiornamento o la ridistribuzione del cluster. Inoltre, viene usato al meglio per assicurare che i log vengono archiviati in un luogo più permanente, dove è possibile accedervi tramite varie piattaforme di analisi. Ciò significa che è meno efficiente per una pipeline rispetto all'uso di un'opzione come EventFlow.

L'uso di [EventFlow](https://github.com/Azure/diagnostics-eventflow) consente di usare servizi per inviare i log direttamente a una piattaforma di analisi e visualizzazione e/o di archiviazione. È possibile usare altre librerie, ILogger, Serilog e così via, per lo stesso scopo, ma EventFlow ha il vantaggio di essere stata progettata specificamente per la raccolta di log nel processo e per supportare i servizi di Service Fabric. I potenziali vantaggi che ne derivano sono numerosi:

* Facilità di configurazione e distribuzione
    * La configurazione della raccolta dei dati di diagnostica è solo una parte della configurazione del servizio ed è facile mantenerla sempre "sincronizzata" con il resto dell'applicazione
    * È facile ottenere la configurazione per ogni applicazione o servizio
    * La configurazione delle destinazioni di dati tramite EventFlow dipende solo dall'aggiunta del pacchetto NuGet appropriato e dalla modifica del file *eventFlowConfig.json*
* Flessibilità
    * L'applicazione può inviare i dati ogni volta che è necessario, purché sia disponibile una libreria client che supporta il sistema di archiviazione dati di destinazione. È possibile aggiungere nuove destinazioni in base alle esigenze
    * È possibile implementare complesse regole di acquisizione, filtro e aggregazione dati
* Accesso al contesto e ai dati di applicazione interni
    * Il sottosistema di diagnostica in esecuzione nel processo dell'applicazione o del servizio può facilmente aumentare le tracce con informazioni contestuali

È importante sottolineare che queste due opzioni non si escludono a vicenda. Pertanto se è possibile eseguire un processo simile usando l'uno o l'altro, sarebbe opportuno configurare entrambi. Nella maggior parte dei casi, la combinazione di un agente con la raccolta nel processo potrebbe comportare un flusso di lavoro del monitoraggio più affidabile. L'estensione Diagnostica di Azure (agente) potrebbe essere il percorso scelto per i log a livello di infrastruttura, mentre EventFlow,ovvero la raccolta nel processo, potrebbe essere usata per i log a livello di applicazione. Dopo aver individuato la soluzione più adatta per l'utente, è necessario pensare al modo in cui i dati devono essere analizzati e visualizzati.

## <a name="event-analysis"></a>Analisi di eventi

Per quanto riguarda l'analisi e la visualizzazione dei dati di monitoraggio e diagnostica, nel mercato esistono diverse piattaforme molto funzionali. Le due piattaforme consigliate, [OMS](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), si integrano meglio con Service Fabric, ma si consiglia di considerare anche [Elastic Stack](https://www.elastic.co/products), specialmente se si intende eseguire un cluster in un ambiente non in linea, [Splunk](https://www.splunk.com/) o qualsiasi altra piattaforma preferita.

Nella scelta della piattaforma si deve considerare l'interfaccia utente e le opzioni di query, la possibilità di visualizzare i dati e creare dashboard facilmente leggibili, oltre agli strumenti aggiuntivi offerti per migliorare il monitoraggio, ad esempio gli avvisi automatici.

Oltre alla piattaforma scelta, quando si configura un cluster di Service Fabric come una risorsa di Azure, è anche possibile accedere al servizio di monitoraggio pronto all'uso di Azure per i computer, che risulta utile per il monitoraggio di prestazioni e metriche specifiche.

### <a name="azure-monitor"></a>Monitoraggio di Azure

È possibile usare [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md) per monitorare molte risorse di Azure in cui viene creato un cluster di Service Fabric. Un set di metriche per il [set di scalabilità di macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e per le singole [macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) viene raccolto automaticamente e visualizzato nel portale di Azure. Per visualizzare le informazioni raccolte, nel portale di Azure selezionare il gruppo di risorse che contiene il cluster di Service Fabric. Selezionare quindi il set di scalabilità di macchine virtuali da visualizzare. Nella sezione **Monitoraggio** selezionare **Metriche** per visualizzare un grafico dei valori.

![Visualizzazione del portale di Azure dei dati di metrica raccolti](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Per personalizzare i grafici, seguire le istruzioni disponibili in [Metriche in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). È anche possibile creare avvisi in base a queste metriche, come illustrato in [Creare avvisi in Monitoraggio di Azure per servizi di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). È possibile inviare avvisi a un servizio di notifica usando webhook, come illustrato in [Configurare un webhook in un avviso relativo alle metriche di Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Monitoraggio di Azure supporta solo una sottoscrizione. Se è necessario monitorare più sottoscrizioni o se sono necessarie funzionalità aggiuntive, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), incluso in Microsoft Operations Management Suite, fornisce una soluzione olistica di gestione IT per infrastrutture locali e basate sul cloud. È possibile indirizzare i dati di Monitoraggio di Azure direttamente a Log Analytics, in modo da visualizzare le metriche e i log dell'intero ambiente in un'unica posizione.

## <a name="next-steps"></a>Passaggi successivi

### <a name="watchdogs"></a>Watchdog

Un watchdog è un servizio separato che può controllare l'integrità e il carico tra servizi e creare report di integrità per l'intera gerarchia di modello. Questo consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. Anche i watchdog sono utili per ospitare il codice che esegue azioni correttive senza l'intervento dell'utente, ad esempio, la cancellazione dei file di log nell'archiviazione a determinati intervalli di tempo. È possibile trovare un'implementazione di esempio del servizio watchdog [qui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

Introduzione alla generazione di eventi e log a [livello di infrastruttura](service-fabric-diagnostics-event-generation-infra.md) e a [livello di applicazione](service-fabric-diagnostics-event-generation-app.md).
