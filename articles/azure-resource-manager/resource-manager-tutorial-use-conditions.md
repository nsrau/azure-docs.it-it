---
title: Usare una condizione nei modelli di Azure Resource Manager | Microsoft Docs
description: Informazioni su come distribuire risorse di Azure in base a condizioni.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 325071be56935ca02adccf69f99fa1718e3f7b91
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239424"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Esercitazione: Usare una condizione nei modelli di Azure Resource Manager

Informazioni su come distribuire risorse di Azure in base a condizioni.

Nell'esercitazione [Impostare l'ordine di distribuzione delle risorse](./resource-manager-tutorial-create-templates-with-dependent-resources.md) si creano una macchina virtuale, una rete virtuale e alcune altre risorse dipendenti, tra cui un account di archiviazione. Invece di creare ogni volta un nuovo account di archiviazione, si consente agli utenti di scegliere tra creare un nuovo account di archiviazione e usarne uno esistente. Per raggiungere questo obiettivo, si definisce un parametro aggiuntivo. Se il valore del parametro è "new", viene creato un nuovo account di archiviazione.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello
> * Pulire le risorse

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) con l'[estensione Strumenti di Azure Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Di seguito è riportato un esempio della generazione di una password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Sono presenti cinque risorse definite dal modello:

    * `Microsoft.Storage/storageAccounts`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
5. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="modify-the-template"></a>Modificare il modello

Apportare due modifiche al modello esistente:

* Aggiungere un parametro per il nome dell'account di archiviazione. Gli utenti potranno specificare il nome di un nuovo account di archiviazione o di uno esistente.
* Aggiungere un nuovo parametro denominato **newOrExisting**. Questo parametro viene usato nella distribuzione per determinare se creare un nuovo account di archiviazione oppure usarne uno esistente.

Di seguito è riportata la procedura per apportare le modifiche:

1. Aprire **azuredeploy.json** in Visual Studio Code.
2. Sostituire **variables('storageAccountName')** con **parameters('storageAccountName')** nell'intero modello.  Sono presenti tre occorrenze di **variables('storageAccountName')**.
3. Rimuovere la definizione di variabile seguente:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. Aggiungere i due parametri seguenti al modello:

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
    La definizione aggiornata dei parametri si presenta come segue:

    ![Usare una condizione in Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Aggiungere la riga seguente all'inizio della definizione dell'account di archiviazione.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    La condizione controlla il valore di un parametro denominato **newOrExisting**. Se il valore del parametro è **new**, con la distribuzione viene creato l'account di archiviazione.

    La definizione aggiornata dell'account di archiviazione si presenta come segue:

    ![Usare una condizione in Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Aggiornare **storageUri** con il valore seguente:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Questa modifica è necessaria quando si usa un account di archiviazione esistente in un diverso gruppo di risorse.

7. Salvare le modifiche.

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello, seguire le istruzioni riportate in [Distribuire il modello](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template).

Quando si distribuisce il modello con Azure PowerShell, è necessario specificare un parametro aggiuntivo. Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Vedere [Prerequisiti](#prerequisites).

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile azuredeploy.json
```

> [!NOTE]
> Se **newOrExisting** è **new**, ma l'account di archiviazione con il nome specificato esiste già, la distribuzione ha esito negativo.

Provare a ripetere la distribuzione con il parametro **newOrExisting** impostato su "existing" e specificare un account di archiviazione esistente. Per creare prima un account di archiviazione, vedere [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato sviluppato un modello che consente agli utenti di scegliere tra creare un nuovo account di archiviazione e usarne uno esistente. Per informazioni su come recuperare segreti da Azure Key Vault e usarli come password nella distribuzione di modelli, vedere:

> [!div class="nextstepaction"]
> [Integrare Key Vault nella distribuzione di modelli](./resource-manager-tutorial-use-key-vault.md)
