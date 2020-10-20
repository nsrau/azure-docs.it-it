---
title: 'Avvio rapido: Creare un servizio Azure SignalR - Modello di Resource Manager'
description: Questa guida di avvio rapido illustra come creare un servizio Azure SignalR con un modello di Azure Resource Manager.
author: mgblythe
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 10/02/2020
ms.openlocfilehash: 04d0a98863dded93216f5fc669b8148f710f5f0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858833"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Avvio rapido: Usare un modello di Resource Manager per distribuire un servizio Azure SignalR

Questa guida di avvio rapido descrive come usare un modello di Azure Resource Manager per creare un servizio Azure SignalR. È possibile distribuire il servizio Azure SignalR tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure dopo l'accesso.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Pulsante per distribuire il servizio Azure SignalR in Azure con un modello di Resource Manager nel portale di Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)

Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).
* Per eseguire il codice in locale, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/).
* Se si vuole eseguire il codice in locale:
    * Una shell Bash (ad esempio Git Bash, inclusa in [Git per Windows](https://gitforwindows.org)).
    * [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Il modello definisce una risorsa di Azure:

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Distribuire il modello

# <a name="portal"></a>[Portale](#tab/azure-portal)

Selezionare il collegamento seguente per distribuire il servizio Azure SignalR seguente con il modello di Resource Manager nel portale di Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Pulsante per distribuire il servizio Azure SignalR in Azure con un modello di Resource Manager nel portale di Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Nella pagina **Deploy an Azure SignalR service** (Distribuisci un servizio Azure SignalR):

1. Facoltativamente, cambiare il valore predefinito di **Sottoscrizione**.

2. In **Gruppo di risorse** selezionare **Crea nuovo**, immettere un nome per il nuovo gruppo di risorse e quindi fare clic su **OK**.

3. Se è stato creato un nuovo gruppo di risorse, in **Area** selezionare un valore per il gruppo di risorse.

4. Facoltativamente, immettere nuovi valori per **Nome** e **Località** (ad esempio **eastus2**) del servizio Azure SignalR. Se non specificato, il nome viene generato automaticamente. La località per il servizio Azure SignalR può essere uguale o diversa dall'area del gruppo di risorse. Se non specificata, la località viene impostata sulla stessa area del gruppo di risorse.

5. Scegliere un valore per **Piano tariffario** (**Free_F1** o **Standard_S1**), immettere un valore per **Capacità** (numero di unità SignalR) e quindi per **Modalità servizio** scegliere **Predefinita** (richiede un server di hub), **Serverless** (non consente alcuna connessione server) o **Classica** (con il routing al server di hub solo se l'hub ha una connessione server). Quindi scegliere se abilitare **Enable Connectivity Logs** (Abilita log di connettività) o **Enable Messaging Logs** (Abilita log di messaggistica).

    > [!NOTE]
    > Per il piano tariffario **Free_F1**, la capacità è limitata a una unità.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Pulsante per distribuire il servizio Azure SignalR in Azure con un modello di Resource Manager nel portale di Azure.":::

6. Selezionare **Rivedi e crea**.

7. Leggere le condizioni e quindi selezionare **Crea**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Per eseguire gli script di PowerShell in locale, immettere prima di tutto `Connect-AzAccount` per configurare le credenziali di Azure.

Usare il codice seguente per distribuire il servizio Azure SignalR con il modello di Resource Manager. Il codice chiede le informazioni seguenti:

* Il nome e l'area del nuovo servizio Azure SignalR
* Il nome e l'area del nuovo gruppo di risorse
* Il piano tariffario di Azure (**Free_F1** o **Standard_S1**)
* La capacità di unità SignalR (1, 2, 5, 10, 20, 50 o 100)
  > [!NOTE]
  > Per il piano tariffario **Free_F1**, la capacità è limitata a una unità.
* La modalità di servizio: **Predefinita** per richiedere un server di hub, **Serverless** per non consentire alcuna connessione server o **Classica** per il routing a un server di hub solo se l'hub ha una connessione server
* Se abilitare i log per la connettività o per la messaggistica (**true** o **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Usare il codice seguente per distribuire il servizio Azure SignalR con il modello di Resource Manager. Il codice chiede le informazioni seguenti:

* Il nome e l'area del nuovo servizio Azure SignalR
* Il nome e l'area del nuovo gruppo di risorse
* Il piano tariffario di Azure (**Free_F1** o **Standard_S1**)
* La capacità di unità SignalR (1, 2, 5, 10, 20, 50 o 100)
    > [!NOTE]
    > Per il piano tariffario **Free_F1**, la capacità è limitata a una unità.
* La modalità di servizio: **Predefinita** per richiedere un server di hub, **Serverless** per non consentire alcuna connessione server o **Classica** per il routing a un server di hub solo se l'hub ha una connessione server
* Se abilitare i log per la connettività o per la messaggistica (**true** o **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Per il completamento della distribuzione possono essere necessari alcuni minuti. Prendere nota dei nomi del servizio Azure SignalR e del gruppo di risorse, che verranno usati per esaminare le risorse distribuite più avanti.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

# <a name="portal"></a>[Portale](#tab/azure-portal)

Seguire questa procedura per vedere una panoramica del nuovo servizio Azure SignalR:

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **SignalR**.

2. Nell'elenco di servizi SignalR selezionare il nuovo servizio. Viene visualizzata la pagina **Panoramica** del nuovo servizio Azure SignalR.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Eseguire il codice interattivo seguente per visualizzare i dettagli del servizio Azure SignalR. Sarà necessario immettere il nome del nuovo servizio e del gruppo di risorse.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Eseguire il codice interattivo seguente per visualizzare i dettagli del servizio Azure SignalR. Sarà necessario immettere il nome del nuovo servizio e del gruppo di risorse.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse per eliminare tutte le risorse contenute al suo interno.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Gruppi di risorse**.

2. Nell'elenco dei gruppi di risorse scegliere il nome del gruppo di risorse.

3. Nella pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.

4. Nella finestra di dialogo di conferma, digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello di Resource Manager, vedere:

> [!div class="nextstepaction"]
> [ Esercitazione: Creare e distribuire il primo modello di Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
