---
title: Usare un modello di Azure Resource Manager per distribuire un endpoint privato per un'app Web
description: Informazioni su come usare il modello di Resource Manager per distribuire un endpoint privato per l'app Web.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652016"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Creare un'app del servizio app e distribuire un endpoint privato usando un modello di Azure Resource Manager

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

1. Per accedere ad Azure e aprire il modello, selezionare questo collegamento:  [Distribuzione in Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). Il modello crea la rete virtuale, l'app Web, l'endpoint privato e la zona DNS privato.
2. Selezionare o creare un gruppo di risorse.
3. Immettere il nome dell'app Web, del piano di servizio app e dell'endpoint privato.
5. Leggere le condizioni per l'utilizzo del servizio. Per accettarle, selezionare **Accetto le condizioni riportate sopra** > **Acquista**. Il completamento della distribuzione può richiedere alcuni minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con l'endpoint privato non sono più necessarie, eliminare il gruppo di risorse. Oltre all'endpoint privato verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

- Altri modelli di Azure Resource Manager per le app Web del servizio app di Azure sono disponibili in [Esempi di modelli di Azure Resource Manager](../samples-resource-manager-templates.md).
