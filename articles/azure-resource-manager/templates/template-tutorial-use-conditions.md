---
title: Usare una condizione nei modelli
description: Informazioni su come distribuire risorse di Azure in base a condizioni. Mostra come distribuire una nuova risorsa oppure usarne una esistente.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d902258c80467380518df3b55583cea1efa76609
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119311"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Esercitazione: Usare una condizione nei modelli di Azure Resource Manager

Informazioni su come distribuire le risorse di Azure in base alle condizioni in un modello di Azure Resource Manager (ARM).

Nell'esercitazione [Impostare l'ordine di distribuzione delle risorse](./template-tutorial-create-templates-with-dependent-resources.md) si creano una macchina virtuale, una rete virtuale e alcune altre risorse dipendenti, tra cui un account di archiviazione. Invece di creare ogni volta un nuovo account di archiviazione, si consente agli utenti di scegliere tra creare un nuovo account di archiviazione e usarne uno esistente. Per raggiungere questo obiettivo, si definisce un parametro aggiuntivo. Se il valore del parametro è "new", viene creato un nuovo account di archiviazione. In caso contrario, viene usato un account di archiviazione esistente con il nome specificato.

![Diagramma dell'uso di una condizione nel modello di Resource Manager](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello
> * Pulire le risorse

Questa esercitazione illustra solo uno scenario di base relativo all'uso delle condizioni. Per altre informazioni, vedere:

* [Struttura del file modello: Condizione](conditional-resource-deployment.md).
* [Distribuire una risorsa in modo condizionale in un modello di Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Funzioni di modello: If](./template-functions-logical.md#if).
* [Funzioni di confronto per i modelli di Azure Resource Manager](./template-functions-comparison.md)

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione Strumenti di Resource Manager. Vedere [Avvio rapido: Creare modelli di Azure Resource Manager con Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Di seguito è riportato un esempio della generazione di una password:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Azure Resource Manager](./template-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File**>**Apri file**.
1. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selezionare **Apri** per aprire il file.
1. Sono presenti sei risorse definite dal modello:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    È utile esaminare le informazioni di riferimento sul modello prima di personalizzare un modello.

1. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="modify-the-template"></a>Modificare il modello

Apportare due modifiche al modello esistente:

* Aggiungere un parametro per il nome dell'account di archiviazione. Gli utenti potranno specificare il nome di un nuovo account di archiviazione o di uno esistente.
* Aggiungere un nuovo parametro denominato **newOrExisting**. Questo parametro viene usato nella distribuzione per determinare se creare un nuovo account di archiviazione oppure usarne uno esistente.

Di seguito è riportata la procedura per apportare le modifiche:

1. Aprire **azuredeploy.json** in Visual Studio Code.
1. Sostituire le tre occorrenze di **variables('storageAccountName')** con **parameters('storageAccountName')** nell'intero modello.
1. Rimuovere la definizione di variabile seguente:

    ![Diagramma dell'uso di una condizione nel modello di Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Aggiungere i due parametri seguenti all'inizio della sezione Parameters:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Premere **[ALT]+[SHIFT]+F** per formattare il modello in Visual Studio Code.

    La definizione aggiornata dei parametri si presenta come segue:

    ![Usare una condizione in Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Aggiungere la riga seguente all'inizio della definizione dell'account di archiviazione.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    La condizione controlla il valore di un parametro denominato **newOrExisting**. Se il valore del parametro è **new**, con la distribuzione viene creato l'account di archiviazione.

    La definizione aggiornata dell'account di archiviazione si presenta come segue:

    ![Usare una condizione in Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Aggiornare la proprietà **storageUri** della definizione di risorsa di macchina virtuale con il valore seguente:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Questa modifica è necessaria quando si usa un account di archiviazione esistente in un diverso gruppo di risorse.

1. Salvare le modifiche.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Accedere ad [Azure Cloud Shell](https://shell.azure.com)

1. Scegliere l'ambiente preferito selezionando **PowerShell** o **Bash** (per l'interfaccia della riga di comando) nell'angolo in alto a sinistra.  Quando si cambia interfaccia, è necessario riavviare la shell.

    ![Caricare file in Cloud Shell nel portale di Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selezionare **Carica/Scarica file** e quindi **Carica**. Vedere l'immagine sopra riportata. Selezionare il file salvato nella sezione precedente. Dopo aver caricato il file, è possibile usare i comandi **ls** e **cat** per verificare che il file sia stato caricato.

1. quindi eseguire lo script di PowerShell seguente per distribuire il modello.

    > [!IMPORTANT]
    > Il nome dell'account di archiviazione deve essere univoco in Azure. Il nome deve essere composto solo da lettere minuscole e numeri e non deve superare i 24 caratteri. Il nome dell'account di archiviazione è il nome del progetto a cui viene aggiunto "store". Assicurarsi che il nome del progetto e il nome dell'account di archiviazione generato soddisfino i requisiti per il nome dell'account di archiviazione.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > Se **newOrExisting** è **new**, ma l'account di archiviazione con il nome specificato esiste già, la distribuzione ha esito negativo.

Provare a ripetere la distribuzione con il parametro **newOrExisting** impostato su "existing" e specificare un account di archiviazione esistente. Per creare prima un account di archiviazione, vedere [Creare un account di archiviazione](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse. Per eliminare il gruppo di risorse, selezionare **Prova** per aprire Cloud Shell. Per incollare lo script di PowerShell, fare clic con il pulsante destro del mouse sul riquadro della shell e quindi scegliere **Incolla**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato sviluppato un modello che consente agli utenti di scegliere tra creare un nuovo account di archiviazione e usarne uno esistente. Per informazioni su come recuperare segreti da Azure Key Vault e usarli come password nella distribuzione di modelli, vedere:

> [!div class="nextstepaction"]
> [Integrare Key Vault nella distribuzione di modelli](./template-tutorial-use-key-vault.md)
