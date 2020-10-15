---
title: Usare le informazioni di riferimento sui modelli
description: Usare le informazioni di riferimento sul modello di Azure Resource Manager per creare un modello.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 3709511fa8da0a40f4faf4ab2dac9505d69003ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118512"
---
# <a name="tutorial-utilize-the-resource-manager-template-reference"></a>Esercitazione: Usare le informazioni di riferimento sui modelli di Resource Manager

Questo articolo illustra come trovare le informazioni sullo schema dei modelli e usarle per creare modelli di Azure Resource Manager (ARM).

In questa esercitazione si usa un modello di base di Modelli di avvio rapido di Azure. Usando la documentazione di riferimento del modello, si personalizza il modello.

![Riferimento del modello di Resource Manager - Account di archiviazione per la distribuzione](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Comprendere il modello
> * Trovare le informazioni di riferimento sui modelli
> * Modificare il modello
> * Distribuire il modello

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione Strumenti di Resource Manager. Vedere [Avvio rapido: Creare modelli di Azure Resource Manager con Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

[Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) è un repository di modelli di Azure Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa guida introduttiva è denominato [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Creare un account di archiviazione Standard). Il modello definisce una risorsa account di archiviazione di Azure.

1. In Visual Studio Code selezionare **File**>**Apri file**.
1. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Selezionare **Apri** per aprire il file.
1. Selezionare **File**>**Salva con nome** per salvare il file con il nome **azuredeploy.json** nel computer locale.

## <a name="understand-the-schema"></a>Informazioni sullo schema

1. Da Visual Studio Code comprimere il modello a livello di radice. Verrà visualizzata la struttura più semplice, con gli elementi seguenti:

    ![Struttura più semplice del modello di Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: specificare il percorso del file di schema JSON che descrive la versione del linguaggio del modello.
    * **contentVersion**: specificare qualsiasi valore per questo elemento per documentare le modifiche significative nel modello.
    * **parameters**: specificare i valori forniti durante la distribuzione per personalizzare la distribuzione di risorse.
    * **variables**: specificare i valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello.
    * **resources**: specificare i tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse.
    * **outputs**: specificare i valori restituiti dopo la distribuzione.

1. Espandere **resources**, in cui è definita una risorsa `Microsoft.Storage/storageAccounts`. Il nome dello SKU usa un valore di parametro.  Il parametro è denominato **storageAccountType**.

    ![Definizione di un account di archiviazione nel modello di Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Espandere **parametri** per visualizzare la definizione di **storageAccountType**. Il parametro prevede quattro valori consentiti. È possibile trovare gli altri valori consentiti, quindi rivedere la definizione del parametro.

    ![SKU delle risorse account di archiviazione del modello di Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Trovare le informazioni di riferimento sui modelli

1. Passare a [Informazioni di riferimento sui modelli di Azure](/azure/templates/).
1. Nella casella **Filtra per titolo** immettere **account di archiviazione** e selezionare il primo **Account di archiviazione** in **Riferimento > Archiviazione**.

    ![Account di archiviazione di riferimento al modello di Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Un provider di risorse offre in genere diverse versioni dell'API:

    ![Riferimento del modello di Resource Manager - Versioni dell'account di archiviazione](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Nel riquadro sinistro selezionare **Tutte le risorse** in **Archiviazione**. In questa pagina sono elencati i tipi di risorse e le versioni del provider di risorse di archiviazione. È consigliabile usare le versioni più recenti dell'API per i tipi di risorse definiti nel modello.

    ![Riferimento del modello di Resource Manager - Versioni dei tipi di account di archiviazione](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Selezionare la versione più recente del tipo di risorsa **storageAccount**. Alla data della pubblicazione di questo articolo, la versione più recente è la versione **2019-06-01**. Verificare che la versione corrisponda a quella usata per la risorsa account di archiviazione nel modello. Se si aggiorna la versione dell'API, verificare che la definizione della risorsa corrisponda alle informazioni di riferimento sul modello.

1. Questa pagina elenca i dettagli del tipo di risorsa storageAccount.  Elenca ad esempio i valori consentiti per l'oggetto **SKU**. Sono presenti più SKU rispetto a quelli elencati nel modello di avvio rapido aperto in precedenza. È possibile personalizzare il modello di avvio rapido per includere tutti i tipi di archiviazione disponibili.

    ![Riferimento del modello di Resource Manager - SKU dell'account di archiviazione](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Modificare il modello

Da Visual Studio Code aggiungere i tipi di account di archiviazione aggiuntivi, come illustrato nello screenshot seguente:

![Riferimento del modello di Resource Manager - Risorse dell'account di archiviazione](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Distribuire il modello

1. Accedere ad [Azure Cloud Shell](https://shell.azure.com)

1. Scegliere l'ambiente preferito selezionando **PowerShell** o **Bash** (per l'interfaccia della riga di comando) nell'angolo in alto a sinistra.  Quando si cambia interfaccia, è necessario riavviare la shell.

    ![Caricare file in Cloud Shell nel portale di Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selezionare **Carica/Scarica file** e quindi **Carica**. Vedere l'immagine sopra riportata. Selezionare il file salvato nella sezione precedente. Dopo aver caricato il file, è possibile usare i comandi **ls** e **cat** per verificare che il file sia stato caricato.

1. In Cloud Shell eseguire i comandi riportati sotto. Selezionare la scheda per visualizzare il codice di PowerShell o il codice dell'interfaccia della riga di comando.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

 Quando si distribuisce il modello, specificare il parametro **storageAccountType** con un valore appena aggiunto, ad esempio **Standard_RAGRS**. La distribuzione non riesce se si usa il modello di avvio rapido originale perché in quel caso **Standard_RAGRS** non è un valore consentito.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come usare le informazioni di riferimento sui modelli per personalizzare un modello esistente. Per informazioni su come creare più istanze dell'account di archiviazione, vedere:

> [!div class="nextstepaction"]
> [Creare più istanze](./template-tutorial-create-multiple-instances.md)
