---
title: "Avvio rapido: Distribuire l'API di Azure per FHIR tramite un modello di Resource Manager"
description: Questo argomento di avvio rapido illustra come distribuire l'API di Azure per Fast Healthcare Interoperability Resources (FHIR®) usando un modello di Azure Resource Manager (ARM).
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: cavoeg
ms.date: 10/06/2020
ms.openlocfilehash: c04bb82810bf55d6ac55b2697f5010896e0eb9b4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289282"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Avvio rapido: Usare un modello di Resource Manager per distribuire l'API di Azure per FHIR

Questo argomento di avvio rapido illustra come usare un modello di Azure Resource Manager (ARM) per distribuire l'API di Azure per Fast Healthcare Interoperability Resources (FHIR®). Per distribuire l'API di Azure per FHIR è possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure dopo l'accesso.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuire in Azure un servizio API di Azure per FHIR usando un modello di Resource Manager nel portale di Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

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

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/).

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

Il modello definisce una risorsa di Azure:

* **Microsoft.HealthcareApis/services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>Distribuire il modello

# <a name="portal"></a>[Portale](#tab/azure-portal)

Selezionare il collegamento seguente per distribuire l'API di Azure per FHIR usando il modello di Resource Manager nel portale di Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuire in Azure un servizio API di Azure per FHIR usando il modello di Resource Manager nel portale di Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

Nella pagina **Deploy Azure API for FHIR** (Distribuisci API di Azure per FHIR):

1. Se si vuole, in **Sottoscrizione** sostituire l'impostazione predefinita con un'altra sottoscrizione.

2. In **Gruppo di risorse** selezionare **Crea nuovo** , immettere un nome per il nuovo gruppo di risorse e quindi fare clic su **OK**.

3. Se è stato creato un nuovo gruppo di risorse, in **Area** selezionare un valore per il gruppo di risorse.

4. Immettere un nuovo **nome del servizio** e scegliere la **località** dell'API di Azure per FHIR. La località può corrispondere o meno all'area del gruppo di risorse.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Distribuire l'API di Azure per FHIR usando il modello di Resource Manager nel portale di Azure.":::

5. Selezionare **Rivedi e crea**.

6. Leggere le condizioni e quindi selezionare **Crea**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Per eseguire gli script di PowerShell in locale, immettere prima di tutto `Connect-AzAccount` per configurare le credenziali di Azure.

Se il provider di risorse `Microsoft.HealthcareApis` non è ancora stato registrato per la sottoscrizione, è possibile registrarlo con il codice interattivo seguente. Per eseguire il codice in Azure Cloud Shell, selezionare **Prova** nell'angolo superiore di qualsiasi blocco di codice.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Usare il codice seguente per distribuire il servizio API di Azure per FHIR usando il modello di Resource Manager. Il codice chiede di immettere il nome del nuovo servizio FHIR, il nome di un nuovo gruppo di risorse e le località per ognuno di essi.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Se il provider di risorse `Microsoft.HealthcareApis` non è ancora stato registrato per la sottoscrizione, è possibile registrarlo con il codice interattivo seguente. Per eseguire il codice in Azure Cloud Shell, selezionare **Prova** nell'angolo superiore di qualsiasi blocco di codice.

```azurecli-interactive
az extension add --name healthcareapis
```

Usare il codice seguente per distribuire il servizio API di Azure per FHIR usando il modello di Resource Manager. Il codice chiede di immettere il nome del nuovo servizio FHIR, il nome di un nuovo gruppo di risorse e le località per ognuno di essi.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Il completamento della distribuzione richiede alcuni minuti. Prendere nota dei nomi del servizio API di Azure per FHIR e del gruppo di risorse, che verranno usati più avanti per verificare le risorse distribuite.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

# <a name="portal"></a>[Portale](#tab/azure-portal)

Seguire questa procedura per visualizzare una panoramica del nuovo servizio API di Azure per FHIR:

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **API di Azure per FHIR**.

2. Nell'elenco dei servizi FHIR selezionare il nuovo servizio. Verrà visualizzata la pagina **Panoramica** del nuovo servizio API di Azure per FHIR.

3. Per verificare che sia stato effettuato il provisioning del nuovo account API FHIR, selezionare il collegamento accanto a **endpoint di metadati FHIR** per recuperare la dichiarazione di funzionalità dell'API FHIR. Il collegamento è nel formato `https://<service-name>.azurehealthcareapis.com/metadata`. Se è stato effettuato il provisioning dell'account, viene visualizzato un file JSON di grandi dimensioni.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Eseguire il codice interattivo seguente per visualizzare i dettagli del servizio API di Azure per FHIR. Sarà necessario immettere il nome del nuovo servizio e del gruppo di risorse.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Eseguire il codice interattivo seguente per visualizzare i dettagli del servizio API di Azure per FHIR. Sarà necessario immettere il nome del nuovo servizio e del gruppo di risorse.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
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

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello di Resource Manager, vedere [Esercitazione: Creare e distribuire il primo modello di Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata distribuita l'API di Azure per FHIR nella sottoscrizione. Per configurare le impostazioni aggiuntive nell'API di Azure per FHIR, passare alla guida pratica sulle impostazioni aggiuntive. Se si vuole iniziare a usare l'API di Azure per FHIR, leggere gli articoli relativi alla registrazione delle applicazioni.

>[!div class="nextstepaction"]
>[Impostazioni aggiuntive nell'API di Azure per FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Panoramica della registrazione di applicazioni](fhir-app-registration.md)
