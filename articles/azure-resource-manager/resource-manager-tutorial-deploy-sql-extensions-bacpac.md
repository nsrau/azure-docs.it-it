---
title: Importare file BACPAC SQL con i modelli
description: Informazioni su come usare l'estensione per il database SQL per importare file BACPAC di SQL con modelli di Azure Resource Manager.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 741521551335712400e5f61822d7dda31199d3df
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422194"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Esercitazione: Importare file BACPAC di SQL con modelli di Azure Resource Manager

Informazioni su come usare estensioni per il database SQL di Azure per importare un file BACPAC con modelli di Azure Resource Manager. Gli artefatti della distribuzione sono tutti i file, oltre al file di modello principale, necessari per completare una distribuzione. Il file BACPAC è uno di questi elementi. In questa esercitazione viene creato un modello per distribuire un server di Azure SQL e un database SQL e importare un file BACPAC. Per informazioni sulla distribuzione di estensioni di macchina virtuale di Azure tramite modelli di Azure Resource Manager, vedere [Esercitazione: Distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare un file BACPAC
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello
> * Verificare la distribuzione

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione di Strumenti di Resource Manager. Vedere [Usare Visual Studio Code per creare modelli di Azure Resource Manager](./resource-manager-tools-vs-code.md).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore del server SQL. Di seguito è riportato un esempio della generazione di una password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault in Distribuzione modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="prepare-a-bacpac-file"></a>Preparare un file BACPAC

Un file BACPAC viene condiviso in [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Per creare un file BACPAC, vedere [Esportare un database SQL di Azure in un file BACPAC](../sql-database/sql-database-export.md). Se si sceglie di pubblicare il file in una posizione personalizzata, è necessario aggiornare il modello più avanti in questa esercitazione.

Il file BACPAC deve essere archiviato in un account di archiviazione di Azure prima di poter essere importato con un modello di Resource Manager.

1. Aprire [Cloud Shell](https://shell.azure.com).
1. Selezionare **Carica/Scarica file** e quindi **Carica**.
1. Specificare l'URL seguente, quindi selezionare **Apri**.

    ```url
    https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac
    ```

1. Copiare e incollare lo script PowerShell seguente nella finestra della shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFile = "$HOME/SQLDatabaseExtension.bacpac"
    $blobName = "SQLDatabaseExtension.bacpac"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    Set-AzStorageBlobContent -File $bacpacFile `
                             -Container $containerName `
                             -Blob $blobName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$blobName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Prendere nota della chiave dell'account di archiviazione e dell'URL del file BACPAC. Questi valori saranno necessari per distribuire il modello.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Il modello usato in questa esercitazione è archiviato in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Selezionare **Apri** per aprire il file.

    Nel modello sono definite tre risorse:

   * `Microsoft.Sql/servers`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
4. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

1. Aggiungere altri due parametri alla fine della sezione **parameters** per impostare la chiave dell'account di archiviazione e l'URL BACPAC:

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Aggiungere una virgola dopo **adminPassword**. Per formattare il file JSON da VS Code, premere **[MAIUSC] + [ALT] + F**.

    Per informazioni su come ottenere questi due valori, vedere [Preparare un file BACPAC](#prepare-a-bacpac-file).

1. Aggiungere altre due risorse al modello.

    * Per consentire all'estensione di database SQL di importare file BACPAC, è necessario consentire il traffico proveniente dai servizi di Azure. Aggiungere la definizione della regola del firewall seguente nella definizione di SQL Server:

        ```json
        {
          "type": "firewallrules",
          "apiVersion": "2015-05-01-preview",
          "name": "AllowAllAzureIps",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
          }
        }
        ```

        Il modello si presenterà come segue:

        ![Distribuzione di estensioni SQL e BACPAC con Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Aggiungere una risorsa estensione di database SQL alla definizione del database con il codice JSON seguente:

        ```json
        "resources": [
            {
              "type": "extensions",
              "apiVersion": "2014-04-01",
              "name": "Import",
              "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
              ],
              "properties": {
                "storageKeyType": "StorageAccessKey",
                "storageKey": "[parameters('storageAccountKey')]",
                "storageUri": "[parameters('bacpacUrl')]",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import"
              }
            }
        ]
        ```

        Il modello si presenterà come segue:

        ![Distribuzione di estensioni SQL e BACPAC con Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Per informazioni sulla definizione della risorsa, vedere le [informazioni di riferimento sulle estensioni di database SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Di seguito sono illustrati alcuni elementi importanti.

        * **dependsOn**: la risorsa di estensione deve essere creata dopo che è stato creato il database SQL.
        * **storageKeyType**: specificare il tipo di chiave di archiviazione da usare. Il valore può essere `StorageAccessKey` o `SharedAccessKey`. In questa esercitazione usare `StorageAccessKey`.
        * **storageKey**: specificare la chiave dell'account di archiviazione in cui è archiviato il file BACPAC. Se il tipo di chiave di archiviazione è SharedAccessKey, deve essere preceduta da "?".
        * **storageUri**: specificare l'URL del file BACPAC archiviato in un account di archiviazione.
        * **administratorLoginPassword**: password dell'amministratore di SQL. Usare una password generata. Vedere [Prerequisiti](#prerequisites).

## <a name="deploy-the-template"></a>Distribuire il modello

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vedere la sezione [Distribuire il modello](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) per la procedura di distribuzione. Usare invece lo script di distribuzione di PowerShell seguente:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

È consigliabile usare lo stesso nome di progetto usato durante la preparazione del file BACPAC, in modo che tutte le risorse vengano archiviate nello stesso gruppo di risorse.  In questo modo la gestione delle risorse, ad esempio la pulizia, risulta più facile. Se si usa lo stesso nome di progetto, è possibile rimuovere il comando **New-AzResourceGroup** dallo script oppure rispondere y o n quando viene chiesto se si vuole aggiornare il gruppo di risorse esistente.

Usare una password generata. Vedere [Prerequisiti](#prerequisites).

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Per accedere a SQL Server dal computer client, è necessario aggiungere una regola del firewall aggiuntiva. Per altre informazioni, vedere [Creare e gestire le regole del firewall IP](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

Nel portale selezionare il database SQL nel gruppo di risorse appena distribuito. Selezionare **Editor di query (anteprima)** e quindi immettere le credenziali di amministratore. Verranno visualizzate le due tabelle importate nel database:

![Distribuzione di estensioni SQL e BACPAC con Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati distribuiti un server SQL e un database SQL ed è stato importato un file BACPAC. Il file BACPAC viene archiviato nell'account di archiviazione di Azure. Chiunque abbia l'URL può accedere al file. Per informazioni su come proteggere il file BACPAC (elemento), vedere

> [!div class="nextstepaction"]
> [Secure the artifacts](./resource-manager-tutorial-secure-artifacts.md) (Proteggere gli elementi)
