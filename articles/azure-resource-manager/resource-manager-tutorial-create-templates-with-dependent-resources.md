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
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 486a13db9cf18cb44a063d37dde4a657f6dc625c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103668"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti

Informazioni su come creare un modello di Azure Resource Manager per distribuire più risorse e configurare l'ordine di distribuzione. Dopo aver creato il modello, è possibile distribuire il modello usando Cloud Shell dal portale di Azure.

In questa esercitazione si creano un account di archiviazione, una macchina virtuale, una rete virtuale e alcune altre risorse dipendenti. Alcune delle risorse non possono essere distribuite finché non esiste un'altra risorsa. Ad esempio, non è possibile creare la macchina virtuale finché non esistono la relativa interfaccia di rete e l'account di archiviazione. Per definire questa relazione, si contrassegna una risorsa come dipendente dalle altre. Resource Manager valuta le dipendenze tra le risorse e le distribuisce in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, Resource Manager le distribuisce in parallelo. Per altre informazioni, leggere [Definire l'ordine per la distribuzione delle risorse nei modelli di Azure Resource Manager](./resource-group-define-dependencies.md).

![modello di resource manager, distribuzione di risorse dipendenti, schema dell'ordine](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Esplorare il modello
> * Distribuire il modello

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) con l'estensione Strumenti di Azure Resource Manager.  Visualizzare [installare l'estensione ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Di seguito è riportato un esempio della generazione di una password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault in Distribuzione modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

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

Quando si esplora il modello in questa sezione, provare a rispondere alle domande seguenti:

* Quante risorse di Azure sono definite in questo modello?
* Una delle risorse è un account di archiviazione di Azure.  La definizione è simile a quella usata nell'ultima esercitazione?
* È possibile trovare i riferimenti del modello per le risorse definite in questo modello?
* È possibile trovare le dipendenze delle risorse?

1. Da Visual Studio Code, comprimere gli elementi finché non vengono visualizzati solo gli elementi di primo livello e gli elementi di secondo livello all'interno di **resources**:

    ![Modelli di Azure Resource Manager in Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Sono presenti cinque risorse definite dal modello:

   * `Microsoft.Storage/storageAccounts`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Prima di personalizzare il modello è utile acquisirne una conoscenza di base.

2. Espandere la prima risorsa. È un account di archiviazione. Confrontare la definizione della risorsa con le [informazioni di riferimento sui modelli](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).

    ![Modelli di Azure Resource Manager in Visual Studio Code - definizione dell'account di archiviazione](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Espandere la seconda risorsa. La risorsa è di tipo `Microsoft.Network/publicIPAddresses`. Confrontare la definizione della risorsa con le [informazioni di riferimento sui modelli](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).

    ![Modelli di Azure Resource Manager in Visual Studio Code - definizione dell'indirizzo IP pubblico](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Espandere la quarta risorsa. La risorsa è di tipo `Microsoft.Network/networkInterfaces`:  

    ![Modelli di Azure Resource Manager in Visual Studio Code - dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    L'elemento dependsOn consente di definire una risorsa come dipendente da una o più risorse. La risorsa dipende da altre due risorse:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Espandere la quinta risorsa. Questa risorsa è una macchina virtuale. Dipende da altre due risorse:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Il diagramma seguente illustra le risorse e le informazioni sulle dipendenze per questo modello:

![Modelli di Azure Resource Manager in Visual Studio Code - diagramma delle dipendenze](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Specificando le dipendenze, Resource Manager distribuisce in modo efficiente la soluzione. Distribuisce l'account di archiviazione, l'indirizzo IP pubblico e la rete virtuale in parallelo poiché non hanno dipendenze. Dopo aver distribuito l'indirizzo IP pubblico e la rete virtuale, viene creata l'interfaccia di rete. Quando sono state distribuite tutte le altre risorse, Resource Manager distribuisce la macchina virtuale.

## <a name="deploy-the-template"></a>Distribuire il modello

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per distribuire i modelli sono disponibili molti metodi.  In questa esercitazione si usa Cloud Shell dal portale di Azure.

1. Accedere a [Cloud Shell](https://shell.azure.com). 
2. Selezionare **PowerShell** nell'angolo superiore sinistro di Cloud Shell e quindi selezionare **Conferma**.  In questa esercitazione verrà usato PowerShell.
3. Selezionare **Caricare file** da Cloud Shell:

    ![Caricare file in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Selezionare il modello salvato in precedenza in questa esercitazione. Il nome predefinito è **azuredeploy.json**.  Se è presente un file con lo stesso nome, il file precedente viene sovrascritto senza alcuna notifica.

    È possibile usare i comandi **ls $HOME** e **cat $HOME/azuredeploy.json** per verificare che il file sia stato caricato correttamente. 

5. In Cloud Shell eseguire i comandi di PowerShell seguenti. Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Vedere [Prerequisiti](#prerequisites).

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"
    ```

8. Eseguire questo comando di PowerShell per visualizzare la macchina virtuale appena creata:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Il nome della macchina virtuale è hardcoded come **SimpleWinVM** nel modello.

9. Connettersi tramite RDP alla macchina virtuale per verificare che sia stata creata correttamente.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato sviluppato e distribuito un modello per creare una macchina virtuale, una rete virtuale e le risorse dipendenti. Per informazioni su come distribuire risorse di Azure in base a condizioni, vedere:

> [!div class="nextstepaction"]
> [Usare le condizioni](./resource-manager-tutorial-use-conditions.md)
