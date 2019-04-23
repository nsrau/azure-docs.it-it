---
title: Importare file BACPAC di SQL con modelli di Azure Resource Manager | Microsoft Docs
description: Informazioni su come usare l'estensione per il database SQL per importare file BACPAC di SQL con modelli di Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7339c975bf979907bd1f9bbb46546b58ef7ae6e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282356"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Esercitazione: Importare file BACPAC di SQL con modelli di Azure Resource Manager

Informazioni su come usare estensioni per il database SQL per importare un file BACPAC con modelli di Azure Resource Manager. Gli elementi di distribuzione sono tutti i file, oltre al file di modello principale, necessari per completare una distribuzione. Il file BACPAC è uno di questi elementi. In questa esercitazione viene creato un modello per distribuire un server di Azure SQL e un database SQL e importare un file BACPAC. Per informazioni sulla distribuzione di estensioni di macchina virtuale di Azure tramite modelli di Azure Resource Manager, vedere [Esercitazione: Distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

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

* [Visual Studio Code](https://code.visualstudio.com/) con l'estensione Strumenti di Azure Resource Manager. Visualizzare [installare l'estensione ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore del server SQL. Di seguito è riportato un esempio della generazione di una password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault in Distribuzione modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="prepare-a-bacpac-file"></a>Preparare un file BACPAC

Un file BACPAC viene condiviso in un [account di archiviazione di Azure](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac) con accesso pubblico. Per creare un file BACPAC, vedere [Esportare un database SQL di Azure in un file BACPAC](../sql-database/sql-database-export.md). Se si sceglie di pubblicare il file in una posizione personalizzata, è necessario aggiornare il modello più avanti in questa esercitazione.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Il modello usato in questa esercitazione viene archiviato in un [account di archiviazione di Azure](https://armtutorials.blob.core.windows.net/createsql/azuredeploy.json). 

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://armtutorials.blob.core.windows.net/createsql/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.

    Nel modello sono definite tre risorse:

   * `Microsoft.Sql/servers`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
4. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

Aggiungere altre due risorse al modello.

* Per consentire all'estensione di database SQL di importare file BACPAC, è necessario consentire l'accesso ai servizi di Azure. Aggiungere il codice JSON seguente alla definizione del server SQL:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2015-05-01-preview",
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
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    Il modello si presenterà come segue:

    ![Distribuzione di estensioni SQL e BACPAC con Azure Resource Manager](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    Per informazioni sulla definizione della risorsa, vedere le [informazioni di riferimento sulle estensioni di database SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Di seguito sono illustrati alcuni elementi importanti.

    * **dependsOn**: la risorsa di estensione deve essere creata dopo che è stato creato il database SQL.
    * **storageKeyType**: tipo di chiave di archiviazione da usare. Il valore può essere `StorageAccessKey` o `SharedAccessKey`. Dato che il file BACPAC fornito viene condiviso in un account di archiviazione di Azure con accesso pubblico, in questo caso viene usato il tipo "SharedAccessKey".
    * **storageKey**: chiave di archiviazione da usare. Se il tipo di chiave di archiviazione è SharedAccessKey, deve essere preceduta da "?".
    * **storageUri**: URI di archiviazione da usare. Se si sceglie di non usare il file BACPAC fornito, è necessario aggiornare i valori.
    * **administratorLoginPassword**: password dell'amministratore di SQL. Usare una password generata. Vedere [Prerequisiti](#prerequisites).

## <a name="deploy-the-template"></a>Distribuire il modello

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vedere la sezione [Distribuire il modello](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) per la procedura di distribuzione. Usare invece lo script di distribuzione di PowerShell seguente:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json"
```

Usare una password generata. Vedere [Prerequisiti](#prerequisites).

## <a name="verify-the-deployment"></a>Verificare la distribuzione

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
