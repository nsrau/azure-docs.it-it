---
title: Analisi di eventi di Azure Service Fabric con Azure Application Insights | Microsoft Docs
description: Informazioni sulla visualizzazione e l'analisi di eventi con Application Insights per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 8e682a5c768ed4b3f35382c87528c1b0d11a3c3d
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019709"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analisi e visualizzazione degli eventi con Application Insights

Application Insights, all'interno di Monitoraggio di Azure, è una piattaforma estendibile per la diagnostica e il monitoraggio delle applicazioni. Include un potente strumento di analisi ed esecuzione di query, visualizzazione e dashboard personalizzabili e altre opzioni tra cui gli avvisi automatizzati. L'integrazione di Application Insights con Service Fabric include l'uso degli strumenti per Visual Studio e il portale di Azure, oltre a metriche specifiche di Service Fabric, offrendo un'esperienza di registrazione completa fin dall'inizio. Nonostante molti log vengano creati e raccolti in modo automatico con Application Insights, è consigliabile aggiungere altre registrazioni personalizzate alle applicazioni per creare un'esperienza di diagnostica più completa.

Questo articolo offre informazioni utili per rispondere alle domande frequenti che seguono:

* Come si fa a sapere cosa accade all'interno dell'applicazione e dei servizi e a raccogliere dati di telemetria?
* Come si possono risolvere i problemi dell'applicazione, in particolare per le comunicazioni tra i servizi?
* Come si possono ottenere metriche sulle prestazioni dei servizi, ad esempio, tempo di caricamento della pagina o richieste HTTP?

Lo scopo di questo articolo è illustrare come ottenere informazioni dettagliate e risolvere i problemi dall'interno di Application Insights. Per informazioni su come installare e configurare Application Insights con Service Fabric, vedere questa [esercitazione](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitoraggio in Application Insights

Application Insights è perfettamente compatibile con l'uso di Service Fabric. Nella pagina di panoramica Application Insights fornisce informazioni chiave sul servizio, ad esempio il tempo di risposta e il numero di richieste elaborate. Facendo clic sul pulsante 'Cerca' nella parte superiore, è possibile visualizzare un elenco delle richieste recenti nell'applicazione. In questa posizione verranno inoltre visualizzate le richieste non riuscite e sarà possibile diagnosticare gli errori che possono verificarsi.

![Panoramica di Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Nel riquadro di destra nell'immagine precedente, esistono due tipi principali di voci nell'elenco: le richieste e gli eventi. Le richieste sono chiamate effettuate all'API dell'app tramite richieste HTTP in questo caso, e gli eventi sono gli eventi personalizzati, che fungono da strumenti di telemetria che è possibile aggiungere in qualsiasi punto nel codice. È possibile esplorare ulteriormente la strumentazione delle applicazioni in [API di Application Insights per metriche ed eventi personalizzati](../azure-monitor/app/api-custom-events-metrics.md). Quando si fa clic su una richiesta vengono visualizzati altri dettagli come illustrato nella figura seguente, inclusi i dati specifici per Service Fabric, raccolti nel pacchetto NuGet di Application Insights Service Fabric. Queste informazioni sono utili per la risoluzione dei problemi e per stabilire qual è lo stato dell'applicazione. È inoltre possibile eseguire ricerche in tutte queste informazioni all'interno di Application Insights.

![Dettagli delle richieste di Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights include una visualizzazione designata per l'esecuzione di query su tutti i dati in arrivo. Fare clic su "Esplora metriche" nella parte superiore della pagina Panoramica per passare al portale di Application Insights. Qui è possibile eseguire query sugli eventi personalizzati menzionati in precedenza, sulle richieste, le eccezioni, i contatori delle prestazioni e altre metriche usando il linguaggio di query Kusto. L'esempio seguente mostra tutte le richieste nell'ultima ora.

![Dettagli delle richieste di Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Per esplorare ulteriormente le funzionalità del portale di Application Insights, passare alla [documentazione del portale di Application Insights](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Configurazione di Application Insights con EventFlow

Se si usa EventFlow per aggregare gli eventi, assicurarsi di importare il pacchetto NuGet `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`. Il codice seguente è obbligatorio nella sezione *outputs* di *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Assicurarsi di apportare le modifiche necessarie nei filtri, nonché di includere altri input, insieme ai rispettivi pacchetti NuGet.

## <a name="application-insights-sdk"></a>Application Insights SDK

È consigliabile usare EventFlow e WAD come soluzioni di aggregazione, in quanto consentono di usare un approccio più modulare alla diagnostica e al monitoraggio. Ad esempio se si vuole modificare l'output da EventFlow non sono necessarie modifiche alla strumentazione effettiva, ma solo una semplice modifica al file di configurazione. Se, tuttavia, si decide di investire nell'uso di Application Insights e non si desidera passare a un'altra piattaforma, è consigliabile eseguire un'analisi usando il nuovo SDK di Application Insights per l'aggregazione di eventi e l'invio ad Application Insights. Ciò significa che non è più necessario configurare EventFlow per l'invio di dati ad Application Insights, ma si dovrà installare il pacchetto NuGet di Service Fabric di Application Insights. I dettagli del pacchetto sono disponibili [qui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights support for Microservices and Containers](https://azure.microsoft.com/blog/app-insights-microservices/) (Supporto di Application Insights per microservizi e contenitori) descrive alcune delle nuove funzionalità su cui si sta lavorando, attualmente ancora in versione beta, che consentono di avere opzioni di monitoraggio con Application Insights pronte all'uso più ricche. Sono inclusi il rilevamento delle dipendenze, usato nella creazione di un AppMap per tutti i servizi e le applicazioni in un cluster e nella comunicazione tra di essi, e una migliore correlazione delle tracce provenienti dai servizi che consente di conoscere meglio un problema nel flusso di lavoro di un'applicazione o di un servizio.

Se si sviluppa in .NET e si intende usare alcuni modelli di programmazione di Service Fabric e Application Insights come piattaforma per la visualizzazione e l'analisi dei dati dell'evento e del log, è consigliabile usare la route SDK di Application Insights come flusso di lavoro di monitoraggio e diagnostica. Leggere [questo articolo](../azure-monitor/app/asp-net-more.md) e [questo argomento](../azure-monitor/app/asp-net-trace-logs.md) per iniziare a usare Application Insights per raccogliere e visualizzare i log.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Passare alla risorsa di Application Insights nel portale di Azure

Dopo aver configurato Application Insights come output per log ed eventi, le informazioni verranno visualizzate nella risorsa di Application Insights in pochi minuti. Passare alla risorsa di Application Insights che consente di accedere al dashboard della risorsa di Application Insights. Fare clic su **Cerca** nella barra delle applicazioni di Application Insights per visualizzare le tracce più recenti ricevute e applicare un filtro.

*Esplora metriche* è uno strumento utile per la creazione di dashboard personalizzati in base alle metriche che possono essere segnalate da applicazioni, servizi e cluster. Vedere [Esaminare le metriche in Application Insights](../azure-monitor/app/metrics-explorer.md) per configurare alcuni grafici per se stessi in base ai dati raccolti.

Facendo clic su **Analisi** si aprirà il portale di Analisi di Application Insights, dove è possibile eseguire query di eventi e tracce con ambito e operazioni facoltative maggiori. Per altre informazioni su questo argomento leggere [Analytics in Application Insights](../azure-monitor/app/analytics.md) (Analisi in Application Insights).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare gli avvisi in AI](../azure-monitor/app/alerts.md) per ricevere una notifica sulle modifiche apportate alle prestazioni o all'uso
* [Rilevamento intelligente in Application Insights](../azure-monitor/app/proactive-diagnostics.md) esegue un'analisi proattiva dei dati di telemetria che vengono inviati ad Application Insights per avvisare l'utente in caso di potenziali problemi di prestazioni
