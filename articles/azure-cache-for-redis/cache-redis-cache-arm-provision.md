---
title: Distribuire cache di Azure per Redis usando Azure Resource Manager modello
description: Informazioni su come usare un modello di Azure Resource Manager (modello ARM) per distribuire una cache di Azure per la risorsa Redis. Sono disponibili modelli per gli scenari comuni.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735975"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Guida introduttiva: creare una cache di Azure per Redis usando un modello ARM

Informazioni su come creare un modello di Azure Resource Manager (ARM template) che distribuisce una cache di Azure per Redis. La cache è utilizzabile con un account di archiviazione esistente per mantenere i dati di diagnostica. Verrà anche illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze. Le impostazioni di diagnostica sono attualmente condivise da tutte le cache nella stessa area di una sottoscrizione. L'aggiornamento di una cache nell'area ha effetto su tutte le altre cache presenti nell'area.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure** . Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure** : Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Un account di archiviazione** : per crearne uno, vedere [creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal). L'account di archiviazione viene usato per i dati di diagnostica.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

Nel modello sono definite le risorse seguenti:

* [Microsoft. cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft. Insights/DiagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

Sono disponibili anche modelli di Gestione risorse per il nuovo [livello Premium](cache-overview.md#service-tiers) .

* [Creare un'istanza di Cache Redis di Azure Premium con clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Creare una Cache Redis Premium di Azure con persistenza dei dati](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Creare una cache Redis Premium distribuita in una rete virtuale](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Per cercare i modelli più recenti, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) e cercare _cache di Azure per Redis_ .

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Selezionare o immettere i valori seguenti:

    * **Sottoscrizione** : selezionare una sottoscrizione di Azure usata per creare la condivisione dati e le altre risorse.
    * **Gruppo di risorse** : selezionare **Crea nuovo** per creare un nuovo gruppo di risorse oppure selezionarne uno esistente.
    * **Località** : selezionare una località per il gruppo di risorse. L'account di archiviazione e la cache Redis devono trovarsi nella stessa area. Per impostazione predefinita, la cache Redis usa la stessa località del gruppo di risorse. Quindi, specificare lo stesso percorso dell'account di archiviazione.
    * **Nome cache Redis** : immettere un nome per la cache Redis.
    * **Account di archiviazione di diagnostica esistente** : immettere l'ID risorsa di un account di archiviazione. La sintassi è `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Per le impostazioni rimanenti, usare i valori predefiniti.
1. Selezionare **Accetto le condizioni riportate sopra** e quindi **Acquista** .

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Aprire la cache Redis creata.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse per eliminare tutte le risorse contenute al suo interno.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come creare un modello di Azure Resource Manager che distribuisce una cache di Azure per Redis. Per informazioni su come creare un modello di Azure Resource Manager per la distribuzione di un'app Web di Azure con cache di Azure per Redis, vedere [creare un'app Web e cache di Azure per Redis usando un modello](./cache-web-app-arm-with-redis-cache-provision.md).
