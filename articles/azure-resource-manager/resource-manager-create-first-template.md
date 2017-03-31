---
title: Creare il primo modello di Azure Resource Manager | Microsoft Docs
description: Guida dettagliata alla creazione del primo modello di Azure Resource Manager. Illustra come usare le informazioni di riferimento sul modello di un account di archiviazione per creare il modello.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/21/2017
ms.topic: get-started-article
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f861f2ba0376f00ff21e03cbb81a04a266f38630
ms.lasthandoff: 03/22/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>Creare il primo modello di Azure Resource Manager
Questo argomento illustra la procedura per creare il primo modello di Azure Resource Manager. I modelli di Resource Manager sono file JSON che definiscono le risorse che è necessario distribuire per la soluzione. Per comprendere i concetti associati alla distribuzione e alla gestione delle soluzioni di Azure, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md). Se si vuole ottenere un modello per risorse esistenti, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).

Per creare e modificare i modelli, è necessario un editor JSON. [Visual Studio Code](https://code.visualstudio.com/) è un editor di codice, leggero, open source e multipiattaforma. Supporta la creazione e la modifica dei modelli di Resource Manager tramite un'estensione. In questo argomento si presuppone che venga usato Visual Studio Code. Se tuttavia si ha un altro editor JSON (ad esempio, Visual Studio), è possibile usare tale editor.

## <a name="get-vs-code-and-extension"></a>Ottenere Visual Studio Code e l'estensione
1. Se necessario, installare Visual Studio Code da [https://code.visualstudio.com/](https://code.visualstudio.com/).

2. Installare l'estensione [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) accedendo a Quick Open (Ctrl+P) ed eseguendo: 

   ```
   ext install msazurermtools.azurerm-vscode-tools
   ```

3. Riavviare Visual Studio Code quando richiesto per abilitare l'estensione.

## <a name="create-blank-template"></a>Creare un modello vuoto

Si inizierà con un modello vuoto che include solo le sezioni di base di un modello.

1. Creare un file. 

2. Copiare e incollare nel file la sintassi JSON seguente:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [  ],
     "outputs": {  }
   }
   ```

3. Salvare il file con il nome **azuredeploy.json**. 

## <a name="add-storage-account"></a>Aggiungere l’account di archiviazione
1. Per definire un account di archiviazione per la distribuzione, si aggiunge tale account alla sezione **resources** del modello. Per trovare i valori disponibili per l'account di archiviazione, vedere le [informazioni di riferimento sul modello degli account di archiviazione](/azure/templates/microsoft.storage/storageaccounts). Copiare il codice JSON visualizzato per l'account di archiviazione. 

3. Incollare tale codice JSON nella sezione **resources** del modello, come illustrato nell'esempio seguente: 

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [
       {
         "name": "string",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-05-01",
         "sku": {
           "name": "string"
         },
         "kind": "string",
         "location": "string",
         "tags": {},
         "properties": {
           "customDomain": {
             "name": "string",
             "useSubDomain": boolean
           },
           "encryption": {
             "services": {
               "blob": {
                 "enabled": boolean
               }
             },
             "keySource": "Microsoft.Storage"
           },
           "accessTier": "string"
         }
       }
     ],
     "outputs": {  }
   }
   ```

  L'esempio precedente include diversi valori segnaposto e alcune proprietà che potrebbero non essere necessarie nell'account di archiviazione in uso.

## <a name="set-values-for-storage-account"></a>Impostare i valori per l'account di archiviazione

È ora possibile impostare i valori per l'account di archiviazione. 

1. Vedere di nuovo le [informazioni di riferimento sul modello degli account di archiviazione](/azure/templates/microsoft.storage/storageaccounts) da cui si è copiato il codice JSON. Sono riportate diverse tabelle che descrivono le proprietà e indicano i valori disponibili. 

2. Si noti che nell'elemento **properties** sono elencate le proprietà **customDomain**, **encryption** e **accessTier** che non sono tuttavia obbligatorie. Questi valori potrebbero essere importanti per gli scenari dell'utente, ma per semplificare l'esempio verranno ora rimossi.

   ```json
   "resources": [
     {
       "name": "string",
       "type": "Microsoft.Storage/storageAccounts",
       "apiVersion": "2016-05-01",
       "sku": {
         "name": "string"
       },
       "kind": "string",
       "location": "string",
       "tags": {},
       "properties": {
       }
     }
   ],
   ```

3. Attualmente, l'elemento **kind** è impostato su un valore segnaposto ("string"). Visual Studio Code include diverse funzionalità che consentono di conoscere i valori da usare nel modello. Si noti che Visual Studio Code indica che questo valore non è valido. Se si passa il puntatore del mouse su "string", Visual Studio Code suggerisce che i valori validi per **kind** sono `Storage` e `BlobStorage`. 

   ![Visualizzare i valori suggeriti da Visual Studio Code](./media/resource-manager-create-first-template/vs-code-show-values.png)

   Per visualizzare i valori disponibili, eliminare i caratteri tra virgolette e premere **CTRL+BARRA SPAZIATRICE**. Selezionare **Storage** tra le opzioni disponibili.
  
   ![visualizzazione di IntelliSense](./media/resource-manager-create-first-template/intellisense.png)

   Se non si usa Visual Studio Code, vedere la pagina delle informazioni di riferimento sul modello degli account di archiviazione. Si noti che la descrizione elenca gli stessi valori validi. Impostare l'elemento su **Storage**.

   ```json
   "kind": "Storage",
   ```

Il modello si presenta ora come segue:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>Aggiungere una funzione di modello

Le funzioni all'interno del modello vengono usate per semplificarne la sintassi e per recuperare valori non disponibili al momento della distribuzione del modello. Per l'elenco completo delle funzioni di modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).

Per specificare che l'account di archiviazione viene distribuito nella stessa località del gruppo di risorse, impostare la proprietà **location** come segue:

```json
"location": "[resourceGroup().location]",
```

Anche in questo caso, Visual Studio Code assiste l'utente suggerendo le funzioni disponibili. 

![Visualizzare le funzioni](./media/resource-manager-create-first-template/show-functions.png)

Si noti che la funzione è racchiusa tra parentesi quadre. La funzione [resourceGroup](resource-group-template-functions.md#resourcegroup) restituisce un oggetto con una proprietà denominata `location`. Il gruppo di risorse contiene tutte le risorse correlate per la soluzione. È possibile impostare la proprietà location come hardcoded con un valore come "Central US", ma per la ridistribuzione in una località diversa sarebbe necessario modificare manualmente il modello. L'uso della funzione `resourceGroup` semplifica la ridistribuzione del modello in un diverso gruppo di risorse in una diversa località.

Il modello si presenta ora come segue:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-parameters-and-variables"></a>Aggiungere parametri e variabili
Nel modello restano da impostare solo due valori: **name** e **sku.name**. Per queste proprietà si aggiungono parametri che consentono di personalizzare i valori durante la distribuzione. 

I nomi degli account di archiviazione sono soggetti a diverse restrizioni che ne complicano l'impostazione. Il nome deve essere di lunghezza compresa tra 3 e 24 caratteri, contenere solo numeri e lettere minuscole ed essere univoco. Invece di provare a indovinare un valore univoco corrispondente alle restrizioni, usare la funzione [uniqueString](resource-group-template-functions.md#uniquestring) per generare un valore hash. Per rendere il valore hash più significativo, aggiungere un prefisso che consenta di identificarlo come account di archiviazione dopo la distribuzione. 

1. Per passare un prefisso per il nome corrispondente alle proprie convenzioni di denominazione, andare alla sezione **parameters** del modello. Aggiungere al modello un parametro che accetta un prefisso per il nome dell'account di archiviazione:

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     }
   },
   ```

  Il prefisso è limitato a un massimo di 11 caratteri, perché `uniqueString` restituisce 13 caratteri e il nome non può superare i 24 caratteri. Se non si passa un valore per il parametro durante la distribuzione, viene usato il valore predefinito.

2. Andare alla sezione **variables** del modello. Per costruire il nome dal prefisso e dalla stringa univoca, aggiungere la variabile seguente:

   ```json
   "variables": {
     "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
   },
   ```

3. Nella sezione **resources** impostare il nome dell'account di archiviazione su tale variabile.

   ```json
   "name": "[variables('storageName')]",
   ```

3. Per offrire la possibilità di passare diversi SKU per l'account di archiviazione, andare alla sezione **parameters**. Dopo il parametro per il prefisso del nome dell'account di archiviazione, aggiungere un parametro che specifica i valori di SKU consentiti e un valore predefinito. È possibile trovare i valori consentiti nella pagina delle informazioni di riferimento sul modello o in Visual Studio Code. Nell'esempio seguente si includono tutti i valori validi per SKU. È tuttavia possibile limitare i valori consentiti ai soli tipi di SKU che si vogliono distribuire tramite il modello.

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     },
     "storageSKU": {
       "type": "string",
       "allowedValues": [
         "Standard_LRS",
         "Standard_ZRS",
         "Standard_GRS",
         "Standard_RAGRS",
         "Premium_LRS"
       ],
       "defaultValue": "Standard_LRS",
       "metadata": {
         "description": "The type of replication to use for the storage account."
       }
     }
   },
   ```

3. Modificare la proprietà SKU per usare il valore del parametro:

   ```json
   "sku": {
     "name": "[parameters('storageSKU')]"
   },
   ```    

4. Salvare il file.

Il modello si presenta ora come segue:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Il modello è completato e può essere distribuito nella sottoscrizione. Per la distribuzione, vedere [Distribuire risorse in Azure](resource-manager-quickstart-deploy.md).
* Per altre informazioni sulla struttura del modello, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).

