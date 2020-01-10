---
title: Monitoraggio di funzioni di Azure con i log di monitoraggio di Azure
description: Informazioni su come usare i log di monitoraggio di Azure con funzioni di Azure per monitorare le esecuzioni di funzioni.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: f4af646569edc8a9274af752e7e4f2a36585ae4d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769099"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitoraggio di funzioni di Azure con i log di monitoraggio di Azure

Funzioni di Azure offre un'integrazione con i [log di monitoraggio di Azure](../azure-monitor/platform/data-platform-logs.md) per monitorare le funzioni. Questo articolo illustra come configurare funzioni di Azure per inviare log generati dal sistema e generati dall'utente ai log di monitoraggio di Azure.

I log di monitoraggio di Azure offrono la possibilità di consolidare i log da diverse risorse nella stessa area di lavoro, dove possono essere analizzati con le [query](../azure-monitor/log-query/log-query-overview.md) per recuperare, consolidare e analizzare rapidamente i dati raccolti.  È possibile creare e testare query usando [Log Analytics](../azure-monitor/log-query/portals.md) nel portale di Azure e quindi analizzare direttamente i dati usando questi strumenti oppure salvare le query per usarle con [visualizzazioni](../azure-monitor/visualizations.md) o [regole degli avvisi](../azure-monitor/platform/alerts-overview.md).

Monitoraggio di Azure usa una versione del [linguaggio di query Kusto](/azure/kusto/query/), usato da Esplora dati di Azure e adatto a query semplici nei log, ma che include anche funzionalità avanzate come le aggregazioni, i join e le analisi intelligenti. È possibile apprendere rapidamente il linguaggio di query usando le [numerose lezioni](../azure-monitor/log-query/get-started-queries.md) disponibili.

> [!NOTE]
> L'integrazione con i log di monitoraggio di Azure è attualmente disponibile in anteprima pubblica per le app per le funzioni in esecuzione in Windows, Premium e piani di hosting dedicati.

## <a name="setting-up"></a>Configurazione

Nella sezione **monitoraggio** selezionare impostazioni di **diagnostica** e quindi fare clic su **Aggiungi impostazione di diagnostica**.

![Aggiungere un'impostazione di diagnostica](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Nella pagina **impostazioni di diagnostica** scegliere **Invia a log Analytics**, quindi selezionare l'area di lavoro log Analytics. In **log** scegliere **FunctionAppLogs**, questa tabella contiene i log desiderati.

![Aggiungere un'impostazione di diagnostica](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Log generati dall'utente

Per generare log personalizzati, è possibile usare l'istruzione di registrazione specifica a seconda del linguaggio. di seguito sono riportati i frammenti di codice di esempio:


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Esecuzione di query sui log

Per eseguire una query sui log generati, passare all'area di lavoro Log Analytics configurata per l'invio dei log delle funzioni e fare clic su **log**.

![Finestra query nell'area di lavoro LA](media/functions-monitor-log-analytics/querying.png)

Funzioni di Azure scrive tutti i log nella tabella **FunctionAppLogs** . di seguito sono riportate alcune query di esempio.

### <a name="all-logs"></a>Tutti i log

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Log di funzione specifici

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

- Vedere [Panoramica di funzioni di Azure](functions-overview.md)
- Altre informazioni sui [log di monitoraggio di Azure](../azure-monitor/platform/data-platform-logs.md)
- Altre informazioni sul [linguaggio di query](../azure-monitor/log-query/get-started-queries.md).
