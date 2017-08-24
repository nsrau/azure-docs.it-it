---
title: Creare il primo modello di Azure Resource Manager | Microsoft Docs
description: Guida dettagliata alla creazione del primo modello di Azure Resource Manager. Illustra come usare le informazioni di riferimento sul modello di un account di archiviazione per creare il modello.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/27/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 49086b51e2db1aebed45746306ae14b6f1feb631
ms.contentlocale: it-it
ms.lasthandoff: 07/31/2017

---

# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Creare e distribuire il primo modello di Azure Resource Manager
Questo argomento illustra la procedura per creare il primo modello di Azure Resource Manager. I modelli di Resource Manager sono file JSON che definiscono le risorse che è necessario distribuire per la soluzione. Per comprendere i concetti associati alla distribuzione e alla gestione delle soluzioni di Azure, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md). Se si vuole ottenere un modello per risorse esistenti, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).

Per creare e modificare i modelli, è necessario un editor JSON. [Visual Studio Code](https://code.visualstudio.com/) è un editor di codice, leggero, open source e multipiattaforma. È consigliabile usare Visual Studio Code per la creazione di modelli di Resource Manager. In questo argomento si presuppone che venga usato Visual Studio Code. Se tuttavia si ha un altro editor JSON (ad esempio, Visual Studio), è possibile usare tale editor.

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio Code. Se necessario, installarlo da [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-template"></a>Creare il modello

Iniziare dalla creazione di un modello semplice che consente di distribuire un account di archiviazione nella sottoscrizione.

1. Selezionare **File** > **Nuovo file**. 

   ![Nuovo file](./media/resource-manager-create-first-template/new-file.png)

2. Copiare e incollare nel file la sintassi JSON seguente:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   I nomi degli account di archiviazione sono soggetti a diverse restrizioni che ne complicano l'impostazione. Il nome deve essere di lunghezza compresa tra 3 e 24 caratteri, contenere solo numeri e lettere minuscole ed essere univoco. Il modello precedente usa la funzione [uniqueString](resource-group-template-functions-string.md#uniquestring) per generare un valore hash. Per dare più significato a questo valore hash, aggiunge il prefisso *storage*. 

3. Salvare il file con il nome **azuredeploy.json** in una cartella locale.

   ![Salvare il modello](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>Distribuire il modello

A questo punto è possibile distribuire il modello. Usare PowerShell o l'interfaccia della riga di comando di Azure per creare un gruppo di risorse. Distribuire quindi un account di archiviazione nel nuovo gruppo di risorse.

* Per PowerShell, usare i comandi seguenti dalla cartella che contiene il modello:

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Per un'installazione locale dell'interfaccia della riga di comando di Azure, usare i comandi seguenti dalla cartella che contiene il modello:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Al termine della distribuzione, il gruppo di risorse conterrà l'account di archiviazione.

## <a name="deploy-template-from-cloud-shell"></a>Distribuire il modello da Cloud Shell

È possibile usare [Cloud Shell](../cloud-shell/overview.md) per eseguire i comandi dell'interfaccia della riga di comando di Azure per la distribuzione del modello. Tuttavia, è prima necessario caricare il modello nella condivisione file per Cloud Shell. Per informazioni sulla configurazione di Cloud Shell per il primo utilizzo, vedere [Panoramica di Azure Cloud Shell](../cloud-shell/overview.md).

1. Accedere al [Portale di Azure](https://portal.azure.com).   

2. Selezionare il gruppo di risorse di Cloud Shell. Il modello del nome è `cloud-shell-storage-<region>`.

   ![Selezionare il gruppo di risorse](./media/resource-manager-create-first-template/select-cs-resource-group.png)

3. Selezionare l'account di archiviazione per Cloud Shell.

   ![Selezionare l'account di archiviazione](./media/resource-manager-create-first-template/select-storage.png)

4. Selezionare **File**.

   ![Selezione dei file](./media/resource-manager-create-first-template/select-files.png)

5. Selezionare la condivisione file per Cloud Shell. Il modello del nome è `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Selezionare la condivisione file](./media/resource-manager-create-first-template/select-file-share.png)

6. Selezionare **Aggiungi directory**.

   ![Aggiungi directory](./media/resource-manager-create-first-template/select-add-directory.png)

7. Assegnare il nome **templates** e scegliere **OK**.

   ![Assegnare il nome alla directory](./media/resource-manager-create-first-template/name-templates.png)

8. Selezionare la nuova directory.

   ![Selezionare la directory](./media/resource-manager-create-first-template/select-templates.png)

9. Selezionare **Carica**.

   ![Selezionare Carica](./media/resource-manager-create-first-template/select-upload.png)

10. Trovare e caricare il modello.

   ![Caricare il file](./media/resource-manager-create-first-template/upload-files.png)

11. Aprire il prompt.

   ![Aprire Cloud Shell](./media/resource-manager-create-first-template/start-cloud-shell.png)

12. Immettere i comandi seguenti in Cloud Shell:

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
   ```

Al termine della distribuzione, il gruppo di risorse conterrà l'account di archiviazione.

## <a name="customize-the-template"></a>Personalizzare il modello

Il modello funziona correttamente, ma non è flessibile. Distribuisce sempre un'archiviazione con ridondanza locale nell'area degli Stati Uniti centro-meridionali. Il nome è sempre *storage* seguito da un valore hash. Per consentire l'uso del modello per diversi scenari, aggiungere parametri al modello.

L'esempio seguente mostra la sezione parametri con due parametri. Il primo parametro `storageSKU` consente di specificare il tipo di ridondanza. Limita i valori che è possibile passare ai valori validi per un account di archiviazione. Specifica anche un valore predefinito. Il secondo parametro `storageNamePrefix` viene impostato per consentire un massimo di 11 caratteri. Specifica un valore predefinito.

```json
"parameters": {
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
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

Nella sezione variabili aggiungere una variabile denominata `storageName`. Combina il valore del prefisso dei parametri e un valore hash della funzione [uniqueString](resource-group-template-functions-string.md#uniquestring). Usa la funzione [toLower](resource-group-template-functions-string.md#tolower) per convertire tutti i caratteri in minuscolo.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Per usare i nuovi valori per l'account di archiviazione, modificare la definizione della risorsa:

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

Si noti che il nome dell'account di archiviazione è ora impostato sulla variabile aggiunta. Il nome dello SKU è impostato sul valore del parametro. La posizione impostata è la stessa del gruppo di risorse.

Salvare il file. 

Al termine della procedura descritta in questo articolo, il modello si presenta come segue:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Ridistribuire il modello

Ridistribuire il modello con valori diversi.

Per PowerShell, usare:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

Per Cloud Shell, caricare il modello modificato nella condivisione file. Sovrascrivere il file esistente. Usare quindi il comando seguente:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non saranno più necessarie, eliminare le risorse distribuite eliminando il gruppo di risorse.

Per PowerShell, usare:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla struttura del modello, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per altre informazioni sulle proprietà di un account di archiviazione, vedere le [informazioni di riferimento sul modello degli account di archiviazione](/azure/templates/microsoft.storage/storageaccounts).
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).

