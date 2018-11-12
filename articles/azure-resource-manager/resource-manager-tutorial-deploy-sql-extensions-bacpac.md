---
title: Importare file BACPAC di SQL con modelli di Azure Resource Manager | Microsoft Docs
description: Informazioni su come usare un'estensione di database SQL per importare file BACPAC di SQL con modelli di Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/29/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fa7d48c9a8079dc9171879ab5b72e3f04f870ebc
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036175"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Esercitazione: Importare file BACPAC di SQL con modelli di Azure Resource Manager

Informazioni su come usare le estensioni di database SQL di Azure per importare un file BACPAC. In questa esercitazione si crea un modello per distribuire un server SQL Azure, un database SQL e un file BACPAC. Per informazioni sulla distribuzione di estensioni di macchina virtuale di Azure con modelli di Azure Resource Manager, vedere [Esercitazione: Distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

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
    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="prepare-a-bacpac-file"></a>Preparare un file BACPAC

Un file BACPAC è condiviso in un [account di archiviazione di Azure con accesso pubblico](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac). Per creare un file BACPAC, vedere [Esportare un database SQL di Azure in un file BACPAC](../sql-database/sql-database-export.md). Se si sceglie di pubblicare il file in una posizione personalizzata, è necessario aggiornare il modello più avanti in questa esercitazione.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Azure SQL Server with Threat Detection](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/) (Server SQL Azure con rilevamento delle minacce).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.

    Nel modello sono definite tre risorse:

    * `Microsoft.Sql/servers`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
    * `Microsoft.SQL/servers/securityAlertPolicies`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
    * `Microsoft.SQL.servers/databases`.  Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
    Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
4. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

È necessario aggiungere altre due risorse al modello.

* Per consentire all'estensione di database SQL di importare file BACPAC, è necessario consentire l'accesso ai servizi di Azure. Aggiungere il codice JSON seguente alla definizione del server SQL:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2014-04-01",
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

    * **dependsOn**: la risorsa estensione deve essere creata dopo che è stato creato il database SQL.
    * **storageKeyType**: tipo di chiave di archiviazione da usare. Il valore può essere `StorageAccessKey` o `SharedAccessKey`. Dato che il file BACPAC fornito viene condiviso in un account di archiviazione di Azure con accesso pubblico, in questo caso viene usato il tipo "SharedAccessKey".
    * **storageKey**: chiave di archiviazione da usare. Se il tipo di chiave di archiviazione è SharedAccessKey, deve essere preceduta da "?".
    * **storageUri**: URI di archiviazione da usare. Se si sceglie di non usare il file BACPAC fornito, è necessario aggiornare i valori.
    * **administratorLoginPassword**: password dell'amministratore di SQL. È consigliabile usare una password generata. Vedere [Prerequisiti](#prerequisites).

## <a name="deploy-the-template"></a>Distribuire il modello

Vedere la sezione [Distribuire il modello](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) per la procedura di distribuzione. Usare invece lo script di distribuzione di PowerShell seguente:

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile azuredeploy.json
```

È consigliabile usare una password generata. Vedere [Prerequisiti](#prerequisites).

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

In questa esercitazione sono stati distribuiti un server SQL e un database SQL ed è stato importato un file BACPAC. Per informazioni su come distribuire le risorse di Azure in più aree e su come usare procedure di distribuzione sicure, vedere:

> [!div class="nextstepaction"]
> [Usare Azure Deployment Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
