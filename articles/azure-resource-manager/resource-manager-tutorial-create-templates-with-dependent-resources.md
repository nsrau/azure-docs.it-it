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
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114313"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti

Informazioni su come creare un modello di Azure Resource Manager per distribuire più risorse.  Dopo aver creato il modello, è possibile distribuire il modello usando Cloud Shell dal portale di Azure.

In questa esercitazione si creano un account di archiviazione, una macchina virtuale, una rete virtuale e alcune altre risorse dipendenti. Alcune delle risorse non possono essere distribuite finché non esiste un'altra risorsa. Ad esempio, non è possibile creare la macchina virtuale finché non esistono la relativa interfaccia di rete e l'account di archiviazione. Per definire questa relazione, si contrassegna una risorsa come dipendente dalle altre. Resource Manager valuta le dipendenze tra le risorse e le distribuisce in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, Resource Manager le distribuisce in parallelo. Per altre informazioni, leggere [Definire l'ordine per la distribuzione delle risorse nei modelli di Azure Resource Manager](./resource-group-define-dependencies.md).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare Key Vault
> * Aprire un modello di avvio rapido
> * Esplorare il modello
> * Modificare il file dei parametri
> * Distribuire il modello

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) con l'estensione Strumenti di Azure Resource Manager.  Vedere [Installare l'estensione ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="prepare-key-vault"></a>Preparare Key Vault

Per impedire gli attacchi password spraying, è consigliabile usare una password generata automaticamente per l'account amministratore della macchina virtuale e usare Key Vault per archiviare la password. La procedura seguente crea un insieme di credenziali delle chiavi e un segreto per archiviare la password. Configura anche le autorizzazioni necessarie alla distribuzione del modello per accedere al segreto archiviato nell'insieme di credenziali delle chiavi. Se l'insieme di credenziali delle chiavi è in un'altra sottoscrizione di Azure, sono necessari criteri di accesso aggiuntivi. Per informazioni dettagliate, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](./resource-manager-keyvault-parameter.md).

1. Accedere ad [Azure Cloud Shell](https://shell.azure.com).
2. Passare all'ambiente preferito, **PowerShell** o **Bash**, nell'angolo superiore sinistro.
3. Eseguire il comando seguente di Azure PowerShell o dell'interfaccia della riga di comando di Azure.  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. Annotare i valori di output perché sarà necessario usarli più avanti nell'esercitazione

> [!NOTE]
> Ogni servizio di Azure presenta requisiti specifici in termini di password. Ad esempio, i requisiti della macchina virtuale di Azure sono reperibili in Quali requisiti devono avere le password durante la creazione di una macchina virtuale?

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.
5. Ripetere i passaggi da 1 a 4 per aprire **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json** e quindi salvare il file con il nome **azuredeploy.parameters.json**.

## <a name="explore-the-template"></a>Esplorare il modello

Quando si esplora il modello in questa sezione, provare a rispondere alle domande seguenti:

- Quante risorse di Azure sono definite in questo modello?
- Una delle risorse è un account di archiviazione di Azure.  La definizione è simile a quella usata nell'ultima esercitazione?
- È possibile trovare i riferimenti del modello per le risorse definite in questo modello?
- È possibile trovare le dipendenze delle risorse?

1. Da Visual Studio Code, comprimere gli elementi finché non vengono visualizzati solo gli elementi di primo livello e gli elementi di secondo livello all'interno di **resources**:

    ![Modelli di Azure Resource Manager in Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Sono presenti cinque risorse definite dal modello.
2. Espandere la prima risorsa. È un account di archiviazione. La definizione deve essere identica a quella usata all'inizio dell'ultima esercitazione.

    ![Modelli di Azure Resource Manager in Visual Studio Code - definizione dell'account di archiviazione](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Espandere la seconda risorsa. Il tipo di risorsa è **Microsoft.Network/publicIPAddresses**. Per trovare il riferimento del modello, selezionare il [riferimento del modello](https://docs.microsoft.com/azure/templates/) e immettere l'**indirizzo IP pubblico** o gli **indirizzi IP pubblici** nel campo **Filtra per titolo**. Confrontare la definizione della risorsa con il riferimento del modello.

    ![Modelli di Azure Resource Manager in Visual Studio Code - definizione dell'indirizzo IP pubblico](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Ripetere l'ultimo passaggio per trovare i riferimenti del modello per le risorse definite in questo modello.  Confrontare le definizione delle risorse con i riferimenti.
5. Espandere la quarta risorsa:

    ![Modelli di Azure Resource Manager in Visual Studio Code - dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    L'elemento dependsOn consente di definire una risorsa come dipendente da una o più risorse. In questo esempio, questa risorsa è una networkInterface.  Dipende da altre due risorse:

    * publicIPAddress
    * virtualNetwork

6. Espandere la quinta risorsa. Questa risorsa è una macchina virtuale. Dipende da altre due risorse:

    * storageAccount
    * networkInterface

Il diagramma seguente illustra le risorse e le informazioni sulle dipendenze per questo modello:

![Modelli di Azure Resource Manager in Visual Studio Code - diagramma delle dipendenze](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Specificando le dipendenze, Resource Manager distribuisce in modo efficiente la soluzione. Distribuisce l'account di archiviazione, l'indirizzo IP pubblico e la rete virtuale in parallelo poiché non hanno dipendenze. Dopo aver distribuito l'indirizzo IP pubblico e la rete virtuale, viene creata l'interfaccia di rete. Quando sono state distribuite tutte le altre risorse, Resource Manager distribuisce la macchina virtuale.

## <a name="edit-the-parameters-file"></a>Modificare il file dei parametri

Non è necessario apportare modifiche al file del modello. È però necessario modificare il file dei parametri per recuperare la password dell'amministratore dall'insieme di credenziali delle chiavi.

1. Aprire **azuredeploy.parameters.json** in Visual Studio Code, se non è aperto.
2. Aggiornare il parametro **adminPassword** nel modo seguente:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    Sostituire **id** con l'ID risorsa dell'istanza di Key Vault creata nella procedura precedente. È uno degli output. 

    ![Integrare Key Vault e i modelli di Resource Manager - File dei parametri per la distribuzione di macchine virtuali](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Specificare i valori dei parametri seguenti.

    - **adminUsername**: assegnare un nome all'account amministratore della macchina virtuale.
    - **dnsLabelPrefix**: assegnare un nome al prefisso dell'etichetta DNS.
4. Salvare le modifiche.

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire i modelli sono disponibili molti metodi.  In questa esercitazione si usa Cloud Shell dal portale di Azure.

1. Accedere a [Cloud Shell](https://shell.azure.com). È anche possibile accedere al [portale di Azure](https://portal.azure.com) e quindi selezionare **Cloud Shell** nell'angolo superiore destro come illustrato nell'immagine seguente:

    ![Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. Selezionare **PowerShell** nell'angolo superiore sinistro di Cloud Shell e quindi selezionare **Conferma**.  In questa esercitazione verrà usato PowerShell.
3. Selezionare **Caricare file** da Cloud Shell:

    ![Caricare file in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Selezionare i file salvati in precedenza in questa esercitazione. I nomi predefiniti sono **azuredeploy.json** e **azuredeploy.paraemters.json**.  Se sono presenti file con gli stessi nomi, i file precedenti verranno sovrascritti senza alcuna notifica.
5. In Cloud Shell eseguire il comando seguente per verificare che il file sia stato caricato. 

    ```bash
    ls
    ```

    ![Elencare file in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Il nome del file visualizzato nello screenshot è azuredeploy.json.

6. In Cloud Shell eseguire il comando seguente per verificare che il contenuto del file JSON:

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. In Cloud Shell eseguire i comandi di PowerShell seguenti. Lo script di esempio usa lo stesso gruppo di risorse creato per l'insieme di credenziali delle chiavi. L'uso dello stesso gruppo di risorse semplifica la pulizia delle risorse.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. Eseguire questo comando di PowerShell per visualizzare la macchina virtuale appena creata:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Il nome della macchina virtuale è hardcoded come **SimpleWinVM** nel modello.

9. Accedere alla macchina virtuale per testare le credenziali dell'amministratore. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, verrà sviluppato e distribuito un modello per creare una macchina virtuale, una rete virtuale e le risorse dipendenti. Per informazioni su come distribuire risorse di Azure in base a condizioni, vedere:


> [!div class="nextstepaction"]
> [Usare le condizioni](./resource-manager-tutorial-use-conditions.md)

