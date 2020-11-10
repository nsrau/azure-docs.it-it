---
title: 'Avvio rapido: Configurare i log del flusso del gruppo di sicurezza di rete usando un modello di Azure Resource Manager'
description: Informazioni su come abilitare i log del flusso del gruppo di sicurezza di rete a livello di codice usando un modello di Azure Resource Manager e Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042747"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Avvio rapido: Configurare i log dei flussi dei gruppi di sicurezza di rete con un modello di Azure Resource Manager

In questo avvio rapido verranno abilitati i [log del flusso del gruppo di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md) a livello di codice usando un modello di [Azure Resource Manager](../azure-resource-manager/management/overview.md) e Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Per iniziare, è necessario fornire una panoramica delle proprietà dell'oggetto log del flusso del gruppo di sicurezza di rete, seguito da alcuni modelli di esempio. Il modello viene quindi distribuito con un'istanza di PowerShell locale.

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Nel modello sono definite diverse risorse:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Oggetto log del flusso del gruppo di sicurezza di rete

Di seguito è illustrato l'oggetto log del flusso del gruppo di sicurezza di rete con tutti i parametri. Per una panoramica completa delle proprietà, vedere [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Per creare una risorsa `Microsoft.Network/networkWatchers/flowLogs`, aggiungere il codice JSON precedente alla sezione resources del modello.

## <a name="creating-your-template"></a>Creazione del modello

Se si usano i modelli di Azure Resource Manager per la prima volta, è possibile ottenere altre informazioni usando i collegamenti seguenti.

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Esercitazione: Creare e distribuire il primo modello di Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

L'esempio seguente di un modello completo è la versione più semplice con i parametri minimi passati per configurare i log del flusso del gruppo di sicurezza di rete. Per altri esempi, passare a questa [guida pratica](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Esempio** : Il modello seguente abilita i log del flusso del gruppo di sicurezza di rete per un gruppo di sicurezza di rete di destinazione e li archivia in un determinato account di archiviazione.

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
> - Il nome della risorsa ha il formato _Risorsa padre_Risorsa figlio_. In questo caso, la risorsa padre è l'istanza di Network Watcher dell'area geografica, nel formato NetworkWatcher_NomeAreaGeografica. Esempio: NetworkWatcher_centraluseuap.
> - `targetResourceId` è l'ID risorsa per il gruppo di sicurezza di rete di destinazione.
> - `storageId` è l'ID risorsa per l'account di archiviazione di destinazione.

## <a name="deploy-the-template"></a>Distribuire il modello

Questa esercitazione presuppone che siano presenti un gruppo di risorse e un gruppo di sicurezza di rete in cui abilitare la funzionalità di registrazione del flusso.
È possibile salvare in locale i modelli di esempio precedenti come `azuredeploy.json`. Aggiornare i valori delle proprietà in modo che puntino a risorse valide nella sottoscrizione.

Per distribuire il modello, eseguire il comando seguente in PowerShell.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> I comandi precedenti distribuiscono una risorsa nel gruppo di risorse NetworkWatcherRG e non nel gruppo di risorse contenente il gruppo di sicurezza di rete.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Esistono due modi per verificare se la distribuzione ha avuto esito positivo. La console di PowerShell dovrebbe mostrare `ProvisioningState` con lo stato `Succeeded`. Inoltre, è possibile visitare la [pagina del portale relativa ai log del flusso del gruppo di sicurezza di rete](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) per confermare le modifiche apportate. Se si sono verificati problemi durante la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Azure consente l'eliminazione di risorse tramite la modalità di distribuzione `Complete`. Per eliminare una risorsa dei log del flusso, specificare una distribuzione in modalità `Complete` senza includere la risorsa da eliminare. Per altre informazioni, vedere [Modalità di distribuzione completa](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

In alternativa è possibile disabilitare un log del flusso del gruppo di sicurezza di rete dal portale di Azure, come illustrato nella procedura seguente:

1. Accedere al portale di Azure
1. Nell'angolo in alto a sinistra del portale selezionare **Tutti i servizi**. Nella **casella del filtro** digitare _Network Watcher_. Selezionare **Network Watcher** quando viene visualizzato tra i risultati della ricerca.
1. In **LOG** selezionare **Log del flusso del NSG**.
1. Nell'elenco di gruppi di sicurezza di rete selezionare quello per cui disabilitare i log dei flussi.
1. In **Impostazioni dei log dei flussi** impostare lo stato dei log dei flussi su **Off**.
1. Scorrere verso il basso e selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido sono stati abilitati log del flusso del gruppo di sicurezza di rete. Vedere ora come visualizzare i dati del lusso del gruppo di sicurezza di rete con:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Strumenti open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Analisi del traffico di Azure](traffic-analytics.md)
