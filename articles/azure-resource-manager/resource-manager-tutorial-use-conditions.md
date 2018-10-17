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
ms.date: 10/02/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 216e474f519e57352b017dc3e6bcdd74d48b03de
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238647"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Esercitazione: Usare una condizione nei modelli di Azure Resource Manager

Informazioni su come distribuire risorse di Azure in base a condizioni. 

Lo scenario usato in questa esercitazione è simile a quello usato in [Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](./resource-manager-tutorial-create-templates-with-dependent-resources.md), in cui si creano un account di archiviazione, una macchina virtuale, una rete virtuale e alcune altre risorse dipendenti. Invece di creare un nuovo account di archiviazione, consentire agli utenti di scegliere tra creare un nuovo account di archiviazione e usarne uno esistente. Per raggiungere questo obiettivo, si definisce un parametro aggiuntivo. Se il valore del parametro è "new", viene creato un nuovo account di archiviazione.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello
> * Pulire le risorse

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) con l'[estensione Strumenti di Azure Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="modify-the-template"></a>Modificare il modello

Apportare due modifiche al modello esistente:

* Aggiungere un parametro che consente di specificare il nome di un account di archiviazione. Questo parametro offre all'utente la possibilità di specificare il nome di un account di archiviazione esistente, ma può anche essere usato per il nome di un nuovo account di archiviazione.
* Aggiungere un nuovo parametro denominato **newOrExisting**. Questo parametro viene usato nella distribuzione per determinare se creare un nuovo account di archiviazione oppure usarne uno esistente.

1. Aprire **azuredeploy.json** in Visual Studio Code.
2. Sostituire **variables('storageAccountName')** con **parameters('storageAccountName')** nell'intero modello.  Sono presenti tre occorrenze di **variables('storageAccountName')**.
3. Rimuovere la definizione di variabile seguente:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. Aggiungere i due parametri seguenti al modello:

    ```json
    "newOrExisting": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    ```
    La definizione aggiornata dei parametri si presenta come segue:

    ![Usare una condizione in Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Aggiungere la riga seguente all'inizio della definizione dell'account di archiviazione.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'yes')]",
    ```

    La condizione controlla il valore di un parametro denominato **newOrExisting**. Se il valore del parametro è **new**, con la distribuzione viene creato l'account di archiviazione.

    La definizione aggiornata dell'account di archiviazione si presenta come segue:

    ![Usare una condizione in Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)

6. Salvare le modifiche.

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello, seguire le istruzioni riportate in [Distribuire il modello](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template).

Quando si distribuisce il modello con Azure PowerShell, è necessario specificare un parametro aggiuntivo:

```powershell
$resourceGroupName = "<Enter the resource group name>"
$storageAccountName = "Enter the storage account name>"
$location = "<Enter the Azure location>"
$vmAdmin = "<Enter the admin username>"
$vmPassword = "<Enter the password>"
$dnsLabelPrefix = "<Enter the prefix>"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
$vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
    -dnsLabelPrefix $dnsLabelPrefix -storageAccountName $storageAccountName -newOrExisting "new"
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

In questa esercitazione si sviluppa un modello che consente agli utenti di scegliere tra creare un nuovo account di archiviazione e usarne uno esistente. La macchina virtuale creata in questa esercitazione richiede un nome utente e una password amministratore. Invece di passare la password durante la distribuzione, è possibile archiviare preventivamente la password con Azure Key Vault e recuperarla durante la distribuzione. Per informazioni su come recuperare segreti da Azure Key Vault e usarli nella distribuzione di modelli, vedere:

> [!div class="nextstepaction"]
> [Integrare Key Vault nella distribuzione di modelli](./resource-manager-tutorial-use-key-vault.md)
