---
title: Creare un account di Sperimentazione di Azure Machine Learning con un modello di Azure Resource Manager | Microsoft Docs
description: Questo articolo illustra un esempio per creare un account di Sperimentazione di Azure Machine Learning usando un modello di Azure Resource Manager.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 585666a521ba8e1fae274687cbd709baba871590
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Configurare il servizio Sperimentazione di Azure Machine Learning

## <a name="overview"></a>Panoramica
L'account, l'area di lavoro e il progetto del servizio Sperimentazione di Azure Machine Learning sono risorse di Azure e possono pertanto essere distribuiti tramite i modelli di Resource Manager. I modelli di Resource Manager sono file JSON che definiscono le risorse che è necessario distribuire per la soluzione. Per comprendere i concetti associati alla distribuzione e alla gestione delle soluzioni di Azure, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Distribuire un modello
Per la distribuzione di un modello è necessario eseguire solo pochi passaggi nell'interfaccia della riga di comando di Azure o nel portale di Azure.

### <a name="deploy-a-template-from-the-command-line"></a>Distribuire un modello dalla riga di comando
Tramite l'interfaccia della riga di comando, un singolo comando può distribuire un modello a un gruppo di risorse esistente.
Per informazioni sulla creazione di un modello, vedere più avanti.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Distribuire un modello dal portale di Azure
Se si preferisce, per creare e distribuire un modello è possibile usare anche il portale di Azure. Seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare **Altri servizi** e cercare "modelli".
3. Selezionare **Modelli**.
4. Fare clic su **+ Aggiungi** e copiare le informazioni del modello. 
5. Selezionare il modello creato nel passaggio 4 e fare clic su **Distribuisci**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Creare un modello da una risorsa di Azure esistente nel portale di Azure
Se si ha già un account di Sperimentazione di Azure Machine Learning, nel [portale di Azure](https://portal.azure.com) è possibile generare un modello a partire da tale risorsa. 

1. Passare a un account di Sperimentazione di Azure Machine Learning nel [portale di Azure](https://portal.azure.com).
2. In **Impostazioni** fare clic su **Script di automazione**.
3. Scaricare il modello. 

In alternativa, è possibile modificare manualmente i file del modello. Vedere la sezione seguente per un esempio di file del modello e dei parametri. 

### <a name="template-file-example"></a>Esempio di file del modello
Creare un file denominato "template-file.json" con il contenuto riportato di seguito. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>Parametri 
Creare un file con il contenuto riportato di seguito e salvarlo come <parameters.json>. 

È possibile modificare i valori di tre parametri. 
* AccountName: il nome dell'account di sperimentazione.
* Location: una delle aree di Azure supportate.
* StorageAccountSKU: Azure ML supporta solo l'archiviazione standard e non la premium. Per altre informazioni sull'archiviazione, vedere [Introduzione ad Archiviazione](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
* [Creare e installare istanze di Azure Machine Learning](quickstart-installation.md)
