---
title: Analisi di eventi di Azure Service Fabric con Azure Application Insights | Microsoft Docs
description: Informazioni sulla visualizzazione e l'analisi di eventi con Application Insights per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
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
ms.openlocfilehash: 4085a607b800f4f4f155cdc266bc203b0858fd7c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analisi e visualizzazione degli eventi con Application Insights

Azure Application Insights è una piattaforma estendibile per la diagnostica e il monitoraggio dell'applicazione. Include un potente strumento di analisi ed esecuzione di query, visualizzazione e dashboard personalizzabili e altre opzioni tra cui gli avvisi automatizzati. È la piattaforma consigliata per il monitoraggio e la diagnostica di servizi e applicazioni di Service Fabric.

## <a name="setting-up-application-insights"></a>Configurazione di Application Insights

### <a name="creating-an-ai-resource"></a>Creazione di una risorsa AI

Per creare una risorsa AI, visitare Azure Marketplace e cercare "Application Insights". Viene visualizzata come la prima soluzione nella categoria "Web e dispositivi mobili". Fare clic su **Crea** mentre si controllano le risorse sulla destra. Verificare che il percorso corrisponda a quello mostrato nell'immagine di seguito.

![Nuova risorsa di Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

È necessario inserire alcune informazioni per eseguire correttamente il provisioning della risorsa. Nel campo *Tipo di applicazione* usare "Applicazione Web ASP.NET" se si intende usare di uno dei modelli di programmazione di Service Fabric o se si intende pubblicare un'applicazione .NET nel cluster. Se si intende distribuire contenitori e file eseguibili guest, usare "Generale". In generale usare l'opzione predefinita "Applicazione Web ASP.NET" per tenere aperte le opzioni disponibili in futuro. L'utente può scegliere il nome in base alle proprie preferenze e sia la sottoscrizione sia il gruppo di risorse sono modificabile in seguito alla distribuzione della risorsa. Si consiglia di inserire la risorsa AI nello stesso gruppo di risorse del cluster. Per altre informazioni, vedere [Creare una risorsa di Application Insights](../application-insights/app-insights-create-new-resource.md)

Per configurare AI con lo strumento di aggregazione di eventi è necessaria la chiave di strumentazione AI. La configurazione della risorsa AI può richiedere pochi minuti dopo la convalida della distribuzione. In seguito passare alla risorsa e cercare la sezione **Proprietà** nella barra di spostamento a sinistra. Verrà visualizzato un nuovo pannello che mostra una *CHIAVE DI STRUMENTAZIONE*. Qui è anche possibile modificare la sottoscrizione o il gruppo di risorse della risorsa.

### <a name="configuring-ai-with-wad"></a>Configurazione di AI con WAD

Per inviare i dati da WAD ad Azure AI esistono due modalità, realizzabili aggiungendo un sink AI alla configurazione di WAD, come descritto in [questo articolo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Aggiungere una chiave di strumentazione AI durante la creazione di un cluster nel portale di Azure

![Aggiunta di un AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Quando si crea un cluster, se la diagnostica è attiva, si visualizzerà un campo facoltativo per immettere una chiave di strumentazione di Application Insights. Se si incolla la chiave di strumentazione AI qui, il sink AI verrà automaticamente configurato per l'utente nel modello di Resource Manager che viene usato per distribuire il cluster.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Aggiungere il sink AI al modello di Resource Manager

Nel modello di Resource Manager, in "WadCfg" aggiungere un "Sink" apportando le due modifiche seguenti:

1. Aggiungere la configurazione del sink:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Includere il sink in DiagnosticMonitorConfiguration aggiungendo la riga seguente in "DiagnosticMonitorConfiguration" di "WadCfg":

    ```json
    "sinks": "applicationInsights"
    ```

In entrambi i frammenti di codice precedenti il nome "applicationInsights" è stato usato per descrivere il sink. Questo non è un requisito e fino a quando il nome del sink è incluso in "sink", è possibile impostare il nome per qualsiasi stringa.

Attualmente, i registri del cluster verranno mostrati come tracce nel visualizzatore di log di AI. Poiché la maggior parte delle tracce provenienti dal livello di infrastruttura è di tipo "Informazioni", è anche possibile modificare la configurazione del sink per inviare solo i log di tipo "Critico" o "Errore". Questa operazione può essere eseguita aggiungendo i "Canali" al sink, come illustrato in [questo articolo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Se si usa una chiave di strumentazione di AI errata nel portale o nel modello di Resource Manager, è necessario modificare la chiave e aggiornare il cluster o ridistribuirlo manualmente. 

### <a name="configuring-ai-with-eventflow"></a>Configurazione di AI con EventFlow

Se si usa EventFlow per aggregare gli eventi, assicurarsi di importare il pacchetto NuGet `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`. È necessario includere nella sezione *output* di *eventFlowConfig.json* il codice seguente:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

Assicurarsi di apportare le modifiche necessarie nei filtri, nonché di includere altri input, insieme ai rispettivi pacchetti NuGet.

## <a name="aisdk"></a>AI.SDK

In genere è consigliabile usare EventFlow e WAD come soluzioni di aggregazione, in quanto consentono di usare un approccio più modulare alla diagnostica e al monitoraggio. Ad esempio se si desidera modificare l'output da EventFlow non sono necessarie modifiche alla strumentazione effettiva, ma solo una semplice modifica al file di configurazione. Se, tuttavia, si decide di investire nell'uso di Application Insights e non si desidera passare a un'altra piattaforma, è consigliabile eseguire un'analisi usando il nuovo SDK di AI per l'aggregazione di eventi e l'invio ad AI. Ciò significa che non è più necessario configurare EventFlow per l'invio di dati AI, ma si dovrà installare il pacchetto NuGet di Service Fabric di ApplicationInsight. I dettagli del pacchetto sono disponibili [qui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Supporto di Application Insights per microservizi e contenitori](https://azure.microsoft.com/app-insights-microservices/) descrive alcune delle nuove funzionalità su cui si sta lavorando, attualmente ancora in versione beta, che consentono di avere opzioni di monitoraggio con AI pronte all'uso più ricche. Sono inclusi il rilevamento delle dipendenze, usato nella creazione di un AppMap per tutti i servizi e le applicazioni in un cluster e nella comunicazione tra di essi, e una migliore correlazione delle tracce provenienti dai servizi che consente di conoscere meglio un problema nel flusso di lavoro di un'app o di un servizio.

Se si sviluppa in .NET e si intende usare alcuni modelli di programmazione di Service Fabric e AI come piattaforma per la visualizzazione e l'analisi dei dati dell'evento e del log, è consigliabile usare la route AI SDK come flusso di lavoro di monitoraggio e diagnostica. Lettura [questo articolo](../application-insights/app-insights-asp-net-more.md) e [questo argomento](../application-insights/app-insights-asp-net-trace-logs.md) per iniziare a usare AI per raccogliere e visualizzare i log.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Spostamento della risorsa AI nel portale di Azure

Dopo aver configurato AI come output per log ed eventi, le informazioni verranno visualizzate nella risorsa AI in pochi minuti. Passare alla risorsa AI, che consente di accedere al dashboard della risorsa AI. Fare clic su **Cerca** nella barra delle applicazioni per visualizzare le tracce più recenti ricevute e applicare un filtro.

*Esplora metriche* è uno strumento utile per la creazione di dashboard personalizzati in base alle metriche che possono essere segnalate da applicazioni, servizi e cluster. Vedere [Esaminare le metriche in Application Insights](../application-insights/app-insights-metrics-explorer.md) per configurare alcuni grafici per se stessi in base ai dati raccolti.

Facendo clic su **Analisi** si aprirà il portale di Analisi di Application Insights, dove è possibile eseguire query di eventi e tracce con ambito e operazioni facoltative maggiori. Per altre informazioni su questo argomento leggere [Analytics in Application Insights](../application-insights/app-insights-analytics.md) (Analisi in Application Insights).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare gli avvisi in AI](../application-insights/app-insights-alerts.md) per ricevere una notifica sulle modifiche apportate alle prestazioni o all'uso
* [Rilevamento intelligente in Application Insights](../application-insights/app-insights-proactive-diagnostics.md) esegue un'analisi proattiva dei dati di telemetria che vengono inviati ad AI per avvisare l'utente in caso di potenziali problemi di prestazioni