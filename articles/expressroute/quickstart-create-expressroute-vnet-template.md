---
title: 'Avvio rapido: Creare un circuito ExpressRoute usando un modello di Azure Resource Manager'
description: Questa guida di avvio rapido descrive come creare un circuito ExpressRoute usando un modello di Azure Resource Manager.
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 37f0b890cd4942e5dcb47b496d661eb7c54db94d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093515"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Avvio rapido: Creare un circuito ExpressRoute con peering privato usando un modello di Resource Manager

Questo argomento di avvio rapido descrive come usare un modello di Azure Resource Manager per creare un circuito ExpressRoute con peering privato.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

In questo argomento di avvio rapido si creerà un circuito ExpressRoute con *Equinix* come provider di servizi. Il circuito userà uno *SKU Premium*, con una larghezza di banda di *50 Mbps* e *Washington DC* come località di peering. Il peering privato verrà abilitato con una subnet primaria e una secondaria, rispettivamente *192.168.10.16/30* e *192.168.10.20/30*. Verrà creata anche una rete virtuale insieme a un *gateway ExpressRoute HighPerformance*.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json" range="001-351" highlight="183-219":::

Nel modello sono state definite più risorse di Azure.

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (per abilitare il peering privato nel circuito)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (il gruppo di sicurezza di rete viene applicato alle subnet nella rete virtuale)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks) 
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (l'indirizzo IP pubblico viene usato dal gateway ExpressRoute)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (il gateway ExpressRoute viene usato per collegare la rete virtuale al circuito)

Per altri modelli correlati a ExpressRoute, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare **Prova** per il blocco di codice seguente per aprire Azure Cloud Shell e seguire le istruzioni per la connessione ad Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Attendere finché non viene visualizzato il prompt nella console.

1. Selezionare **Copia** nel blocco di codice precedente per copiare lo script di PowerShell.

1. Fare clic con il pulsante destro del mouse sul riquadro della console della shell e quindi scegliere **Incolla**.

1. Immettere i valori desiderati.

    Il nome del gruppo di risorse è il nome del progetto seguito da **rg**.

    La distribuzione del modello richiede circa 20 minuti. Al termine, l'output sarà simile al seguente:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="Modello di Resource Manager per ExpressRoute: output della distribuzione con PowerShell":::

Per distribuire il modello viene usato Azure PowerShell. Oltre ad Azure PowerShell, è anche possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Gruppi di risorse** nel riquadro sinistro.

1. Selezionare il gruppo di risorse creato nella sezione precedente. Il nome del gruppo di risorse predefinito è il nome del progetto seguito da **rg**.

1. Il gruppo di risorse dovrà contenere le risorse illustrate qui:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="Modello di Resource Manager per ExpressRoute: output della distribuzione con PowerShell":::

1. Selezionare il circuito ExpressRoute **er-ck01** per verificare che lo stato del circuito sia **abilitato**, lo stato del provider sia **senza provisioning** e lo stato del peering privato sia **con provisioning**.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="Modello di Resource Manager per ExpressRoute: output della distribuzione con PowerShell":::

> [!NOTE]
> Per completare il processo di provisioning prima di collegare la rete virtuale al circuito, è necessario chiamare il provider.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il circuito ExpressRoute non sono più necessarie, eliminare il gruppo di risorse. Oltre al circuito ExpressRoute verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:
* Circuito ExpressRoute
* Rete virtuale
* Gateway VPN
* IP pubblico
* gruppi di sicurezza di rete

Per informazioni su come collegare una rete virtuale a un circuito, continuare con le esercitazioni su ExpressRoute.

> [!div class="nextstepaction"]
> [Esercitazioni su ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
