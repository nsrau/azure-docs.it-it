---
title: Creare un'app del servizio app usando un modello di Azure Resource Manager
description: Creare la prima app nel Servizio app di Azure in pochi secondi usando un modello di Azure Resource Manager, uno dei molti modi per eseguire la distribuzione nel servizio app.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653269"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>Creare un'app del servizio app usando un modello di Azure Resource Manager

Per iniziare a usare il [Servizio app di Azure](overview.md), distribuire un'app nel cloud usando un modello di Azure Resource Manager e l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) in Cloud Shell. Poiché si usa un livello di servizio app gratuito, non verrà addebitato alcun costo per il completamento di questa guida di avvio rapido.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>Creare un'app del Servizio app di Azure

### <a name="review-the-template"></a>Rivedere il modello

::: zone pivot="platform-windows"
Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/). Distribuisce un piano di servizio app e un'app del servizio app in Windows. È compatibile con le app .NET Core, .NET Framework, PHP, Node.js e HTML statiche. Per Java, vedere [Creare un'app Java](app-service-web-get-started-java.md). 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

Nel modello sono definite due risorse di Azure:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): per creare un piano di servizio app.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): per creare un'app del servizio app.

Questo modello contiene diversi parametri predefiniti per praticità. Vedere la tabella seguente per le impostazioni predefinite dei parametri e le relative descrizioni:

| Parametri | Type    | Valore predefinito                | DESCRIZIONE |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Nome app |
| posizione   | string  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | Area dell'app |
| sku        | string  | "F1"                         | Dimensione delle istanze (F1 = livello gratuito) |
| Linguaggio   | string  | ".net"                       | Stack del linguaggio di programmazione (.net, php, node, html) |
| helloWorld | boolean | False                        | True = distribuire l'app "Hello World |
| repoUrl    | string  |                             | Repository Git esterno (facoltativo) |
::: zone-end
::: zone pivot="platform-linux"
Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/). Distribuisce un piano di servizio app e un'app del servizio app in Linux. È compatibile con tutti i linguaggi di programmazione supportati nel servizio app.

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

Nel modello sono definite due risorse di Azure:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): per creare un piano di servizio app.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): per creare un'app del servizio app.

Questo modello contiene diversi parametri predefiniti per praticità. Vedere la tabella seguente per le impostazioni predefinite dei parametri e le relative descrizioni:

| Parametri | Type    | Valore predefinito                | DESCRIZIONE |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Nome app |
| posizione   | string  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | Area dell'app |
| sku        | string  | "F1"                         | Dimensione delle istanze (F1 = livello gratuito) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "Stack del linguaggio di programmazione &#124; Versione" |
| repoUrl    | string  |                             | Repository Git esterno (facoltativo) |

---
::: zone-end


### <a name="deploy-the-template"></a>Distribuire il modello

In questo caso per distribuire il modello viene usata l'interfaccia della riga di comando di Azure. È anche possibile usare il portale di Azure, Azure PowerShell e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-powershell.md). 

Il codice seguente crea un gruppo di risorse, un piano di servizio app e un'app Web. Sono stati impostati automaticamente un gruppo di risorse, un piano di servizio app e una posizione predefiniti. Sostituire `<app-name>` con un nome di app univoco globale (i caratteri validi sono `a-z`, `0-9` e `-`).

::: zone pivot="platform-windows"
Eseguire il codice riportato di seguito per distribuire un'app .NET Framework in Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Per distribuire uno stack di linguaggio diverso, aggiornare `linuxFxVersion` con i valori appropriati. Di seguito sono riportati alcuni esempi. Per visualizzare le versioni correnti, eseguire questo comando in Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Linguaggio    | Esempio:                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> È possibile trovare altri [esempi di modelli del Servizio app di Azure qui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).


## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Passare a `http://<app_name>.azurewebsites.net/` e verificare che la creazione sia stata completata.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, [eliminare il gruppo di risorse](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [](deploy-local-git.md)Distribuire dall'archivio Git locale

> [!div class="nextstepaction"]
>ASP.NET Core con database SQL

> [!div class="nextstepaction"]
>Python con Postgres

> [!div class="nextstepaction"]
>PHP con MySQL

> [!div class="nextstepaction"]
> [Connettersi al database SQL di Azure con Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
>Eseguire il mapping di un dominio personalizzato