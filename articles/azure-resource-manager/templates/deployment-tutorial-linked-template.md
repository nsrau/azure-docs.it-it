---
title: 'Esercitazione: Distribuire un modello collegato'
description: Informazioni su come distribuire un modello collegato
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: a90bb90c90206ffe00f8b4f2d035c0ea844b5c47
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611674"
---
# <a name="tutorial-deploy-a-linked-template"></a>Esercitazione: Distribuire un modello collegato

Nelle [esercitazioni precedenti](./deployment-tutorial-local-template.md) si è appreso come distribuire un modello archiviato nel computer locale. Per distribuire soluzioni complesse, è possibile suddividere un modello in più modelli e distribuire questi modelli tramite un modello principale. In questa esercitazione si apprenderà come distribuire un modello principale che contiene il riferimento a un modello collegato. Quando il modello principale viene distribuito, viene attivata la distribuzione del modello collegato. Si apprenderà anche come archiviare e proteggere il modello collegato usando il token di firma di accesso condiviso. Per completare l'esercitazione, sono necessari circa **12 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'esercitazione precedente.

## <a name="review-template"></a>Rivedere il modello

Nelle esercitazioni precedenti si è distribuito un modello che crea un account di archiviazione, un piano di servizio app e un'app Web. Il modello usato era:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Creare un modello collegato

È possibile separare la risorsa dell'account di archiviazione da un modello collegato:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Il modello seguente è il modello principale.  L'oggetto evidenziato **Microsoft.Resources/deployments** mostra come chiamare un modello collegato. Il modello collegato non può essere archiviato come file locale o file disponibile unicamente nella rete locale. È possibile fornire solo un valore URI che includa *http* o *https*. Il servizio Resource Manager deve poter accedere al modello. È possibile inserire il modello collegato in un account di archiviazione e usare l'URI per tale elemento. L'URI viene passato al modello usando un parametro. Vedere la definizione del parametro evidenziato.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Salvare una copia del modello principale nel computer locale con l'estensione JSON, ad esempio azuredeploy.json. Non è necessario salvare una copia del modello collegato.  Il modello collegato verrà copiato da un repository GitHub in un account di archiviazione.

## <a name="store-the-linked-template"></a>Archiviare il modello collegato

Con lo script di PowerShell seguente viene creato un account di archiviazione, viene creato un contenitore e il modello collegato viene copiato da un repository GitHub nel contenitore. Una copia del modello collegato viene archiviata in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Selezionare **Prova** per aprire Cloud Shell, selezionare **Copia** per copiare lo script di PowerShell e fare clic con il pulsante destro del mouse sul riquadro della shell per incollare lo script:

> [!IMPORTANT]
> I nomi degli account di archiviazione devono essere di lunghezza compresa tra 3 e 24 caratteri e utilizzare solo numeri e lettere minuscole. Il nome deve essere univoco. Nel modello il nome dell'account di archiviazione è il nome del progetto con l'aggiunta di "store" e il nome del progetto deve avere una lunghezza compresa tra 3 e 11 caratteri. Quindi il nome del progetto deve soddisfare i requisiti del nome dell'account di archiviazione e deve avere meno di 11 caratteri.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Distribuire il modello

Per distribuire un modello privato in un account di archiviazione, generare un token di firma di accesso condiviso e includerlo nell'URI del modello. Impostare l'ora di scadenza in modo da garantire un tempo sufficiente per completare la distribuzione. Il BLOB contenente il modello sarà accessibile solo da parte del proprietario dell'account. Tuttavia, quando si crea un token di firma di accesso condiviso per il BLOB, quest'ultimo sarà accessibile a tutti gli utenti con quell'URI. Se l'URI viene intercettato da un altro utente, quest'ultimo sarà in grado di accedere al modello. Usare un token di firma di accesso condiviso è un buon metodo per limitare l'accesso ai modelli, ma è necessario non includere direttamente nel modello dati sensibili come le password.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> Nel codice dell'interfaccia della riga di comando di Azure seguente il parametro di data -d sarebbe un argomento non valido in macOS. Gli utenti macOS dovranno quindi usare dovranno usare -v+2H per aggiungere 2 ore all'ora corrente nel terminale in macOS.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare le risorse distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come distribuire un modello collegato. Nell'esercitazione successiva si apprenderà come creare una pipeline DevOp per distribuire un modello.

> [!div class="nextstepaction"]
> [Creare una pipeline](./deployment-tutorial-pipeline.md)
