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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e72a7b967ef314dcdcfb2773627ce3d020e2fdcb
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614226"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Esercitazione: Creare modelli collegati di Azure Resource Manager

Informazioni su come creare modelli collegati di Azure Resource Manager. Utilizzando modelli collegati, è possibile avere un modello che chiama un altro modello. È utile per la modularizzazione del modelli. In questa esercitazione si usa lo stesso modello usato nell'esercitazione [: creare più istanze di risorse usando i modelli di Resource Manager](./resource-manager-tutorial-create-multiple-instances.md), che crea una macchina virtuale, una rete virtuale e altre risorse dipendenti tra cui un account di archiviazione. Separare la risorsa account dell'account di archiviazione da un modello collegato.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Creazione del modello collegato
> * Caricamento del modello collegato
> * Collegamento al modello collegato
> * Configurare le dipendenze
> * Distribuire il modello

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

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Questo è lo stesso modello usato in [Esercitazione: creare più istanze di risorse usando i modelli di Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). Si salvano due copie dello stesso modello da utilizzare come:

* **Il modello principale**: creare tutte le risorse, ad eccezione dell'account di archiviazione.
* **Il modello collegato**: creare l'account di archiviazione.

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
6. Selezionare **File**>**Salva con nome** per creare un'altra copia del file con il nome **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Creazione del modello collegato

Il modello collegato crea un account di archiviazione. Il modello collegato è quasi identico al modello autonomo che crea un account di archiviazione. In questa esercitazione, il modello collegato deve ripassare un valore al modello principale. Questo valore viene definito nell'elemento `outputs`.

1. Aprire linkedTemplate.json in Visual Studio Code, se non è aperto.
2. Apportare le modifiche seguenti:

    * Rimuovere tutte le risorse, ad eccezione dell'account di archiviazione. Si rimuove un totale di quattro risorse.
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
    * Rimuovere i parametri che non vengano mai utilizzati. Questi parametri hanno una riga ondulata verde sottostante. Deve rimanere un solo parametro denominato **posizione**.
    * Rimuovere l'elemento **variabili**. Non sono necessarie in questa esercitazione.
    * Aggiungere un parametro denominato **storageAccountName**. Il nome dell'account di archiviazione viene passato dal modello principale al modello collegato come parametro.

    Al termine, il modello è simile a:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
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
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
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

I modelli devono essere accessibili dalla posizione in cui si esegue la distribuzione. Questa posizione potrebbe essere un account di archiviazione di Azure, Github o Dropbox. Se i modelli contengono delle informazioni riservate, assicurarsi di proteggere l'accesso a tali informazioni. In questa esercitazione si utilizza il metodo di distribuzione di Cloud shell come è accaduto anche nell'[Esercitazione: creare più istanze di risorse usando i modelli di Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). Nella shell viene caricato il modello principale (azuredeploy.json). Il modello collegato (linkedTemplate.json) deve essere condiviso da qualche parte.  Per ridurre le attività di questa esercitazione, il modello collegato definito nella sezione precedente è stato caricato in [un account di archiviazione di Azure](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json).

## <a name="call-the-linked-template"></a>Chiamare il modello collegato

Il modello principale è denominato azuredeploy.json.

1. Aprire azuredeploy.json in Visual Studio Code, se non è aperto.
2. Eliminare la definizione della risorsa dell'account di archiviazione dal modello.
3. Aggiungere il frammento json seguente alla posizione in cui è presente la definizione dell'account di archiviazione:

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
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
    * `templateLink/uri` contiene l'URI del modello collegato. Il modello collegato è stato caricato in un account di archiviazione condiviso. Se si carica il modello di un'altra posizione su Internet, è possibile aggiornare l'URI.
    * Per passare i valori dal modello principale al modello collegato, usare `parameters`.
4. Salvare le modifiche.

## <a name="configure-dependency"></a>Configurare le dipendenze

Rivedere in [Esercitazione: creare più istanze di risorse usando il modello di Resource Manager](./resource-manager-tutorial-create-multiple-instances.md), la risorsa della macchina virtuale varia a seconda dell'account di archiviazione:

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

    ![I modelli collegati di Azure Resource Manager configurano la dipendenza ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* è il nome della risorsa deployments.  
3. aggiornare **properties/diagnosticsProfile/bootDiagnostics/storageUri** come illustrato nello screenshot precedente.

Per altre informazioni, vedere [Usare modelli collegati e annidati nella distribuzione di risorse di Azure](./resource-group-linked-templates.md)

## <a name="deploy-the-template"></a>Distribuire il modello

Vedere la sezione [Distribuire il modello](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) per la procedura di distribuzione. Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Vedere [Prerequisiti](#prerequisites).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato sviluppato e distribuito un modello collegato. Per informazioni su come usare estensioni di macchina virtuale per eseguire le attività post-distribuzione, vedere:

> [!div class="nextstepaction"]
> [Distribuire estensioni di macchina virtuale](./deployment-manager-tutorial.md)
