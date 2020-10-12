---
title: 'Modello di Azure ExpressRoute: creare un circuito ExpressRoute'
description: Informazioni su come creare un circuito ExpressRoute di Azure distribuendo un modello di Azure Resource Manager usando Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 3428f092c4b2356e9ea6afa54d1a03fc8b4f9f05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566178"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Creare un circuito ExpressRoute usando Azure Resource Manager modello

> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-circuit-cli.md)
> * [Modello di Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-circuit-classic.md) (PowerShell (classico))
>

Informazioni su come creare un circuito ExpressRoute distribuendo un modello di Azure Resource Manager usando Azure PowerShell. Per altre informazioni sullo sviluppo di modelli di Resource Manager, vedere la [documentazione di Resource Manager](/azure/azure-resource-manager/) e le [informazioni di riferimento sui modelli](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Prima di iniziare

* Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).
* Verificare di avere le autorizzazioni necessarie per creare nuove risorse di rete. Se non si hanno le autorizzazioni appropriate, contattare l'amministratore dell'account.
* È possibile [visualizzare un video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) prima di iniziare, per ottenere una comprensione migliore della procedura.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Creare un circuito ExpressRoute ed eseguirne il provisioning

I [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) hanno una raccolta di gestione risorse modello. Usare uno dei [modelli esistenti](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) per creare un circuito ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Per visualizzare altri modelli correlati, fare clic [qui](https://azure.microsoft.com/resources/templates/?term=expressroute).

Per creare un circuito ExpressRoute distribuendo un modello:

1. Selezionare **prova** dal blocco di codice seguente e quindi seguire le istruzioni per accedere ad Azure cloud Shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * Il **livello SKU** determina se un circuito ExpressRoute è [locale](expressroute-faqs.md#expressroute-local), standard o [Premium](expressroute-faqs.md#expressroute-premium). È possibile specificare *local*, * standard o *Premium*. Non è possibile cambiare lo SKU da *Standard/Premium* a *Locale*.
   * La **famiglia di SKU** determina il tipo di fatturazione. È possibile specificare *Metereddata* per un piano dati a consumo e *Unlimiteddata* per un piano dati senza limiti. È possibile modificare il tipo di fatturazione da *Metereddata* a *Unlimiteddata*, ma *non* è possibile eseguire il passaggio *inverso*. Un circuito *locale* è solo *Unlimiteddata* .
   * **Località peer** è la posizione fisica di peering con Microsoft.

     > [!IMPORTANT]
     > La località peer indica la [posizione fisica](expressroute-locations.md) di peering con Microsoft. Questo percorso **non** è collegato alla proprietà "Location", ovvero all'area geografica in cui si trova il provider di risorse di rete di Azure. Dal momento che non sono collegati, è consigliabile scegliere un provider di risorse di rete geograficamente vicino alla posizione di peering del circuito.

    Il nome del gruppo di risorse è il nome dello spazio dei nomi del bus di servizio con l'aggiunta di **RG** .

2. Selezionare **Copia** per copiare lo script di PowerShell.
3. Fare clic con il pulsante destro del mouse sulla console della shell e quindi scegliere **Incolla**.

La creazione di un hub eventi richiede pochi minuti.

Azure PowerShell viene usato per distribuire il modello in questa esercitazione. Per altri metodi di distribuzione dei modelli, vedere:

* [Utilizzando il portale di Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Usando l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md).
* [Usando l'API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning ed eliminazione di un circuito ExpressRoute

È possibile eliminare il circuito ExpressRoute selezionando l'icona **Elimina** . Tenere presente quanto segue:

* È necessario scollegare tutte le reti virtuali dal circuito ExpressRoute. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.
* Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continuano a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.
* Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. Viene così interrotta la fatturazione per il circuito.

È possibile eliminare il circuito ExpressRoute eseguendo il comando PowerShell seguente:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito, continuare con i passaggi seguenti:

* [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
