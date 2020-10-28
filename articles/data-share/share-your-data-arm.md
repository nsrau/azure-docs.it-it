---
title: Condividere all'esterno dell'organizzazione (modello di Resource Manager) - Avvio rapido su Condivisione dati di Azure
description: In questa guida di avvio rapido viene illustrato come condividere dati con clienti e partner usando Condivisione dati di Azure e un modello di Resource Manager.
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: f72fbad579bcb08a36c2dd29c387e18953f26c09
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146148"
---
# <a name="quickstart-share-data-using-azure-data-share-and-resource-manager-templates"></a>Avvio rapido: Condividere i dati usando Condivisione dati di Azure e modelli di Resource Manager

Informazioni su come configurare una nuova condivisione dati di Azure da un account di archiviazione usando un modello di Resource Manager e come iniziare a condividere i dati con clienti e partner esterni all'organizzazione di Azure. Per un elenco degli archivi dati supportati, vedere [Archivi dati supportati in Condivisione dati di Azure](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure** . Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/).

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

Nel modello sono definite le risorse seguenti:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft.Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/accounts](/rest/api/datashare/accounts/create)
* [Microsoft.DataShare/accounts/shares](/rest/api/datashare/shares/create)
* [Microsoft.DataShare/accounts/shares/dataSets](/rest/api/datashare/datasets/create)
* [Microsoft.DataShare/accounts/shares/invitations](/rest/api/datashare/invitations/create)
* [Microsoft.DataShare/accounts/shares/synchronizationSettings](/rest/api/datashare/synchronizationsettings/create)

Il modello esegue le attività seguenti:

* Crea un account di archiviazione e un contenitore da usare come origine dati di Condivisione dati.
* Crea un account di Condivisione dati e una condivisione dati.
* Crea un'assegnazione di ruolo per concedere il ruolo con autorizzazioni di lettura per i dati dei BLOB alla risorsa condivisione dati di origine. Vedere [Ruoli e requisiti di Condivisione dati di Azure](./concepts-roles-permissions.md).
* Aggiunge un set di dati alla condivisione dati.
* Aggiunge i destinatari alla condivisione dati.
* Abilita una pianificazione degli snapshot per la condivisione dati.

Questo modello viene creato a scopo di apprendimento. Nella pratica, in genere si hanno alcuni dati in un account di archiviazione esistente. Prima di eseguire un modello o uno script per creare il set di dati, è necessario creare l'assegnazione di ruolo. In alcuni casi, quando si distribuisce il modello, è possibile che si riceva il messaggio di errore seguente:

```error message
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Il motivo è che la distribuzione prova a creare il set di dati prima che l'assegnazione del ruolo di Azure venga finalizzata. Nonostante il messaggio di errore, la distribuzione potrebbe riuscire.  Sarà comunque possibile completare l'attività descritta in [Esaminare le risorse distribuite](#review-deployed-resources).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Selezionare o immettere i valori seguenti:

    * **Sottoscrizione** : selezionare una sottoscrizione di Azure usata per creare la condivisione dati e le altre risorse.
    * **Gruppo di risorse** : selezionare **Crea nuovo** per creare un nuovo gruppo di risorse oppure selezionarne uno esistente.
    * **Località** : selezionare una località per il gruppo di risorse.
    * **Nome progetto** : immettere un nome per il progetto.  Il nome del progetto viene usato per generare i nomi delle risorse.  Vedere le definizioni delle variabili nel modello precedente.
    * **Località** : selezionare una località per le risorse.  È possibile usare la stessa località per il gruppo di risorse.
    * **Messaggio di posta elettronica di invito** : immettere l'indirizzo di posta elettronica del destinatario della condivisione dati usato per l'accesso ad Azure.  L'alias di posta elettronica non funziona.

    Per le impostazioni rimanenti, usare i valori predefiniti.
1. Selezionare **Accetto le condizioni riportate sopra** e quindi **Acquista** .

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Aprire l'account di condivisione dati creato.
1. Nel menu a sinistra selezionare **Send Shares** (Invia condivisioni).  Verrà visualizzato l'account di archiviazione.
1. Selezionare l'account di archiviazione.  In **Dettagli** verrà visualizzata l'impostazione di sincronizzazione configurata nel modello.

    ![Impostazioni di sincronizzazione dell'account di archiviazione di Condivisione dati di Azure](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Selezionare **Inviti** nella parte superiore. Verrà visualizzato l'indirizzo di posta elettronica specificato durante la distribuzione del modello. Lo **stato** sarà **In sospeso** .

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse per eliminare tutte le risorse contenute al suo interno.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare una condivisione dati di Azure e invitare destinatari. Per altre informazioni su come un consumer di dati può accettare e ricevere una condivisione dati, continuare con l'esercitazione [Accettare e ricevere dati](subscribe-to-data-share.md).
