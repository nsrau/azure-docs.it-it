---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico interno usando un modello'
description: Questa guida di avvio rapido descrive come creare un servizio di bilanciamento del carico interno usando un modello di Azure Resource Manager (ARM).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 749b233b827c27d2c998cfd6be66cf79cf48089d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831651"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Avvio rapido: Creare un servizio di bilanciamento del carico interno per le macchine virtuali tramite un modello di Azure Resource Manager (ARM)

Questo argomento di avvio rapido descrive come usare un modello di Azure Resource Manager (ARM) per creare un servizio di bilanciamento del carico interno.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-2-vms-internal-load-balancer%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo argomento di avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer).

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

Nel modello sono state definite più risorse di Azure.

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): Account di archiviazione della macchina virtuale per la diagnostica di avvio.
- [**Microsoft.Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets): Set di disponibilità per le macchine virtuali.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): Rete virtuale per il servizio di bilanciamento del carico e le macchine virtuali.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): Interfacce di rete per le macchine virtuali.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): Servizio di bilanciamento del carico interno.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): macchine virtuali.

Per altri modelli correlati ad Azure Load Balancer, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

**Interfaccia della riga di comando di Azure**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Gruppi di risorse** nel riquadro sinistro.

1. Selezionare il gruppo di risorse creato nella sezione precedente. Il nome del gruppo di risorse predefinito è **myResourceGroupLB**

1. Verificare che le risorse seguenti siano state create nel gruppo di risorse:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Usare il portale di Azure per verificare la creazione delle risorse." border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse al suo interno non sono più necessari, usare il comando [az group delete](/cli/azure/group#az-group-delete) per rimuoverli.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere:

> [!div class="nextstepaction"]
> [Esercitazione: Creare e distribuire il primo modello di Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
