---
title: Creare modelli collegati di Azure Resource Manager | Microsoft Docs
description: Informazioni su come creare modelli di Azure Resource Manager collegati per la creazione di macchine virtuali
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/18/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: de2e848bd587f3b9bf2efe3fa8df3710e24243e4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241390"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Esercitazione: Creare modelli collegati di Azure Resource Manager

Informazioni su come creare modelli collegati di Azure Resource Manager. Utilizzando modelli collegati, è possibile avere un modello che chiama un altro modello. È utile per la modularizzazione del modelli. In questa esercitazione si usa lo stesso modello di [Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](./resource-manager-tutorial-create-templates-with-dependent-resources.md), operazione che crea una macchina virtuale, una rete virtuale e altre risorse dipendenti, tra cui un account di archiviazione. Separare la creazione della risorsa account dell'account di archiviazione da un modello collegato.

La chiamata di un modello collegato è simile a una chiamata di funzione.  Verrà anche illustrato come passare i valori dei parametri al modello collegato e come ottenere i "valori restituiti" da tale modello.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Creazione del modello collegato
> * Caricamento del modello collegato
> * Collegamento al modello collegato
> * Configurare le dipendenze
> * Distribuire il modello
> * Procedure consigliate aggiuntive

Per altre informazioni, vedere [Uso di modelli collegati e annidati nella distribuzione di risorse di Azure](./resource-group-linked-templates.md).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) con l'[estensione Strumenti di Azure Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Di seguito è riportato un esempio della generazione di una password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault in Distribuzione modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Si tratta dello stesso modello di [Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Si salvano due copie dello stesso modello da utilizzare come:

* **Il modello principale**: creare tutte le risorse, ad eccezione dell'account di archiviazione.
* **Il modello collegato**: creare l'account di archiviazione.

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Sono presenti cinque risorse definite dal modello:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Prima di personalizzare il modello è utile acquisire una conoscenza di base del relativo schema.
5. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.
6. Selezionare **File**>**Salva con nome** per creare un'altra copia del file con il nome **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Creazione del modello collegato

Il modello collegato crea un account di archiviazione. Il modello collegato può essere usato come un modello autonomo per creare un account di archiviazione. In questa esercitazione, il modello collegato accetta due parametri e passa quindi un valore al modello principale. Questo valore "restituito" viene definito nell'elemento `outputs`.

1. Aprire **linkedTemplate.json** in Visual Studio Code, se il file non è aperto.
2. Apportare le modifiche seguenti:

    * Rimuovere tutti i parametri tranne **location**.
    * Aggiungere un parametro denominato **storageAccountName**.
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        Il nome e la località dell'account di archiviazione vengono passati dal modello principale al modello collegato come parametri.

    * Rimuovere l'elemento **variables** e tutte le definizioni delle variabili.
    * Rimuovere tutte le risorse tranne l'account di archiviazione. Si rimuove un totale di quattro risorse.
    * Aggiornare il valore dell'elemento **name** della risorsa dell'account di archiviazione a:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Aggiornare l'elemento **output**, in modo che sia simile a:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```
       **storageUri** è richiesto dalla definizione della risorsa della macchina virtuale nel modello principale.  Si ripassa il valore al modello principale come un valore di output.

        Al termine, il modello è simile a:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```
3. Salvare le modifiche.

## <a name="upload-the-linked-template"></a>Caricamento del modello collegato

Il modello principale e quello collegato devono essere accessibili dalla posizione in cui si esegue la distribuzione. In questa esercitazione si usa il metodo di distribuzione di Cloud Shell come nell'[esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Nella shell viene caricato il modello principale (azuredeploy.json). Il modello collegato (linkedTemplate.json) deve essere condiviso in modo sicuro da qualche parte. Lo script di PowerShell seguente crea un account di archiviazione di Azure, carica il modello nell'account di archiviazione e quindi genera un token di firma di accesso condiviso per concedere accesso limitato al file del modello. Per semplificare l'esercitazione, lo script scarica un modello collegato completato da un percorso condiviso. Se si desidera usare il modello collegato creato, è possibile usare [Cloud Shell](https://shell.azure.com) per caricare il modello collegato e quindi modificare lo script per usarlo.

> [!NOTE]
> Lo script limita l'uso del token di firma di accesso condiviso a otto ore. Se è necessario più tempo per completare questa esercitazione, aumentare il tempo di scadenza.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Selezionare il pulsante verde **Prova** per aprire il riquadro di Azure Cloud Shell.
2. Selezionare **Copia** per copiare lo script di PowerShell.
3. Fare doppio clic su un punto qualsiasi all'interno del riquadro di shell, ovvero la parte di colore blu marino, quindi selezionare **Incolla**.
4. Prendere nota dei due valori, ovvero il nome del gruppo di risorse e l'URI del modello collegato, in fondo al riquadro della shell. Sarà necessario usare questi valori più avanti nell'esercitazione.
5. Selezionare **Esci dalla modalità messa a fuoco** per chiudere il riquadro della shell.

In pratica, si genera un token di firma di accesso condiviso quando si distribuisce il modello principale e si assegnare una finestra di scadenza più ridotta al token per renderlo più sicuro. Per ulteriori informazioni, vedere [Indicare un token di firma di accesso condiviso durante la distribuzione](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Chiamare il modello collegato

Il modello principale è denominato azuredeploy.json.

1. Aprire **azuredeploy.json** in Visual Studio Code, se non è aperto.
2. Eliminare la definizione della risorsa dell'account di archiviazione dal modello:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. Aggiungere il frammento json seguente alla posizione in cui è presente la definizione dell'account di archiviazione:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Prestare attenzione ai seguenti dettagli:

    * Viene usata una risorsa `Microsoft.Resources/deployments` nel modello principale per collegarsi a un altro modello.
    * La risorsa `deployments` ha un nome denominato `linkedTemplate`. Questo nome viene usato per la [configurazione della dipendenza](#configure-dependency).
    * È possibile usare solo la modalità di distribuzione [Incrementale](./deployment-modes.md) quando si chiamano i modelli collegati.
    * `templateLink/uri` contiene l'URI del modello collegato. Aggiornare il valore all'URI che si ottiene quando si carica il modello collegato, ovvero quello con un token di firma di accesso condiviso.
    * Per passare i valori dal modello principale al modello collegato, usare `parameters`.
4. Assicurarsi di aver aggiornato il valore dell'elemento `uri` con quell ottenuto dopo aver caricato il modello collegato, ovvero il valore con token di firma di accesso condiviso. In pratica, si desidera fornire l'URI con un parametro.
5. Salvare il modello modificato

## <a name="configure-dependency"></a>Configurare le dipendenze

Fare riferimento a [Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](./resource-manager-tutorial-create-templates-with-dependent-resources.md), la risorsa di macchina virtuale varia a seconda dell'account di archiviazione:

![Diagramma delle dipendenze dei modelli di Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Poiché l'account di archiviazione viene ora definito nel modello collegato, è necessario aggiornare i seguenti due elementi della risorsa `Microsoft.Compute/virtualMachines`.

* Riconfigurare l'elemento `dependOn`. La definizione dell'account di archiviazione viene spostata nel modello collegato.
* Riconfigurare l'elemento `properties/diagnosticsProfile/bootDiagnostics/storageUri`. In [creare il modello collegato](#create-the-linked-template), è stato aggiunto un valore di output:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Questo valore viene richiesto dal modello principale.

1. Aprire azuredeploy.json in Visual Studio Code, se non è aperto.
2. Espandere la definizione della risorsa della macchina virtuale, aggiornare **dependsOn** come illustrato nello screenshot seguente:

    ![I modelli collegati di Azure Resource Manager configurano la dipendenza](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* è il nome della risorsa deployments.
3. Aggiornare **properties/diagnosticsProfile/bootDiagnostics/storageUri** come illustrato nello screenshot precedente.
4. Salvare il modello modificato.

## <a name="deploy-the-template"></a>Distribuire il modello

Vedere la sezione [Distribuire il modello](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) per la procedura di distribuzione. Usare lo stesso nome del gruppo di risorse dell'account di archiviazione usato per il modello collegato. Rende più semplice pulire le risorse nella sezione successiva. Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Vedere [Prerequisiti](#prerequisites).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="additional-practice"></a>Procedura consigliata aggiuntiva

Per migliorare il progetto, effettuare le modifiche aggiuntive seguenti al progetto completato:

1. Modificare il modello principale (azuredeploy.json) in modo che riceva il valore URI del modello collegato tramite un parametro.
2. Invece di generare un token di firma di accesso condiviso quando si carica il modello collegato, generare il token quando si distribuisce il modello principale. Per ulteriori informazioni, vedere [Indicare un token di firma di accesso condiviso durante la distribuzione](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, si è suddiviso un modello in un modello principale e un modello collegato. Per informazioni su come usare estensioni di macchina virtuale per eseguire le attività post-distribuzione, vedere:

> [!div class="nextstepaction"]
> [Distribuire estensioni di macchina virtuale](./resource-manager-tutorial-deploy-vm-extensions.md)
