---
title: Proteggere gli artefatti - Modelli di Azure Resource Manager
description: Informazioni su come proteggere gli elementi usati nei modelli di Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d9570c30e2870a136bd97ed5fc2304d6f0d280c9
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528284"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Esercitazione: Proteggere gli elementi nelle distribuzioni di modelli di Azure Resource Manager

Informazioni su come proteggere gli elementi usati nei modelli di Azure Resource Manager tramite un account di archiviazione di Azure con firme di accesso condiviso (SAS). Gli elementi di distribuzione sono tutti i file, oltre al file di modello principale, necessari per completare una distribuzione. Ad esempio, in [Esercitazione: Importare file BACPAC di SQL con i modelli di Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), il modello principale crea un database SQL di Azure e chiama anche un file BACPAC per creare tabelle e inserire i dati. Il file BACPAC è uno di questi elementi. L'elemento viene archiviato in un account di archiviazione di Azure con accesso pubblico. In questa esercitazione si usa la firma di accesso condiviso per concedere un accesso limitato al file BACPAC nel proprio account di archiviazione di Azure. Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Per informazioni su come proteggere un modello collegato, vedere [Esercitazione: Creare modelli collegati di Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare un file BACPAC
> * Aprire un modello esistente
> * Modificare il modello
> * Distribuire il modello
> * Verificare la distribuzione

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) con l'estensione Strumenti di Azure Resource Manager. Visualizzare [installare l'estensione ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Rivedere [Esercitazione: Importare file BACPAC di SQL con modelli di Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Il modello usato in questa esercitazione è quello sviluppata in tale esercitazione. In questo articolo viene fornito un collegamento di download del modello completato.
* Per una maggiore sicurezza, usare una password generata per l'account amministratore del server SQL. Di seguito è riportato un esempio della generazione di una password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault in Distribuzione modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="prepare-a-bacpac-file"></a>Preparare un file BACPAC

In questa sezione si prepara il file BACPAC in modo che sia accessibile in modo sicuro quando si distribuisce il modello di Resource Manager. In questa sezione sono disponibili cinque procedure:

* Scaricare il file BACPAC.
* Creare un account di archiviazione di Azure.
* Creare un contenitore BLOB nell'account di archiviazione.
* Caricare il file BACPAC nel contenitore.
* Recuperare il token di firma di accesso condiviso del file BACPAC.

Per automatizzare questi passaggi usando uno script di PowerShell, vedere lo script in [Caricare il modello collegato](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Scaricare il file BACPAC

Scaricare il [file BACPAC](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) e salvarlo nel computer locale con lo stesso nome, **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

1. Selezionare l'immagine seguente per aprire un modello di Resource Manager nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Immettere le seguenti proprietà:

    * **Sottoscrizione** Selezionare la sottoscrizione di Azure.
    * **Gruppo di risorse**: Selezionare **Crea nuovo** e assegnargli un nome. Un gruppo di risorse è un contenitore per le risorse di Azure con scopi di gestione. In questa esercitazione è possibile usare lo stesso gruppo di risorse per l'account di archiviazione e il database SQL di Azure. Prendere nota del nome di questo gruppo di risorse, sarà necessario alla creazione successiva del database SQL di Azure nelle esercitazioni.
    * **Località**: Scegliere un'area, Ad esempio **Stati Uniti centrali**.
    * **Tipo di account di archiviazione**: usare il valore predefinito, ovvero **Standard_LRS**.
    * **Località**: Usare il valore predefinito, ovvero **[resourceGroup().location]** . Si usa pertanto la posizione del gruppo di risorse per l'account di archiviazione.
    * **Accetto le condizioni riportate sopra**: (selezionato)
3. Selezionare **Acquisto**.
4. Selezionare l'icona di notifica a forma di campanello in alto a destra nel portale per visualizzare lo stato di distribuzione.

    ![Riquadro notifiche del portale nell'esercitazione di Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Dopo aver distribuito correttamente l'account di archiviazione, selezionare **Vai al gruppo di risorse** dal riquadro di notifica per aprire il gruppo di risorse.

### <a name="create-a-blob-container"></a>Creare un contenitore BLOB

Prima di poter caricare i file, è necessario un contenitore BLOB.

1. Selezionare l'account di archiviazione per aprirlo. Nel gruppo di risorse verrà visualizzato solo un account di archiviazione. Il nome del proprio account di archiviazione è diverso da quello mostrato nello screenshot seguente.

    ![Account di archiviazione nell'esercitazione di Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Selezionare il riquadro **BLOB**.

    ![BLOB dell'esercitazione di Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.
4. Immettere i valori seguenti:

    * **Nome**: immettere **sqlbacpac**.
    * **Livello di accesso pubblico**: usare il valore predefinito, **Privato - nessun accesso anonimo**.
5. Selezionare **OK**.
6. Selezionare **sqlbacpac** per aprire il contenitore appena creato.

### <a name="upload-the-bacpac-file-to-the-container"></a>Caricare il file BACPAC nel contenitore

1. Selezionare **Carica**.
2. Immettere i valori seguenti:

    * **File**: Seguendo le istruzioni per selezionare il file BACPAC scaricato in precedenza. Il nome predefinito è **SQLDatabaseExtension.bacpac**.
    * **Tipo di autenticazione**: Selezionare **Firma di accesso condiviso**.  *Firma di accesso condiviso* è il valore predefinito.
3. Selezionare **Carica**.  Una volta caricato correttamente il file, il suo nome verrà elencato nel contenitore.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Generare un token di firma di accesso condiviso

1. Fare doppio clic su **SQLDatabaseExtension.bacpac** dal contenitore e quindi selezionare **Genera firma di accesso condiviso**.
2. Immettere i valori seguenti:

    * **Autorizzazione**: Usare il valore predefinito **Read**.
    * **Data/ora di inizio e di fine**: Il valore predefinito fornisce otto ore per usare il token di firma di accesso condiviso. Se è necessario più tempo per completare questa esercitazione, aggiornare il campo **Scadenza**.
    * **Indirizzi IP consentiti**: Lasciare vuoto questo campo.
    * **Protocolli consentiti**: usare il valore predefinito: **HTTPS**.
    * **Chiave di firma**: usare il valore predefinito: **Chiave 1**.
3. Selezionare **Genera token di firma di accesso condiviso e URL**.
4. Creare una copia dell'**URL di firma di accesso condiviso del BLOB**. Al centro l'URL si trova il nome file **SQLDatabaseExtension.bacpac**.  Il nome del file divide l'URL in tre parti:

   - **Percorso dell'elemento**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Assicurarsi che il percorso termini con un "/".
   - **Nome file BACPAC**: SQLDatabaseExtension.bacpac.
   - **Token di firma di accesso condiviso posizione elementi**: Assicurarsi che il token sia preceduto da un "?."

     Sono necessari questi tre valori in [distribuire il modello](#deploy-the-template).

## <a name="open-an-existing-template"></a>Aprire un modello esistente

In questa sessione si modifica il modello creato in [Esercitazione: Importare file BACPAC di SQL con modelli di Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) per chiamare il file BACPAC con un token di firma di accesso condiviso.  Il modello sviluppato nell'esercitazione dell'estensione SQL è condiviso in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.

    Sono presenti cinque risorse definite nel modello:

   * `Microsoft.Sql/servers`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
4. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

Aggiungere i parametri seguenti:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Parametri degli elementi protetti nell'esercitazione di Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Aggiornare il valore dei due elementi seguenti:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Distribuire il modello

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vedere la sezione [Distribuire il modello](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) per la procedura di distribuzione. Usare invece lo script di distribuzione di PowerShell seguente:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Usare una password generata. Vedere [Prerequisiti](#prerequisites).
Per i valori di _artifactsLocation, _artifactsLocationSasToken and bacpacFileName, vedere [Generare un token di firma di accesso condiviso](#generate-a-sas-token).

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

In questa esercitazione sono stati distribuiti un server SQL e un database SQL ed è stato importato un file BACPAC tramite un token di firma di accesso condiviso. Per informazioni su come creare una pipeline di Azure per sviluppare e distribuire modelli di Resource Manager in modo continuo, vedere

> [!div class="nextstepaction"]
> [Integrazione continua con Azure Pipelines](./resource-manager-tutorial-use-azure-pipelines.md)
