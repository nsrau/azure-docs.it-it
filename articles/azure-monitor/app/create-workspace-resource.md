---
title: Creare un nuovo monitoraggio di Azure Application Insights risorsa basata sull'area di lavoro | Microsoft Docs
description: Informazioni sui passaggi necessari per abilitare il nuovo monitoraggio di Azure Application Insights risorse basate sull'area di lavoro.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65c2220bd3e9ea93e562b256e84796aed9046d00
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211408"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Risorse Application Insights basate sull'area di lavoro (anteprima)

Le risorse basate sull'area di lavoro supportano l'integrazione completa tra Application Insights e Log Analytics. È ora possibile scegliere di inviare i dati di telemetria Application Insights a un'area di lavoro Log Analytics comune, che consente l'accesso completo a tutte le funzionalità di Log Analytics mantenendo al tempo stesso i log dell'applicazione, dell'infrastruttura e della piattaforma in un unico percorso consolidato.

Questo consente anche il controllo degli accessi in base al ruolo comune tra le risorse e Elimina la necessità di eseguire query tra app o aree di lavoro.

> [!NOTE]
> L'inserimento e la conservazione dei dati per le risorse Application Insights basate sull'area di lavoro vengono fatturate tramite l'area di lavoro Log Analytics in cui si trovano i dati. [Altre]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) informazioni sulla fatturazione per le risorse di Application Insights basate sull'area di lavoro.

Per testare la nuova esperienza, accedere alla [portale di Azure](https://portal.azure.com)e creare una risorsa Application Insights:

![Risorsa Application Insights basata sull'area di lavoro](./media/create-workspace-resource/create-workspace-based.png)

Se non si dispone già di un'area di lavoro Log Analytics, [consultare la documentazione relativa alla creazione dell'area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Per le risorse basate sull'area di lavoro di anteprima pubblica **sono attualmente limitate agli Stati Uniti occidentali 2, Stati Uniti orientali e Stati Uniti centro-meridionali.**

Una volta creata la risorsa, le informazioni sull'area di lavoro corrispondenti vengono visualizzate nel riquadro **Panoramica** :

![Nome area di lavoro](./media/create-workspace-resource/workspace-name.png)

Facendo clic sul testo del collegamento blu verrà visualizzata l'area di lavoro Log Analytics associata in cui è possibile sfruttare il nuovo ambiente di query unificato dell'area di lavoro.

> [!NOTE]
> È ancora disponibile la compatibilità completa con le versioni precedenti per le query di risorse Application Insights classiche, le cartelle di lavoro e gli avvisi basati su log nell'esperienza Application Insights. Per eseguire una query o una vista sulla [nuova struttura o schema della tabella basata sull'area di lavoro](apm-tables.md) , è innanzitutto necessario passare all'area di lavoro log Analytics. Durante l'anteprima, la selezione dei **log** dall'interno dei riquadri Application Insights consentirà di accedere all'esperienza di query Application Insights classica.

## <a name="copy-the-connection-string"></a>Copia della stringa di connessione

La [stringa di connessione]() identifica la risorsa a cui si vuole associare i dati di telemetria. Consente inoltre di modificare gli endpoint che la risorsa userà come destinazione per i dati di telemetria. Sarà necessario copiare la stringa di connessione e aggiungerla al codice dell'applicazione o a una variabile di ambiente.

![Fare clic e copiare la chiave di strumentazione](./media/create-new-resource/instrumentation-key.png)

## <a name="monitoring-configuration"></a>Configurazione del monitoraggio

Una volta creata una risorsa Application Insights basata sull'area di lavoro, la configurazione del monitoraggio è relativamente semplice.

### <a name="code-based-application-monitoring"></a>Monitoraggio delle applicazioni basate su codice

Per il monitoraggio delle applicazioni basate su codice, è sufficiente installare il Application Insights SDK appropriato e indirizzarlo alla chiave di strumentazione o alla stringa di connessione per la risorsa appena creata.  

Per la documentazione dettagliata sulla configurazione di un SDK Application Insights per il monitoraggio basato sul codice, consultare la documentazione specifica del linguaggio o del Framework:

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [Attività in background & applicazioni console moderne (.NET/.NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [Applicazioni console classiche (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Monitoraggio non codificato e creazione di risorse di Visual Studio

Per il monitoraggio senza codice di servizi come funzioni di Azure e servizi di app Azure, è necessario creare prima di tutto la risorsa Application Insights basata sull'area di lavoro, quindi puntare a tale risorsa durante la fase di configurazione del monitoraggio.

Anche se questi servizi offrono la possibilità di creare una nuova risorsa Application Insights all'interno del proprio processo di creazione di risorse, le risorse create tramite queste opzioni dell'interfaccia utente sono attualmente limitate all'esperienza di Application Insights classica.

Lo stesso vale per l'esperienza di creazione delle risorse Application Insights in Visual Studio per ASP.NET e ASP.NET Core. È necessario selezionare una risorsa basata sull'area di lavoro esistente da con l'interfaccia utente di abilitazione del monitoraggio di Visual Studio. Se si seleziona Crea nuova risorsa da Visual Studio, la creazione di una risorsa di Application Insights classica sarà limitata.

## <a name="creating-a-resource-automatically"></a>Creazione automatica di una risorsa

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per accedere all'anteprima Application Insights comandi dell'interfaccia della riga di comando di Azure, è prima di tutto necessario eseguire:

```azurecli
 az extension add -n application-insights
```

Se non si esegue il `az extension add` comando, verrà visualizzato un messaggio di errore che indica:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

A questo punto è possibile eseguire il comando seguente per creare la risorsa Application Insights:

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

Per la documentazione completa dell'interfaccia della riga di comando di Azure per questo comando, vedere la [documentazione di Azure CLI](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

Il `New-AzApplicationInsights` comando di PowerShell attualmente non supporta la creazione di una risorsa Application Insights basata sull'area di lavoro. Per creare una risorsa basata sull'area di lavoro con PowerShell, è possibile usare i modelli di Azure Resource Manager riportati di seguito e distribuire con PowerShell.

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

Una volta creata una risorsa Application Insights basata sull'area di lavoro, è possibile modificare l'area di lavoro Log Analytics associata.

Dall'interno del riquadro risorsa Application Insights selezionare **Proprietà**  >  **modifica area**di lavoro  >  **log Analytics aree di lavoro**

## <a name="next-steps"></a>Passaggi successivi

* [Esplora le metriche](../../azure-monitor/platform/metrics-charts.md)
* [Scrivere query di Analisi](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
