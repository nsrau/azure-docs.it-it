---
title: 'Avvio rapido: Creare un profilo di Gestione traffico usando un modello di Azure Resource Manager'
description: Questo argomento di avvio rapido descrive come creare un profilo di Gestione traffico di Azure usando un modello di Azure Resource Manager.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: dbdb6a255fdf0214103a0011f25b0a6d25014e69
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89299151"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Avvio rapido: Creare un profilo di Gestione traffico usando un modello di Resource Manager

Questo argomento di avvio rapido descrive come usare un modello di Azure Resource Manager per creare un profilo di Gestione traffico con endpoint esterni usando il metodo di routing delle prestazioni.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

Nel modello è definita una risorsa di Azure:

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Per trovare altri modelli correlati a Gestione traffico di Azure, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare **Prova** per il blocco di codice seguente per aprire Azure Cloud Shell e seguire le istruzioni per la connessione ad Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Attendere finché non viene visualizzato il prompt nella console.

1. Selezionare **Copia** nel blocco di codice precedente per copiare lo script di PowerShell.

1. Fare clic con il pulsante destro del mouse sul riquadro della console della shell e quindi scegliere **Incolla**.

1. Immettere i valori desiderati.

    La distribuzione del modello crea un profilo con due endpoint esterni. **Endpoint1** usa un endpoint di destinazione *w<span>ww.microsoft</span>.com* con località **Europa settentrionale**. **Endpoint2** usa un endpoint di destinazione *d<span>ocs.microsoft</span>.com* con località **Stati Uniti centro-meridionali**. 

    Il nome del gruppo di risorse è il nome del progetto seguito da **rg**.

    > [!NOTE]
    > Per consentire la corretta distribuzione del modello, **uniqueDNSname** deve essere un nome univoco globale. Se la distribuzione non riesce, ricominciare dal passaggio 1.

    La distribuzione del modello può richiedere alcuni minuti. Al termine, l'output sarà simile al seguente:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Modello di Resource Manager per Gestione traffico di Azure: output della distribuzione con PowerShell":::

Per distribuire il modello viene usato Azure PowerShell. Oltre ad Azure PowerShell, è anche possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

1. Determinare il nome DNS del profilo di Gestione traffico usando [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Copiare il valore **RelativeDnsName**. Il nome DNS del profilo di Gestione traffico è *<* relativednsname *>.trafficmanager.net*. 

1. Da un'istanza locale di PowerShell eseguire il comando seguente sostituendo la variabile **{relativeDNSname}** con *<* relativednsname *>.trafficmanager.net*.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```
    Si dovrebbe ottenere il NameHost *w<span>ww.microsoft</span>.com* o *d<span>ocs.microsoft</span>.com*, in base all'area più vicina.

1. Per verificare se è possibile risolvere l'altro endpoint, disabilitare l'endpoint per la destinazione ottenuta nel passaggio precedente. Sostituire **{endpointName}** con **endpoint1** o **endpoint2** per disabilitare la destinazione per *w<span>ww.microsoft</span>.com* o *d<span>ocs.microsoft</span>.com* rispettivamente.

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```
1. Eseguire di nuovo il comando dal passaggio 2 in un'istanza locale di PowerShell. Questa volta si dovrebbe ottenere l'altro NameHost per l'altro endpoint. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il profilo di Gestione traffico non serve più, eliminare il gruppo di risorse. Oltre al profilo di Gestione traffico verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:
* Profilo di Gestione traffico

Per altre informazioni su come instradare il traffico, passare alle esercitazioni di Gestione traffico.

> [!div class="nextstepaction"]
> [Esercitazioni di Gestione traffico](tutorial-traffic-manager-improve-website-response.md)
