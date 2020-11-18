---
title: Creare la prima funzione con i modelli di Azure Resource Manager
description: Creare e distribuire in Azure una semplice funzione serverless attivata tramite HTTP usando un modello di Azure Resource Manager.
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 1e623405faa89ff41eccdaa57578bc8ac94cd78c
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422825"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Avvio rapido: Creare e distribuire risorse di Funzioni di Azure da un modello di Resource Manager

In questo articolo si userà un modello di Azure Resource Manager per creare una funzione che risponde alle richieste HTTP. 

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-account"></a>Account Azure 

Prima di iniziare, è necessario disporre di un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).

### <a name="create-a-local-functions-project"></a>Creare un progetto per le funzioni locale

Questo articolo richiede un progetto di codice per le funzioni locale da eseguire sulle risorse di Azure create. Se non si crea prima un progetto da pubblicare, non sarà possibile completare la sezione di questo articolo relativa alla distribuzione. 

Scegliere una delle schede seguenti, seguire il collegamento e completare la sezione per creare un'app per le funzioni nel linguaggio preferito:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Creare il progetto di Funzioni locale in un linguaggio a scelta in Visual Studio Code:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Creare il progetto per le funzioni locale in Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Riga di comando](#tab/command-line)

Creare il progetto di Funzioni locale in un linguaggio a scelta dalla riga di comando:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

Dopo aver creato il progetto in locale, è possibile creare le risorse necessarie per eseguire la nuova funzione in Azure. 

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/).

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Con questo modello vengono create le quattro risorse di Azure seguenti:

+ [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): crea un account di archiviazione di Azure, richiesto da Funzioni.
+ [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): crea un piano di hosting create a serverless a consumo per l'app per le funzioni.
+ [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): crea un'app per le funzioni.
+ [**microsoft.insights/components**](/azure/templates/microsoft.insights/components): crea un'istanza di Application Insights per il monitoraggio.

## <a name="deploy-the-template"></a>Distribuire il modello

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

A questo punto, è possibile convalidare le risorse di hosting dell'app per le funzioni create pubblicando il progetto in Azure e chiamando l'endpoint HTTP della funzione.

### <a name="publish-the-function-project-to-azure"></a>Pubblicare il progetto per le funzioni in Azure

Usare la procedura seguente per pubblicare il progetto nelle nuove risorse di Azure:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

Nell'output copiare l'URL del trigger HTTP, che viene usato per testare la funzione in esecuzione in Azure. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. In **Selezionare una destinazione di pubblicazione** scegliere **Piano a consumo di Funzioni di Azure** con **Seleziona esistente** e selezionare **Crea profilo**.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Scegliere una destinazione di pubblicazione esistente":::

1. Scegliere la **sottoscrizione**, espandere il gruppo di risorse, selezionare l'app per le funzioni e quindi fare clic su **OK**.

1. Al termine della pubblicazione, copiare il valore di **URL sito**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Copiare l'URL del sito dal riepilogo della pubblicazione":::

1. Aggiungere il percorso `/api/<FUNCTION_NAME>?name=Functions`, dove `<FUNCTION_NAME>` è il nome della funzione. Il formato dell'URL che chiama la funzione attivata tramite HTTP è il seguente:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Tale URL viene usato per testare la funzione attivata tramite HTTP in esecuzione in Azure.

# <a name="command-line"></a>[Riga di comando](#tab/command-line)

Per pubblicare il codice locale in un'app per le funzioni in Azure, usare il comando `publish`:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

In questo esempio sostituire `<FUNCTION_APP_NAME>` con il nome dell'app per le funzioni. Potrebbe essere necessario eseguire di nuovo l'accesso con il comando `az login`. 

Nell'output copiare l'URL del trigger HTTP, che viene usato per testare la funzione in esecuzione in Azure.

---

### <a name="invoke-the-function-on-azure"></a>Richiamare la funzione in Azure

Incollare l'URL copiato per la richiesta HTTP nella barra degli indirizzi del browser, assicurarsi di aggiungere alla fine dell'URL la stringa di query `name` come `?name=Functions` e quindi eseguire la richiesta. 

Verrà visualizzata una risposta simile alla seguente:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Pulire le risorse

Se si continua con il passaggio successivo e si aggiunge un binding di output della coda di Archiviazione di Azure, mantenere tutte le risorse esistenti per poterle riutilizzare.

In caso contrario, usare il comando seguente per eliminare il gruppo di risorse e tutte le relative risorse contenute per evitare di incorrere in costi aggiuntivi.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Sostituire `<RESOURCE_GROUP_NAME>` con il nome del gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver pubblicato la prima funzione, è possibile ottenere altre informazioni aggiungendo un'associazione di output alla funzione.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Riga di comando](#tab/command-line)

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-cli.md)

---
