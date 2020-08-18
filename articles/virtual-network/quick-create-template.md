---
title: 'Avvio rapido: Creare una rete virtuale usando un modello di Resource Manager'
titleSuffix: Azure Virtual Network
description: Informazioni su come usare un modello di Resource Manager per creare una rete virtuale di Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c9e9ea2e59537a1c0c8e372a766fba3aa9a1b9a0
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122155"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Avvio rapido: Creare una rete virtuale - Modello di Resource Manager

Questo argomento di avvio rapido illustra come creare una rete virtuale con due subnet usando il modello di Resource Manager. Una rete virtuale è il blocco costitutivo fondamentale per una rete privata in Azure. Consente la comunicazione sicura tra le risorse di Azure, come le VM, e con Internet.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

È anche possibile completare questa guida di avvio rapido usando il [portale di Azure](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) o l'[interfaccia della riga di comando di Azure](quick-create-cli.md).

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo argomento di avvio rapido proviene dai [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

Nel modello sono state definite le risorse di Azure seguenti:
- [**Microsoft.Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks): per creare una rete virtuale di Azure.
-  [**Microsoft.Network/virtualNetworks/subnets**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets): per creare una subnet.

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea una rete virtuale con due subnet.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. Nella pagina **Create a Virtual Network with two Subnets** (Crea una rete virtuale con due subnet) del portale di Azure digitare o selezionare i valori seguenti:
   - **Gruppo di risorse**: selezionare **Crea nuovo**, digitare un nome per il gruppo di risorse e selezionare **OK**.
   - **Nome della rete virtuale**: digitare un nome per la nuova rete virtuale.
3. Selezionare **Rivedi e crea** e quindi **Crea**.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Esplorare le risorse create con la rete virtuale.

Per altre informazioni sulla sintassi JSON e sulle proprietà di una rete virtuale in un modello, vedere [Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con la rete virtuale non sono più necessarie, eliminare il gruppo di risorse. Oltre alla rete virtuale verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido è stata distribuita una rete virtuale di Azure con due subnet. Per altre informazioni sulle reti virtuali di Azure, passare all'esercitazione per le reti virtuali.

> [!div class="nextstepaction"]
> [Filtrare il traffico di rete](tutorial-filter-network-traffic.md)
