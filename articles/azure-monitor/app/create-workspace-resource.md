---
title: Creare una nuova risorsa di Application Insights basata sull'area di lavoro in Monitoraggio di Azure | Microsoft Docs
description: Informazioni sui passaggi necessari per abilitare le nuove risorse di Application Insights basate sull'area di lavoro in Monitoraggio di Azure.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 186d4c510b58e06fcb0b823ca0d5770a2684196e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824988"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Risorse di Application Insights basate sull'area di lavoro (anteprima)

Le risorse basate sull'area di lavoro supportano l'integrazione completa tra Application Insights e Log Analytics. È ora possibile scegliere di inviare i dati di telemetria di Application Insights a un'area di lavoro Log Analytics comune, che consente l'accesso completo a tutte le funzionalità di Log Analytics mantenendo al tempo stesso i log dell'applicazione, dell'infrastruttura e della piattaforma in un'unica posizione consolidata.

Ciò consente anche un controllo degli accessi in base al ruolo comune tra le risorse ed elimina la necessità di eseguire query tra app e aree di lavoro.

> [!NOTE]
> L'inserimento e la conservazione di dati per le risorse di Application Insights basate sull'area di lavoro vengono fatturate tramite l'area di lavoro Log Analytics in cui si trovano i dati. [Altre informazioni]( ./pricing.md#workspace-based-application-insights) sulla fatturazione per le risorse di Application Insights basate sull'area di lavoro.

Per testare la nuova esperienza, accedere al [portale di Azure](https://portal.azure.com) e creare una risorsa di Application Insights:

![Risorsa di Application Insights basata sull'area di lavoro](./media/create-workspace-resource/create-workspace-based.png)

Se non si dispone di un'area di lavoro Log Analytics esistente, [consultare la documentazione relativa alla creazione dell'area di lavoro Log Analytics](../learn/quick-create-workspace.md).

Per l'anteprima pubblica **le risorse basate sull'area di lavoro sono attualmente limitate agli Stati Uniti occidentali 2, agli Stati Uniti orientali e agli Stati Uniti centro-meridionali.**

Dopo aver creato la risorsa, le informazioni corrispondenti sull'area di lavoro vengono visualizzate nel riquadro **Panoramica**:

![Nome dell'area di lavoro](./media/create-workspace-resource/workspace-name.png)

Facendo clic sul testo del collegamento in blu verrà visualizzata l'area di lavoro Log Analytics associata in cui è possibile sfruttare il nuovo ambiente di query unificato dell'area di lavoro.

> [!NOTE]
> È ancora disponibile la completa compatibilità con le versioni precedenti per le query classiche sulle risorse di Application Insights, le cartelle di lavoro e gli avvisi basati su log all'interno dell'esperienza di Application Insights. Per eseguire query o visualizzare la [nuova struttura di tabella o lo schema basati sull'area di lavoro](apm-tables.md) è prima necessario passare all'area di lavoro Log Analytics. Durante l'anteprima, selezionare **Log** all'interno dei riquadri di Application Insights per accedere all'esperienza di query classica di Application Insights.

## <a name="copy-the-connection-string"></a>Copia della stringa di connessione

La [stringa di connessione](./sdk-connection-string.md?tabs=net) identifica la risorsa a cui si vuole associare i dati di telemetria. Consente inoltre di modificare gli endpoint che la risorsa userà come destinazione per la telemetria. Sarà necessario copiare la stringa di connessione e aggiungerla al codice dell'applicazione o a una variabile di ambiente.

## <a name="monitoring-configuration"></a>Configurazione del monitoraggio

Dopo aver creato una risorsa di Application Insights basata sull'area di lavoro, la configurazione del monitoraggio è relativamente semplice.

### <a name="code-based-application-monitoring"></a>Monitoraggio basato su codice delle applicazioni

Per il monitoraggio basato su codice delle applicazioni, è sufficiente installare l'SDK appropriato di Application Insights e associarlo alla chiave di strumentazione o alla stringa di connessione per la risorsa appena creata.  

Per informazioni dettagliate sulla configurazione di un SDK di Application Insights per il monitoraggio basato sul codice, consultare la documentazione specifica del linguaggio o del framework:

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [Attività in background e applicazioni console moderne (.NET/.NET Core)](./worker-service.md)
- [Applicazioni console classiche (.NET)](./console.md) 
- [Java ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Monitoraggio senza codice e creazione di risorse di Visual Studio

Per il monitoraggio senza codice di servizi come Funzioni di Azure e Servizi app di Azure, è necessario creare prima di tutto la risorsa di Application Insights basata sull'area di lavoro, quindi associarla a tale risorsa durante la fase di configurazione del monitoraggio.

Anche se questi servizi offrono la possibilità di creare una nuova risorsa di Application Insights all'interno del proprio processo di creazione, le risorse create tramite queste opzioni dell'interfaccia utente sono attualmente limitate all'esperienza classica di Application Insights.

Lo stesso vale per l'esperienza di creazione delle risorse di Application Insights in Visual Studio per ASP.NET e ASP.NET Core. È necessario selezionare una risorsa esistente basata sull'area di lavoro dall'interfaccia utente di abilitazione del monitoraggio di Visual Studio. Selezionando la creazione di una nuova risorsa da Visual Studio ci si limita a creare una risorsa classica di Application Insights.

## <a name="creating-a-resource-automatically"></a>Creazione automatica di una risorsa

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per accedere all'anteprima dell'interfaccia della riga di comando di Azure di Application Insights, è prima necessario eseguire:

```azurecli
 az extension add -n application-insights
```

Se non si esegue il comando `az extension add`, verrà visualizzato un messaggio di errore che indica: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

A questo punto è possibile eseguire il comando seguente per creare la risorsa di Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Esempio

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Per informazioni dettagliate su questo comando, vedere la [documentazione dell'interfaccia della riga di comando di Azure](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

Il comando di PowerShell `New-AzApplicationInsights` non supporta attualmente la creazione di una ricorsa di Application Insights basata sull'area di lavoro. Per creare una risorsa basata sull'area di lavoro con PowerShell, è possibile usare i modelli di Azure Resource Manager riportati di seguito ed eseguire la distribuzione con PowerShell.

### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

#### <a name="template-file"></a>File modello

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>File dei parametri

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modifica dell'area di lavoro associata

Dopo aver creato una risorsa di Application Insights basata sull'area di lavoro, è possibile modificare l'area di lavoro Log Analytics associata.

Dal riquadro della risorsa di Application Insights selezionare **Proprietà** > **Cambia l'area di lavoro** > **Aree di lavoro di Log Analytics**

## <a name="export-telemetry"></a>Esportare i dati di telemetria

La funzionalità di esportazione continua legacy non è supportata per risorse basate su area di lavoro. Selezionare invece **Impostazioni di diagnostica** > **Aggiungi impostazione di diagnostica** dalla risorsa di Application Insights. È possibile selezionare tutte le tabelle o un subset di tabelle da archiviare in un account di archiviazione o di cui eseguire lo streaming in Hub eventi di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare le metriche](../platform/metrics-charts.md)
* [Scrivere query di Analisi](../log-query/log-query-overview.md)

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md