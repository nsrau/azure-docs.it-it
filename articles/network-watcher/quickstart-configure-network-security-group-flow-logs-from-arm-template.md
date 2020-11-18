---
title: 'Avvio rapido: Configurare i log dei flussi dei gruppi di sicurezza di rete con un modello di Azure Resource Manager'
description: Informazioni su come abilitare i log dei flussi dei gruppi di sicurezza di rete a livello di codice con un modello di Azure Resource Manager e Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 563f111a656376899fcd0201b42f87bfea445865
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488049"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Avvio rapido: Configurare i log dei flussi dei gruppi di sicurezza di rete con un modello di Resource Manager

Questa guida di avvio rapido illustra come abilitare i [log dei flussi dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md) con un modello di [Azure Resource Manager](../azure-resource-manager/management/overview.md) e Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si inizia con una panoramica delle proprietà dell'oggetto log dei flussi dei gruppi di sicurezza di rete. Vengono forniti modelli di esempio. Viene quindi usata un'istanza locale di Azure PowerShell per distribuire il modello.

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello viene visualizzato nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questa guida di avvio rapido è incluso nei [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Nel modello sono definite queste risorse:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Oggetto log del flusso del gruppo di sicurezza di rete

Il codice seguente mostra un oggetto log dei flussi dei gruppi di sicurezza di rete e i relativi parametri. Per creare una risorsa `Microsoft.Network/networkWatchers/flowLogs`, aggiungere questo codice alla sezione resources del modello:

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

Per una panoramica completa delle proprietà dell'oggetto flusso dei log dei gruppi di sicurezza di rete, vedere [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Creare il modello

Se si usano i modelli di Resource Manager per la prima volta, vedere gli articoli seguenti per altre informazioni:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Esercitazione: Creare e distribuire il primo modello di Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

L'esempio seguente è un modello completo. È anche la versione più semplice del modello. L'esempio contiene i parametri minimi passati per configurare i log dei flussi dei gruppi di sicurezza di rete. Per altri esempi, vedere l'articolo introduttivo [Configurare i log dei flussi dei gruppi di sicurezza di rete da un modello di Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md).

### <a name="example"></a>Esempio

Il modello seguente abilita i log dei flussi per un gruppo di sicurezza di rete e quindi li archivia in uno specifico account di archiviazione:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - Il nome della risorsa usa il formato _ParentResource_ChildResource_. In questo esempio la risorsa padre è l'istanza locale di Azure Network Watcher:
>    - **Format**: NetworkWatcher_RegionName
>    - **Esempio**: NetworkWatcher_centraluseuap
> - `targetResourceId` è l'ID risorsa per il gruppo di sicurezza di rete di destinazione.
> - `storageId` è l'ID risorsa per l'account di archiviazione di destinazione.

## <a name="deploy-the-template"></a>Distribuire il modello

Questa esercitazione presuppone che siano presenti un gruppo di risorse e un gruppo di sicurezza di rete in cui abilitare la funzionalità dei log dei flussi.

È possibile salvare i modelli di esempio illustrati in questo articolo in locale come *azuredeploy.json*. Aggiornare i valori delle proprietà in modo che puntino a risorse valide nella sottoscrizione.

Per distribuire il modello, eseguire il comando seguente in Azure PowerShell:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Questi comandi distribuiscono una risorsa nel gruppo di risorse NetworkWatcherRG di esempio e non nel gruppo di risorse contenente il gruppo di sicurezza di rete.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Sono disponibili due opzioni per verificare se la distribuzione è riuscita:

- La console di PowerShell mostra `ProvisioningState` con lo stato `Succeeded`.
- Visitare la [pagina del portale relativa ai log dei flussi dei gruppi di sicurezza di rete](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) per confermare le modifiche. 

Se si sono verificati problemi durante la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare le risorse di Azure usando la modalità di distribuzione completa. Per eliminare una risorsa log dei flussi, specificare una distribuzione in modalità completa senza includere la risorsa da eliminare. Per altre informazioni, vedere [Modalità di distribuzione completa](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

È anche possibile disabilitare un log dei flussi dei gruppi di sicurezza di rete nel portale di Azure:

1. Accedere al portale di Azure.
1. Selezionare **Tutti i servizi**. Nella casella **Filtro** immettere **Network Watcher**. Nei risultati della ricerca selezionare **Network Watcher**.
1. In **Log** selezionare **Log del flusso del NSG**.
1. Nell'elenco di gruppi di sicurezza di rete selezionare quello per cui disabilitare i log dei flussi.
1. In **Impostazioni dei log dei flussi** selezionare **Disattivato**.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come abilitare i log dei flussi dei gruppi di sicurezza di rete con un modello di Resource Manager. Successivamente, vedere come visualizzare i dati dei flussi dei gruppi di sicurezza di rete con una di queste opzioni:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Strumenti open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Analisi del traffico di Azure](traffic-analytics.md)
