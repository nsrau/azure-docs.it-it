---
title: Registrazione diagnostica
titleSuffix: Azure Cognitive Services
description: Questa guida fornisce istruzioni dettagliate per abilitare la registrazione diagnostica per un servizio cognitivo di Azure. Questi log forniscono dati avanzati e frequenti sul funzionamento di una risorsa che vengono usati per l'identificazione e il debug dei problemi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827905"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Abilitare la registrazione diagnostica per servizi cognitivi di Azure

Questa guida fornisce istruzioni dettagliate per abilitare la registrazione diagnostica per un servizio cognitivo di Azure. Questi log forniscono dati avanzati e frequenti sul funzionamento di una risorsa che vengono usati per l'identificazione e il debug dei problemi. Prima di continuare, è necessario avere un account Azure con una sottoscrizione ad almeno un servizio cognitivo, ad esempio [ricerca Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [servizi vocali](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)o [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Prerequisiti

Per abilitare la registrazione diagnostica, è necessario un punto in cui archiviare i dati di log. Questa esercitazione Usa archiviazione di Azure e Log Analytics.

* [Archiviazione di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) : conserva i log di diagnostica per il controllo dei criteri, l'analisi statica o il backup. L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della risorsa che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) : strumento di ricerca e analisi dei log flessibile che consente di analizzare i log non elaborati generati da una risorsa di Azure.

> [!NOTE]
> Sono disponibili altre opzioni di configurazione. Per altre informazioni, vedere [raccogliere e utilizzare i dati di log dalle risorse di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Abilitare la raccolta di log di diagnostica  

Per iniziare, abilitare la registrazione diagnostica usando il portale di Azure.

> [!NOTE]
> Per abilitare questa funzionalità usando PowerShell o l'interfaccia della riga di comando di Azure, usare le istruzioni fornite in [raccogliere e utilizzare i dati di log dalle risorse di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

1. Passare al portale di Azure. Individuare e selezionare una risorsa Servizi cognitivi. Ad esempio, la sottoscrizione Ricerca Web Bing.   
2. Quindi, nel menu di spostamento a sinistra individuare **monitoraggio** e selezionare impostazioni di **diagnostica**. Questa schermata contiene tutte le impostazioni di diagnostica create in precedenza per questa risorsa.
3. Se si vuole usare una risorsa creata in precedenza, è possibile selezionarla ora. In caso contrario, selezionare **+ Aggiungi impostazioni di diagnostica**.
4. Immettere un nome per l'impostazione. Selezionare quindi **archivia in un account di archiviazione** e **Invia a log Analytics**.
5. Quando viene richiesto di configurare, selezionare l'account di archiviazione e l'area di lavoro OMS che si vuole usare per archiviare i log di diagnostica. **Nota**: Se non si dispone di un account di archiviazione o di un'area di lavoro OMS, seguire le istruzioni per crearne uno.
6. Selezionare **Audit**, **RequestResponse**e **AllMetrics**. Impostare quindi il periodo di conservazione per i dati del log di diagnostica. Se un criterio di conservazione è impostato su zero, gli eventi per tale categoria di log vengono archiviati per un periodo illimitato.
7. Fare clic su **Salva**.

Potrebbero essere necessarie fino a due ore prima che i dati di registrazione siano disponibili per eseguire query e analizzare. Quindi, non c'è da preoccuparsi se non vengono visualizzati immediatamente.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visualizzare ed esportare i dati di diagnostica da archiviazione di Azure

Archiviazione di Azure è una soluzione di archiviazione di oggetti affidabile ottimizzata per l'archiviazione di grandi quantità di dati non strutturati. In questa sezione si apprenderà come eseguire una query sull'account di archiviazione per le transazioni totali in un intervallo di tempo di 30 giorni ed esportare i dati in Excel.

1. Dalla portale di Azure individuare la risorsa di archiviazione di Azure creata nell'ultima sezione.
2. Dal menu di spostamento a sinistra individuare **monitoraggio** e selezionare **metriche**.
3. Utilizzare gli elenchi a discesa disponibili per configurare la query. Per questo esempio, è possibile impostare l'intervallo di tempo sugli **ultimi 30 giorni** e la metrica su **Transaction**.
4. Al termine della query, verrà visualizzata una visualizzazione della transazione negli ultimi 30 giorni. Per esportare questi dati, usare il pulsante **Esporta in Excel che** si trova nella parte superiore della pagina.

Altre informazioni sulle operazioni che è possibile eseguire con i dati di diagnostica in [archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Visualizzare i log in Log Analytics

Seguire queste istruzioni per esplorare i dati di log Analytics per la risorsa.

1. Dalla portale di Azure individuare e selezionare **log Analytics** dal menu di spostamento a sinistra.
2. Individuare e selezionare la risorsa creata durante l'abilitazione della diagnostica.
3. In **generale**individuare e selezionare **log**. Da questa pagina è possibile eseguire query sui log.

### <a name="sample-queries"></a>Query di esempio

Di seguito sono riportate alcune query kusto di base che è possibile usare per esplorare i dati di log.

Eseguire la query per tutti i log di diagnostica dei servizi cognitivi di Azure per un periodo di tempo specificato:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Eseguire la query per visualizzare i 10 log più recenti:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Eseguire la query per raggruppare le operazioni per **risorsa**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Eseguire questa query per trovare il tempo medio necessario per eseguire un'operazione:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Eseguire la query per visualizzare il volume delle operazioni nel tempo diviso da OperationName con conteggi contenitori per ogni 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come abilitare la registrazione e le metriche e le categorie di log supportate dai vari servizi di Azure, vedere la [Panoramica delle metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) in Microsoft Azure e panoramica degli articoli relativi ai [log di diagnostica di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) .
* Per informazioni sugli hub eventi, leggere gli articoli seguenti:
  * [Che cos'è l'hub di eventi di Azure?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Introduzione all'Hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Leggere [Scaricare le metriche e i log di diagnostica da Archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Leggere [informazioni sulle ricerche nei log nei log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
