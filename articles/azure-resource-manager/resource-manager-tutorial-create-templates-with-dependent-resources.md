---
title: Creare modelli di Azure Resource Manager con risorse dipendenti | Microsoft Docs
description: Informazioni su come creare un modello di Azure Resource Manager con più risorse e come eseguirne la distribuzione tramite il portale di Azure
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bd559cb9f0140706a4b9735c642367e03616a14d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188166"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti

Informazioni su come creare un modello di Azure Resource Manager per distribuire più risorse.  Dopo aver creato il modello, è possibile distribuire il modello usando Cloud Shell dal portale di Azure.

Alcune delle risorse non possono essere distribuite finché non esiste un'altra risorsa. Ad esempio, non è possibile creare la macchina virtuale finché non esistono la relativa interfaccia di rete e l'account di archiviazione. Per definire questa relazione, si contrassegna una risorsa come dipendente dalle altre. Resource Manager valuta le dipendenze tra le risorse e le distribuisce in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, Resource Manager le distribuisce in parallelo. Per altre informazioni, leggere [Definire l'ordine per la distribuzione delle risorse nei modelli di Azure Resource Manager](./resource-group-define-dependencies.md).

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Esplorare il modello
> * Distribuire il modello
> * Pulire le risorse

Le istruzioni riportate in questa esercitazione consentono di creare una macchina virtuale, una rete virtuale e alcune altre risorse dipendenti. 

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/).
* Estensione Strumenti di Azure Resource Manager.  Vedere [Installare l'estensione ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="explore-the-template"></a>Esplorare il modello

1. Da Visual Studio Code, comprimere gli elementi finché non vengono visualizzati solo gli elementi di primo livello e gli elementi di secondo livello all'interno di **resources**:

    ![Modelli di Azure Resource Manager in Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Sono presenti cinque risorse definite dal modello.
2. Espandere il quarto elemento:

    ![Modelli di Azure Resource Manager in Visual Studio Code - dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    L'elemento dependsOn consente di definire una risorsa come dipendente da una o più risorse. In questo esempio, questa risorsa è una networkInterface.  Dipende da altre due risorse:

    * publicIPAddress
    * virtualNetwork

3. Espandere il quinto elemento. Questa risorsa è una macchina virtuale. Dipende da altre due risorse:

    * storageAccount
    * networkInterface

Il diagramma seguente illustra le risorse e le informazioni sulle dipendenze per questo modello:

![Modelli di Azure Resource Manager in Visual Studio Code - diagramma delle dipendenze](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Specificando le dipendenze, Resource Manager distribuisce in modo efficiente la soluzione. Distribuisce l'account di archiviazione, l'indirizzo IP pubblico e la rete virtuale in parallelo poiché non hanno dipendenze. Dopo aver distribuito l'indirizzo IP pubblico e la rete virtuale, viene creata l'interfaccia di rete. Quando sono state distribuite tutte le altre risorse, Resource Manager distribuisce la macchina virtuale.

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire i modelli sono disponibili molti metodi.  In questa esercitazione si usa Cloud Shell dal portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com)
2. Selezionare **Cloud Shell** nell'angolo superiore destro come illustrato nell'immagine seguente:

    ![Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. Selezionare **PowerShell** nell'angolo superiore sinistro di Cloud Shell.  In questa esercitazione verrà usato PowerShell.
4. Selezionare **Riavvia**
5. Selezionare **Caricare file** da Cloud Shell:

    ![Caricare file in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. Selezionare il file salvato in precedenza in questa esercitazione. Il nome predefinito è **azuredeploy.json**.  Se è presente un file con lo stesso nome, il file precedente verrà sovrascritto senza alcuna notifica.
7. In Cloud Shell eseguire il comando seguente per verificare che il file sia stato caricato. 

    ```shell
    ls
    ```

    ![Elencare file in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Il nome del file visualizzato nello screenshot è azuredeploy.json.

8. In Cloud Shell eseguire il comando seguente per verificare che il contenuto del file JSON:

    ```shell
    cat azuredeploy.json
    ```
9. In Cloud Shell eseguire i comandi di PowerShell seguenti:

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    Di seguito è riportato lo screenshot di una distribuzione di esempio:

    ![Distribuire il modello in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    Nello screenshot vengono usati i valori riportati di seguito.

    * **$resourceGroupName**: myresourcegroup0710. 
    * **$location**: eastus2
    * **&lt;DeployName>**: mydeployment0710
    * **&lt;TemplateFile>**: azuredeploy.json
    * **Parametri del modello**:

        * **adminUsername**: JohnDole
        * **adminPassword**: Pass@word123
        * **dnsLabelPrefix**: myvm0710

10. Eseguire questo comando di PowerShell per visualizzare la macchina virtuale appena creata:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    Il nome della macchina virtuale è hardcoded come **SimpleWinVM** nel modello.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, verrà sviluppato e distribuito un modello per creare una macchina virtuale, una rete virtuale e le risorse dipendenti. Per altre informazioni sui modelli, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](./resource-group-authoring-templates.md).