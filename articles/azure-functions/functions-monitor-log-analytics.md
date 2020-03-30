---
title: Monitoraggio delle funzioni di Azure con i log di Monitoraggio di AzureMonitoring Azure Functions with Azure Monitor Logs
description: Informazioni su come usare i log di Monitoraggio di Azure con Funzioni di Azure per monitorare le esecuzioni delle funzioni.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649875"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitoraggio delle funzioni di Azure con i log di Monitoraggio di AzureMonitoring Azure Functions with Azure Monitor Logs

Funzioni di Azure offre un'integrazione con i log di Monitoraggio di [Azure](../azure-monitor/platform/data-platform-logs.md) per monitorare le funzioni. Questo articolo illustra come configurare Funzioni di Azure per inviare log generati dal sistema e generati dall'utente ai log di Monitoraggio di Azure.This article shows you how to configure Azure Functions to send system-generated logs to Azure Monitor Logs.

I log di Monitoraggio di Azure offrono la possibilità di consolidare i log di risorse diverse nella stessa area di lavoro, in cui possono essere analizzati con [le query](../azure-monitor/log-query/log-query-overview.md) per recuperare, consolidare e analizzare rapidamente i dati raccolti.  È possibile creare e testare query usando [Log Analytics](../azure-monitor/log-query/portals.md) nel portale di Azure e quindi analizzare direttamente i dati usando questi strumenti oppure salvare le query per usarle con [visualizzazioni](../azure-monitor/visualizations.md) o [regole degli avvisi](../azure-monitor/platform/alerts-overview.md).

Monitoraggio di Azure usa una versione del [linguaggio di query Kusto](/azure/kusto/query/), usato da Esplora dati di Azure e adatto a query semplici nei log, ma che include anche funzionalità avanzate come le aggregazioni, i join e le analisi intelligenti. È possibile apprendere rapidamente il linguaggio di query usando le [numerose lezioni](../azure-monitor/log-query/get-started-queries.md) disponibili.

> [!NOTE]
> L'integrazione con i log di Monitoraggio di Azure è attualmente in anteprima pubblica per le app per le funzioni in esecuzione nei piani di utilizzo di Windows, Premium e dedicati.

## <a name="setting-up"></a>Configurazione

Nella sezione **Monitoraggio** selezionare **Impostazioni di diagnostica** e quindi fare clic su Aggiungi **impostazione diagnostica**.

![Aggiungere un'impostazione di diagnosticaAdd a diagnostic setting](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Nella pagina **Impostazioni di diagnostica** scegliere Invia a Log **Analytics**e quindi selezionare l'area di lavoro di Log Analytics. In **log** scegliere **FunctionAppLogs**, questa tabella contiene i log desiderati.

![Aggiungere un'impostazione di diagnosticaAdd a diagnostic setting](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Registri generati dall'utente

Per generare log personalizzati, è possibile usare l'istruzione di registrazione specifica a seconda del linguaggio, ecco frammenti di codice di esempio:To generate custom logs, you can use the specific logging statement depending on your language, here are sample code snippets:


# <a name="c"></a>[C #](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Esecuzione di query sui log

Per eseguire una query sui log generati, passare all'area di lavoro di Log Analytics configurata per l'invio dei log delle funzioni e fare clic su **Registri**.

![Finestra Query nell'area di lavoro di Los Angeles](media/functions-monitor-log-analytics/querying.png)

Funzioni di Azure scrive tutti i log nella tabella **FunctionAppLogs,** ecco alcune query di esempio.

### <a name="all-logs"></a>Tutti i registri

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Una funzione specifica registra

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Eccezioni

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Passaggi successivi

- Esaminare la [panoramica di Funzioni](functions-overview.md) di AzureReview the Azure Functions overview
- Altre informazioni sui log di Monitoraggio di [AzureLearn](../azure-monitor/platform/data-platform-logs.md) more about Azure Monitor Logs
- Ulteriori informazioni sulla lingua di [query](../azure-monitor/log-query/get-started-queries.md).
