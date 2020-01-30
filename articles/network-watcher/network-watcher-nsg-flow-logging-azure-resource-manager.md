---
title: Network Watcher creare log di flusso NSG usando un modello di Azure Resource Manager
description: Usare un modello di Azure Resource Manager e PowerShell per configurare facilmente i log di flusso NSG.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 2ae6314accf5ad9143e1e103239c2e7c884ec947
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776638"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Configurare i log di flusso NSG da un modello di Azure Resource Manager

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfaccia della riga di comando di Azure](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) è il modo nativo e potente di Azure per gestire l' [infrastruttura come codice](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code).

Questo articolo illustra come abilitare i [log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) a livello di codice usando un modello di Azure Resource Manager e Azure PowerShell. Per iniziare, è necessario fornire una panoramica delle proprietà dell'oggetto log del flusso NSG, seguito da alcuni modelli di esempio. Quindi il modello di distribuzione viene usato con un'istanza di PowerShell locale.


## <a name="nsg-flow-logs-object"></a>Oggetto log del flusso NSG

L'oggetto log del flusso NSG con tutti i parametri with è illustrato di seguito.
Per una panoramica completa delle proprietà, è possibile leggere il [riferimento al modello dei log di flusso NSG](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Per creare una risorsa Microsoft. Network/networkWatchers/dashboard, aggiungere il codice JSON precedente alla sezione Resources del modello.


## <a name="creating-your-template"></a>Creazione del modello

Se si usano i modelli di Azure Resource Manager per la prima volta, è possibile ottenere altre informazioni usando i collegamenti seguenti.

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Esercitazione: creare e distribuire il primo modello di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Di seguito sono riportati due esempi di modelli completi per configurare i log di flusso NSG.

**Esempio 1**: la versione più semplice del precedente con i parametri minimi passati. Il modello seguente Abilita i log dei flussi di NSG in un NSG di destinazione e li archivia in un determinato account di archiviazione.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Il nome della risorsa ha il formato "risorsa padre > risorsa/Child". In questo caso, la risorsa padre è l'istanza di Network Watcher regionale (Format: NetworkWatcher_<RegionName>. Esempio: NetworkWatcher_centraluseuap)
> * targetResourceId è l'ID risorsa del NSG di destinazione
> * storageId è l'ID risorsa dell'account di archiviazione di destinazione

**Esempio 2**: i modelli seguenti abilitano i log di flusso NSG (versione 2) con un periodo di conservazione per 5 giorni. Abilitazione di Analisi del traffico con un intervallo di elaborazione di 10 minuti.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
            "enabled": true,
        "workspaceResourceId": "91a3d1e9-698e-4a49-96dc-f6fc585ae888",
        "trafficAnalyticsInterval": 10
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 1
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Distribuzione del modello di Azure Resource Manager

Questa esercitazione presuppone che si disponga di un gruppo di risorse esistente e di un NSG è possibile abilitare la registrazione del flusso.
È possibile salvare i modelli di esempio precedenti in locale come `azuredeploy.json`. Aggiornare i valori delle proprietà in modo che puntino a risorse valide nella sottoscrizione.

Per distribuire il modello, eseguire il comando seguente in PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Verifica della distribuzione

Esistono due modi per verificare se la distribuzione ha avuto esito positivo. Nella console di PowerShell verrà visualizzato "ProvisioningState" come "succeeded". È anche possibile visitare la [pagina del portale dei log di flusso di NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) per confermare le modifiche. Se si sono verificati problemi con la distribuzione, vedere [risolvere gli errori comuni di distribuzione di Azure con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come visualizzare i dati del flusso NSG usando:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Strumenti open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Analisi del traffico di Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
