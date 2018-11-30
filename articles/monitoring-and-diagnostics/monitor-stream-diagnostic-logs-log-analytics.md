---
title: Trasmettere i log di diagnostica di Azure a Log Analytics
description: Informazioni su come trasmettere log di diagnostica di Azure a un'area di lavoro di Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: fe1557a6f9e5fd4e463af254fa1dd52726e73024
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713045"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Trasmettere i log di diagnostica di Azure a Log Analytics

**[I log di diagnostica Azure](monitoring-overview-of-diagnostic-logs.md)** possono essere trasmessi quasi in tempo reale ad Azure Log Analytics usando il portale, i cmdlet PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Che cosa si può fare con i log di diagnostica in Log Analytics

Log Analytics di Azure è uno strumento di analisi e ricerca log flessibile che consente di conoscere i dati di registro non elaborati generati da risorse di Azure. Le funzionalità includono:

* **Ricerca log**: scrivere query avanzate sui dati di registro, correlare i log da varie origini e persino generare grafici che possono essere aggiunti al dashboard di Azure.
* **Avvisi**: rilevare quando uno o più eventi corrispondano a una particolare query e vengono notificati con una mail o una chiamata webhook.
* **Soluzioni**: usare le viste predefinite e i dashboard che forniscono informazioni dettagliate e immediate sui dati di registro.
* **Analisi avanzata**: applicare algoritmi di machine learning e criteri di ricerca per identificare i possibili problemi risultanti dai log.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Abilitare la trasmissione dei log di diagnostica a Log Analytics

È possibile abilitare la trasmissione dei log di diagnostica a livello di codice tramite il portale o tramite le [API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). In entrambi i casi, si crea un'impostazione di diagnostica in cui specificare un'area di lavoro di Log Analytics, le categorie di log e le metriche da inviare all'area di lavoro. Una **categoria di log** di diagnostica è un tipo di log che una risorsa può fornire.

L'area di lavoro di Log Analytics non deve trovarsi nella stessa sottoscrizione della risorsa che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni.

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Eseguire lo streaming dei log di diagnostica usando il portale
1. Nel portale passare a Monitoraggio di Azure e fare clic su **Impostazioni di diagnostica**

    ![Sezione relativa al monitoraggio di Monitoraggio di Azure](media/monitor-stream-diagnostic-logs-log-analytics/diagnostic-settings-blade.png)

2. Facoltativamente filtrare l'elenco in base al tipo di risorsa o al gruppo di risorse, quindi fare clic sulla risorsa per cui si vuole specificare un'impostazione di diagnostica.

3. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su "Attiva diagnostica".

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/monitor-stream-diagnostic-logs-log-analytics/diagnostic-settings-none.png)

   Se esistono già impostazioni sulla risorsa, verrò visualizzato un elenco di impostazioni già configurate per questa risorsa. Fare clic su "Add diagnostic setting" (Aggiungi impostazione di diagnostica).

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/monitor-stream-diagnostic-logs-log-analytics/diagnostic-settings-multiple.png)

3. Assegnare all'impostazione un nome e selezionare la casella **Invia a Log Analytics**, quindi selezionare un'area di lavoro di Log Analytics.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/monitor-stream-diagnostic-logs-log-analytics/diagnostic-settings-configure.png)

4. Fare clic su **Save**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e vengono trasmessi i log di diagnostica a tale area di lavoro non appena vengono generati nuovi dati di eventi. Si noti che potrebbero passare fino a quindici minuti tra l'emissione di un evento e la sua apparizione in Log Analytics.

### <a name="via-powershell-cmdlets"></a>Tramite i cmdlet di PowerShell
Per abilitare la trasmissione tramite i [cmdlet di Azure PowerShell](insights-powershell-samples.md), è possibile usare il cmdlet `Set-AzureRmDiagnosticSetting` con i parametri seguenti:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Si noti che la proprietà ID dell'area di lavoro assume l'intero ID della risorsa Azure dell'area di lavoro e non l'ID/chiave dell'area di lavoro visualizzata nel portale Log Analytics.

### <a name="via-azure-cli"></a>Tramite l'interfaccia della riga di comando di Azure

Per abilitare lo streaming tramite l'[interfaccia della riga di comando di Azure](insights-cli-samples.md), è possibile usare il comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

È possibile aggiungere altre categorie al log di diagnostica aggiungendo dizionari alla matrice JSON passata come parametro `--logs`.

L'argomento `--resource-group` è obbligatorio solo se `--workspace` non è un ID oggetto.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Come eseguire query sui dati in Log Analytics

Nel pannello Ricerca log del portale o nell'esperienza Analisi avanzata come parte di Log Analytics, è possibile eseguire query sui log di diagnostica come parte della soluzione di gestione dei log sotto la tabella AzureDiagnostics. Sono anche disponibili [diverse soluzioni per le risorse di Azure](../azure-monitor/insights/solutions.md) che è possibile installare per ottenere informazioni dettagliate e immediate per i dati di log inviati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui log di diagnostica di Azure](monitoring-overview-of-diagnostic-logs.md)
