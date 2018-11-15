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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6c75c0ce7d12f65878ec8cde0f265022c7beeb29
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613512"
---
# <a name="tutorial-deploy-an-encrypted-azure-storage-account-with-resource-manager-template"></a>Esercitazione: Distribuire un account di archiviazione di Azure crittografato con un modello di Resource Manager

Questo articolo illustra come trovare le informazioni sullo schema dei modelli e usarle per creare modelli di Azure Resource Manager.

In questa esercitazione si usa un modello di base di Modelli di avvio rapido di Azure. Usando la documentazione di riferimento sui modelli, si personalizza il modello in modo da creare un account di archiviazione crittografato.

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

* [Visual Studio Code](https://code.visualstudio.com/) con l'[estensione Strumenti di Azure Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

[Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa guida introduttiva è denominato [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Creare un account di archiviazione Standard). Il modello definisce una risorsa account di archiviazione di Azure.

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Selezionare **File**>**Salva con nome** per salvare il file con il nome **azuredeploy.json** nel computer locale.

## <a name="understand-the-schema"></a>Informazioni sullo schema

1. Da Visual Studio Code comprimere il modello a livello di radice. Verrà visualizzata la struttura più semplice, con gli elementi seguenti:

    ![Struttura più semplice del modello di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: specificare il percorso del file di schema JSON che descrive la versione del linguaggio del modello.
    * **contentVersion**: specificare qualsiasi valore per questo elemento per documentare le modifiche significative nel modello.
    * **parameters**: specificare i valori forniti durante la distribuzione per personalizzare la distribuzione di risorse.
    * **variables**: specificare i valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello.
    * **resources**: specificare i tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse.
    * **outputs**: specificare i valori restituiti dopo la distribuzione.

2. Espandere **resources**, in cui è definita una risorsa `Microsoft.Storage/storageAccounts`. Il modello crea un account di archiviazione non crittografato.

    ![Definizione di un account di archiviazione nel modello di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Trovare le informazioni di riferimento sui modelli

1. Passare a [Modelli di Azure](https://docs.microsoft.com/azure/templates/).
2. In **Filtra per titolo**, immettere **account di archiviazione**.
3. Selezionare **Riferimento/Riferimento al modello/Archiviazione/ Account di archiviazione** come illustrato nello screenshot seguente:

    ![Account di archiviazione di riferimento al modello di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

4. Trovare le informazioni della definizione correlate alla crittografia.  

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Nella stessa pagina Web, la descrizione seguente conferma che l'oggetto `encryption` consente di creare un account di archiviazione crittografato.

    ![Crittografia dell'account di archiviazione nelle informazioni di riferimento sui modelli di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    Per gestire la chiave di crittografia sono disponibili due modi. È possibile usare con Crittografia del servizio di archiviazione le chiavi di crittografia gestite da Microsoft oppure le proprie chiavi di crittografia. Per semplificare l'esercitazione si usa l'opzione `Microsoft.Storage` in modo da non dover creare un'istanza di Azure Key Vault.

    ![Oggetto encryption per l'account di archiviazione nelle informazioni di riferimento sui modelli di Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    L'oggetto encryption si presenterà come segue:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Modificare il modello

In Visual Studio Code modificare il modello in modo che l'elemento resources si presenti come segue:

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
