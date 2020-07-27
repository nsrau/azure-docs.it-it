---
title: Con questo modello di Azure Resource Manager, sarà possibile distribuire un endpoint privato per l'app Web.
description: Informazioni su come usare un modello di Resource Manager per distribuire un endpoint privato per l'app Web
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524825"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>Creare un'app del servizio app e distribuire un endpoint privato usando un modello di Azure Resource Manager

In questa guida di avvio rapido si usa un modello di Azure Resource Manager per creare un'app Web ed esporla con un endpoint privato.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Prerequisito

È necessario un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

Questo modello crea un endpoint privato per un'app Web di Azure.

### <a name="review-the-template"></a>Rivedere il modello

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Distribuire il modello

Di seguito è riportata la procedura per distribuire il modello di Azure Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea la rete virtuale, l'app Web, l'endpoint privato e la zona DNS privato.

   [Distribuzione in Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Selezionare o creare un gruppo di risorse.
3. Digitare il nome dell'app Web, del piano di servizio app e dell'endpoint privato.
5. Esaminare le condizioni per l'utilizzo del servizio. Per accettarle, selezionare Accetto le condizioni riportate sopra > Acquista. La distribuzione può richiedere diversi minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con l'endpoint privato non sono più necessarie, eliminare il gruppo di risorse. Oltre all'endpoint privato verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

- Altri modelli di Azure Resource Manager per le app Web del servizio app di Azure sono disponibili in [Esempi di modelli di Azure Resource Manager](../samples-resource-manager-templates.md).
