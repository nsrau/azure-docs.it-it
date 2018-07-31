---
title: Creare un modello di Azure Resource Manager per la distribuzione di un account di archiviazione crittografato | Microsoft Docs
description: Usare Visual Studio Code per creare un modello per la distribuzione di un account di archiviazione crittografato.
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
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188200"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Esercitazione: Creare un modello di Azure Resource Manager per la distribuzione di un account di archiviazione crittografato

Come trovare informazioni per completare un modello di Azure Resource Manager.

In questa esercitazione verrà usato un modello di base dai modelli di avvio rapido di Azure per creare un account di archiviazione di Azure.  Usando la documentazione di riferimento per i modelli, personalizzare il modello di base in modo da creare un account di archiviazione crittografato.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Comprendere il formato del modello
> * Usare i parametri nel modello
> * Usare le variabili nel modello
> * Modificare il modello
> * Distribuire il modello

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/).
* Estensione Strumenti di Azure Resource Manager. Per eseguire l'installazione, vedere [Installare l'estensione Strumenti di Azure Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Il modello usato in questa guida introduttiva è denominato [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Creare un account di archiviazione Standard). Il modello definisce una risorsa account di archiviazione di Azure.

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Selezionare **File**>**Salva con nome** per salvare il file con il nome **azuredeploy.json** nel computer locale.

## <a name="understand-the-format"></a>Comprendere il formato

Da Visual Studio Code comprimere il modello a livello di radice. Verrà visualizzata la struttura più semplice, con gli elementi seguenti:

![Struttura più semplice del modello di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: specificare il percorso del file di schema JSON che descrive la versione del linguaggio del modello.
* **contentVersion**: specificare qualsiasi valore per questo elemento per documentare le modifiche significative nel modello.
* **parameters**: specificare i valori forniti durante la distribuzione per personalizzare la distribuzione di risorse.
* **variables**: specificare i valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello.
* **resources**: specificare i tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse.
* **outputs**: specificare i valori restituiti dopo la distribuzione.

## <a name="use-parameters-in-template"></a>Usare i parametri nel modello

I parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente. I parametri definiti nel modello vengono usati quando si impostano i valori per l'account di archiviazione.

![Parametri del modello di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

In questo modello sono definiti due parametri. Si noti che viene usata una funzione di modello in location.defaultValue:

```json
"defaultValue": "[resourceGroup().location]",
```

La funzione resourceGroup() restituisce un oggetto che rappresenta il gruppo di risorse corrente. Per un elenco completo delle funzioni del modello, vedere [Funzioni del modello di Azure Resource Manager](./resource-group-template-functions.md).

Per usare i parametri definiti nel modello:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>Usare le variabili nel modello

Le variabili consentono di costruire valori da usare in tutto il modello. Grazie alle variabili è possibile ridurre la complessità dei modelli.

![Variabili del modello di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Questo modello definisce una variabile *storageAccountName*. Nella definizione vengono usate due funzioni di modello:

- **concat()**: concatena le stringhe. Per altre informazioni, vedere [concat](./resource-group-template-functions-string.md#concat).
- **uniqueString()**: crea una stringa hash deterministica in base ai valori forniti come parametri. Ogni account di archiviazione di Azure deve avere un nome univoco in Azure. Questa funzione fornisce una stringa univoca. Per altre funzioni di stringa, vedere [Funzioni di stringa](./resource-group-template-functions-string.md).

Per usare la variabile definita nel modello:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>Modificare il modello

Per trovare la configurazione dell'account di archiviazione correlata alla crittografia, è possibile usare le informazioni di riferimento sui modelli dell'account di archiviazione di Azure.

1. Passare a [Modelli di Azure](https://docs.microsoft.com/azure/templates/).
2. Dal sommario a sinistra selezionare **Riferimento**->**Archiviazione**->**Account di archiviazione**. La pagina contiene le informazioni per la definizione di un account di archiviazione.
3. Esplorare le informazioni relative alla crittografia.  
4. All'interno dell'elemento properties della definizione di risorsa dell'account di archiviazione, aggiungere il codice JSON seguente:

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    Questa parte abilita la funzione di crittografia del servizio di archiviazione BLOB.

L'aspetto finale dell'elemento resources è simile al seguente:

![Account di archiviazione crittografato del modello di Resource Manager - risorse](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire i modelli sono disponibili molti metodi.  In questa esercitazione si usa Cloud Shell dal portale di Azure. Cloud Shell supporta sia l'interfaccia della riga di comando di Azure che Azure PowerShell. Le istruzioni riportate di seguito riguardano l'interfaccia della riga di comando.

1. Accedere al [portale di Azure](https://portal.azure.com)
2. Selezionare **Cloud Shell** nell'angolo superiore destro come illustrato nell'immagine seguente:

    ![Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. Selezionare la freccia rivolta verso il basso e quindi selezionare **Bash** se non è Bash. In questa esercitazione si usa l'interfaccia della riga di comando di Azure.

    ![Interfaccia della riga di comando in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. Selezionare **Riavvia** per riavviare la shell.
5. Selezionare **Carica/Scarica file** e quindi **Carica**.

    ![Caricare file in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. Selezionare il file salvato in precedenza in questa esercitazione. Il nome predefinito è **azuredeploy.json**.
7. In Cloud Shell eseguire il comando **ls** per verificare che il file sia stato caricato. È anche possibile usare il comando **cat** per verificare il contenuto del modello.

    ![Elencare file in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. In Cloud Shell eseguire questi comandi:

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    Di seguito è riportato lo screenshot di una distribuzione di esempio:

    ![Distribuire il modello in Cloud Shell nel portale di Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    Nello screenshot vengono usati i valori riportati di seguito.

    * **&lt;ResourceGroupName>**: myresourcegroup0719. Sono presenti due occorrenze del parametro.  Assicurarsi di usare lo stesso valore.
    * **&lt;AzureLocation>**: eastus2
    * **&lt;DeployName>**: mydeployment0719
    * **&lt;TemplateFile>**: azuredeploy.json

    Nell'output dello screenshot, il nome dell'account di archiviazione è *fhqbfslikdqdsstandardsa*. 

9. Eseguire questo comando di PowerShell per visualizzare l'account di archiviazione appena creato:

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    Verrà visualizzato un output simile allo screenshot seguente, che indica che la crittografia è stata abilitata per l'archiviazione BLOB.

    ![Account di archiviazione crittografato di Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come usare le informazioni di riferimento sui modelli per personalizzare un modello esistente. Il modello usato in questa esercitazione contiene una sola risorsa di Azure.  Nell'esercitazione successiva verrà sviluppato un modello con più risorse,  alcune con risorse dipendenti.

> [!div class="nextstepaction"]
> [Creare più risorse](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
