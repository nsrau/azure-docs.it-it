---
title: Esempi di modelli di Resource Manager per Monitoraggio di Azure
description: Distribuire e configurare le funzionalità di Monitoraggio di Azure con i modelli di Resource Manager
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 2c9287e6f4bda429309dd0041215b271678d03d3
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860699"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Esempi di modelli di Resource Manager per Monitoraggio di Azure

Monitoraggio di Azure può essere distribuito e configurato su larga scala usando un [modello di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Gli articoli seguenti forniscono modelli di esempio per diverse funzionalità di Monitoraggio di Azure. Questi esempi possono essere modificati in base a requisiti specifici e distribuiti con qualsiasi metodo standard per la distribuzione di modelli di Resource Manager. 

## <a name="deploying-the-sample-templates"></a>Distribuzione dei modelli di esempio
I passaggi di base per usare gli esempi sono:

1. Copiare il modello e salvarlo come file JSON.
2. Modificare i parametri per l'ambiente e salvarli come file JSON.
4. Distribuire il modello usando [qualsiasi metodo di distribuzione per i modelli di Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). 

Ad esempio, usare i comandi seguenti per distribuire il modello e il file dei parametri nella sottoscrizione tramite PowerShell o l'interfaccia della riga di comando di Azure.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Elenco di modelli di esempio

- [Agenti](resource-manager-agent.md): distribuire e configurare l'agente di Log Analytics e l'estensione di diagnostica.
- Avvisi
  - [Regole di avviso per i log](resource-manager-alerts-log.md): avvisi delle query su log e log attività di Azure.
  - [Regole di avviso delle metriche](resource-manager-alerts-metric.md): avvisi delle metriche che usano tipi diversi di logica.
- Application Insights: disponibile prossimamente.
- [Impostazioni di diagnostica](resource-manager-diagnostic-settings.md): creare le impostazioni di diagnostica per inoltrare log e metriche di diversi tipi di risorse.
- [Query su log](resource-manager-log-queries.md): creare query su log salvate in un'area di lavoro Log Analytics.
- [Area di lavoro Log Analytics](resource-manager-workspace.md): creare un'area di lavoro Log Analytics e configurare la raccolta di origini dati diverse dall'agente di Log Analytics.
- [Cartelle di lavoro](resource-manager-workbooks.md): creare cartelle di lavoro.
- [Monitoraggio di Azure per i contenitori](resource-manager-container-insights.md): eseguire l'onboarding di cluster in Monitoraggio di Azure per i contenitori.
- [Monitoraggio di Azure per le macchine virtuali](resource-manager-vminsights.md): eseguire l'onboarding di VM in Monitoraggio di Azure per le macchine virtuali.



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [modelli di Resource Manager](../../azure-resource-manager/templates/overview.md)
