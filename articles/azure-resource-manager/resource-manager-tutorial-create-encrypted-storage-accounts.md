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
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d48374d7919be3d141ea199e8238a220dbfe0332
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419526"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Esercitazione: Creare un modello di Azure Resource Manager per la distribuzione di un account di archiviazione crittografato

Come trovare informazioni per completare un modello di Azure Resource Manager.

In questa esercitazione verrà usato un modello di base dai modelli di avvio rapido di Azure per creare un account di archiviazione di Azure.  Usando la documentazione di riferimento per i modelli, personalizzare il modello di base in modo da creare un account di archiviazione crittografato.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Comprendere il modello
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

L'obiettivo di questa esercitazione consiste nel definire un modello per creare un account di archiviazione crittografato.  Il modello di esempio crea solo un account di archiviazione non crittografato di base. Per trovare la configurazione correlata alla crittografia, è possibile usare le informazioni di riferimento sui modelli dell'account di archiviazione di Azure.

1. Passare a [Modelli di Azure](https://docs.microsoft.com/azure/templates/).
2. In **Filtra per titolo**, immettere **account di archiviazione**.
3. Selezionare **Riferimento/Riferimento al modello/Archiviazione/ Account di archiviazione** come illustrato nello screenshot seguente:

    ![Account di archiviazione di riferimento al modello di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    resource-manager-template-resources-reference-storage-accounts
1. Esplorare le informazioni relative alla crittografia.  
1. All'interno dell'elemento properties della definizione di risorsa dell'account di archiviazione, aggiungere il codice JSON seguente:

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

In Visual Studio Code modificare il modello in modo che l'elemento finale delle risorse sia simile al seguente:

![Account di archiviazione crittografato del modello di Resource Manager - risorse](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Distribuire il modello

Vedere la sezione [Distribuire il modello](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) della guida introduttiva di Visual Studio Code per la procedura di distribuzione.

Lo screenshot seguente mostra il comando dell'interfaccia della riga di comando per elencare l'account di archiviazione appena creato, che indica che la crittografia è stata abilitata per l'archiviazione BLOB.

![Account di archiviazione crittografato di Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come usare le informazioni di riferimento sui modelli per personalizzare un modello esistente. Per informazioni su come creare più istanze dell'account di archiviazione, vedere:

> [!div class="nextstepaction"]
> [Creare più istanze](./resource-manager-tutorial-create-multiple-instances.md)
