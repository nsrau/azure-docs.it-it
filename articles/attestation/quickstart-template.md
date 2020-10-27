---
title: Creare un certificato di Attestazione di Azure con un modello di Azure Resource Manager
description: Informazioni su come creare un certificato di Attestazione di Azure con un modello di Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 8c56a37ebcc799b0170785666212eb4e3b00a5f0
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144980"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Avvio rapido: Creare un provider di Attestazione di Azure con un modello di Resource Manager

[Attestazione di Microsoft Azure](overview.md) è una soluzione per l'attestazione di ambienti TEE (Trusted Execution Environment). Questo argomento di avvio rapido illustra il processo di distribuzione di un modello di Azure Resource Manager per creare un criterio di Attestazione di Microsoft Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure** . Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Risorse di Azure definite nel modello:

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Selezionare o immettere i valori seguenti.

    Se non diversamente specificato, usare il valore predefinito per creare il provider di attestazioni.

    - **Nome del provider di attestazioni** : selezionare un nome per il provider di Attestazione di Azure.
    - **Località** : Selezionare una località. Ad esempio **Stati Uniti centrali** .
    - **Tag** : Selezionare una località. Ad esempio **Stati Uniti centrali** .

1. Selezionare **Acquisto** . Al termine della distribuzione della risorsa di attestazione, si riceve una notifica.

Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

È possibile usare il portale di Azure per verificare la risorsa di attestazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Un'altra istanza di Attestazione di Azure basata su questo argomento di avvio rapido. Se si prevede di usare le guide introduttive e le esercitazioni successive, è consigliabile non cancellare le risorse create.

Quando non è più necessario, eliminare il gruppo di risorse per eliminare la risorsa di attestazione. Per eliminare il gruppo di risorse con l'interfaccia della riga di comando di Azure oppure con Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata una risorsa di attestazione usando un modello di Resource Manager, quindi è stata convalidata la distribuzione. Per altre informazioni su Attestazione di Azure, vedere [Panoramica di Attestazione di Azure](overview.md).
