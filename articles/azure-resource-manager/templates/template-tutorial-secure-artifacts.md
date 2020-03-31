---
title: Proteggere gli artefatti nei modelli
description: Informazioni su come proteggere gli elementi usati nei modelli di Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ad6ea3c68ed6f48ac48bbbdafed7f8660df23937
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239223"
---
# <a name="tutorial-secure-artifacts-in-arm-template-deployments"></a>Esercitazione: Proteggere gli artefatti nelle distribuzioni dei modelli di Azure Resource Manager

Informazioni su come proteggere gli elementi usati nei modelli di Azure Resource Manager tramite un account di archiviazione di Azure con firme di accesso condiviso (SAS). Gli elementi di distribuzione sono tutti i file, oltre al file di modello principale, necessari per completare una distribuzione. Ad esempio, in [Esercitazione: Importare file BACPAC SQL con i modelli di Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md), il modello principale crea un'istanza di database SQL di Azure e chiama anche un file BACPAC per creare tabelle e inserire i dati. Il file BACPAC è un artefatto che viene archiviato in un account di archiviazione di Azure. Per accedere all'artefatto è stata usata una chiave dell'account di archiviazione.

In questa esercitazione si usa la firma di accesso condiviso per concedere un accesso limitato al file BACPAC nel proprio account di archiviazione di Azure. Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Per informazioni su come proteggere un modello collegato, vedere [Esercitazione: Creare modelli di Azure Resource Manager collegati](./template-tutorial-create-linked-templates.md).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare un file BACPAC.
> * Aprire un modello esistente.
> * Modificare il modello.
> * Distribuire il modello.
> * Verificare la distribuzione.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione Strumenti di Resource Manager. Vedere [Usare Visual Studio Code per creare i modelli di Azure Resource Manager](./use-vs-code-to-create-template.md).
* Rivedere [Esercitazione: Importare file BACPAC SQL con i modelli di Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md). Il modello usato in questa esercitazione è quello sviluppata in tale esercitazione. In questo articolo viene fornito un collegamento di download del modello completato.
* Per una maggiore sicurezza, usare una password generata per l'account amministratore del server SQL. Di seguito è riportato un esempio che è possibile usare per generare una password:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Azure Resource Manager](./template-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="prepare-a-bacpac-file"></a>Preparare un file BACPAC

In questa sezione si prepara il file BACPAC affinché sia accessibile in modo sicuro quando si distribuisce il modello di Azure Resource Manager. In questa sezione sono disponibili cinque procedure:

* Scaricare il file BACPAC.
* Creare un account di archiviazione di Azure.
* Creare un contenitore BLOB nell'account di archiviazione.
* Caricare il file BACPAC nel contenitore.
* Recuperare il token di firma di accesso condiviso del file BACPAC.

1. Selezionare **Prova** per aprire Cloud Shell. Incollare lo script di PowerShell seguente nella finestra della shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

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
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Prendere nota dell'URL del file BACPAC e del token di firma di accesso condiviso. Questi valori saranno necessari per distribuire il modello.

## <a name="open-an-existing-template"></a>Aprire un modello esistente

In questa sessione si modifica il modello creato in [Esercitazione: Importare file BACPAC SQL con modelli di Azure Resource Manager](./template-tutorial-deploy-sql-extensions-bacpac.md) per chiamare il file BACPAC con un token di firma di accesso condiviso. Il modello sviluppato nell'esercitazione dell'estensione SQL è condiviso in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. In Visual Studio Code selezionare **File** > **Apri file**.
1. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Selezionare **Apri** per aprire il file.

    Nel modello sono definite quattro risorse:

   * `Microsoft.Sql/servers`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
1. Selezionare **File** > **Salva con nome** per salvare una copia del file con il nome *azuredeploy.json* nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

1. Sostituire la definizione del parametro storageAccountKey con la definizione del parametro seguente:

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Parametri degli elementi protetti nell'esercitazione di Resource Manager](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Aggiornare il valore dei tre elementi seguenti della risorsa di estensione SQL:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

Il modello completato è simile a:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Distribuire il modello

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Vedere la sezione [Distribuire il modello](./template-tutorial-create-multiple-instances.md#deploy-the-template) per la procedura di distribuzione. Usare invece lo script di distribuzione di PowerShell seguente.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Usare una password generata. Vedere [Prerequisiti](#prerequisites).
Per i valori di _artifactsLocation, _artifactsLocationSasToken e bacpacFileName, vedere [Preparare un file BACPAC](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Nel portale selezionare il database SQL nel gruppo di risorse appena distribuito. Selezionare **Editor di query (anteprima)** e quindi immettere le credenziali di amministratore. Verranno visualizzate le due tabelle importate nel database.

![Editor di query (anteprima)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse. Nel gruppo di risorse verrà visualizzato un totale di sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati distribuiti un server SQL e un database SQL ed è stato importato un file BACPAC tramite un token di firma di accesso condiviso. Per informazioni su come distribuire le risorse di Azure in più aree e su come usare procedure di distribuzione sicure, vedere:

> [!div class="nextstepaction"]
> [Usare procedure di distribuzione sicure](./deployment-manager-tutorial.md)
