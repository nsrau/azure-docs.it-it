---
title: Sezione di Azure Resource Manager modello parametro | Documenti Microsoft
description: Descrive la sezione parametri di modelli di gestione risorse di Azure utilizzando la sintassi dichiarativa JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: tomfitz
ms.openlocfilehash: 7d0f53751bf529d52c156a8b9319b10560eb8997
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Sezione parametri di modelli di gestione risorse di Azure
Nella sezione parameters del modello si possono specificare i valori che è possibile immettere durante la distribuzione delle risorse. I valori dei parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente, ad esempio sviluppo, test e produzione. Non è obbligatorio specificare i parametri nel modello, ma senza di essi il modello distribuisce sempre le stesse risorse con lo stesso nome, località e proprietà.

Sono limitati a 255 parametri in un modello. È possibile ridurre il numero di parametri utilizzando gli oggetti che contengono più proprietà, come illustrato in questo articolo.

## <a name="define-and-use-a-parameter"></a>Definire e utilizzare un parametro

Nell'esempio seguente viene illustrata una definizione di parametro semplici. Definisce il nome del parametro e specifica che richiede un valore stringa. Il parametro accetta solo valori significativi per l'uso previsto. Specifica un valore predefinito quando viene fornito alcun valore durante la distribuzione. Infine, il parametro include una descrizione dell'utilizzo. 

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
  }   
}
```

Nel modello di riferimento al valore per il parametro con la sintassi seguente:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="available-properties"></a>Proprietà disponibili

Nell'esempio precedente è stato illustrato solo alcune delle proprietà che è possibile utilizzare nella sezione parametro. Le proprietà disponibili sono:

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--- |:--- |:--- |
| parameterName |Sì |Nome del parametro. Deve essere un identificatore JavaScript valido. |
| type |Sì |Tipo di valore del parametro. I tipi consentiti e i valori sono **stringa**, **secureString**, **int**, **bool**, **oggetto**, **secureObject**, e **matrice**. |
| defaultValue |No  |Valore predefinito per il parametro, se non viene fornito alcun valore per il parametro. |
| allowedValues |No  |Matrice di valori consentiti per il parametro per assicurare che venga fornito il valore corretto. |
| minValue |No  |Il valore minimo per i parametri di tipo int, questo valore è inclusivo. |
| maxValue |No  |Il valore massimo per i parametri di tipo int, questo valore è inclusivo. |
| minLength |No  |Lunghezza minima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| maxLength |No  |Lunghezza massima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| description |No  |Descrizione del parametro visualizzato agli utenti nel portale. |

## <a name="template-functions-with-parameters"></a>Funzioni di modello con parametri

Quando si specifica il valore predefinito per un parametro, è possibile utilizzare la maggior parte delle funzioni di modello. È possibile utilizzare un altro valore di parametro per generare un valore predefinito. Il modello seguente viene illustrato l'utilizzo delle funzioni del valore predefinito:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Non è possibile utilizzare il `reference` funzione nella sezione dei parametri. I parametri vengono valutati prima della distribuzione pertanto `reference` funzione non è possibile ottenere lo stato di runtime di una risorsa. 

## <a name="objects-as-parameters"></a>Oggetti come parametri

Potrebbe risultare più semplice organizzare i valori correlati passandoli come oggetto. Questo approccio riduce inoltre il numero di parametri nel modello.

Definire il parametro del modello e specificare un oggetto JSON anziché un solo valore durante la distribuzione. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Quindi, fare riferimento a sottoproprietà del parametro utilizzando l'operatore punto.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location":"[resourceGroup().location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="recommendations"></a>Raccomandazioni
Le informazioni seguenti possono essere utili quando si usano parametri:

* Ridurre al minimo l'uso di parametri. Se possibile, usare una variabile o un valore letterale. Specificare parametri solo per questi scenari:
   
   * Impostazioni da variare in base all'ambiente (ad esempio SKU, dimensioni o capacità).
   * Nomi di risorse da specificare per facilitare l'identificazione.
   * Valori usati spesso per completare altre attività (ad esempio nome utente amministratore).
   * Segreti (ad esempio password).
   * Il numero o la matrice di valori da usare durante la creazione di più istanze di un tipo di risorsa.
* Usare la notazione Camel per i nomi dei parametri.
* Indicare una descrizione nei metadati per ogni parametro:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definire i valori predefiniti per i parametri (ad eccezione delle password e delle chiavi SSH). Fornendo un valore predefinito, il parametro diventa facoltativo durante la distribuzione. Il valore predefinito può essere una stringa vuota. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Utilizzare **SecureString** per tutti i segreti e le password. Se si passano dati sensibili in un oggetto JSON, usare il tipo **secureObject**. Non è possibile leggere i parametri di modello di tipo secureString o secureObject dopo la distribuzione delle risorse. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Quando possibile, evitare di usare un parametro per specificare la posizione. Usare invece la proprietà **location** del gruppo di risorse. Usando l'espressione **resourceGroup().location** per tutte le risorse, le risorse nel modello verranno distribuite nella stessa posizione del gruppo di risorse:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Se un tipo di risorsa è supportato solo in un numero limitato di posizioni, provare a specificare una posizione valida direttamente nel modello. Se è necessario usare un parametro **location**, condividere per quanto possibile il relativo valore con le risorse che potrebbero essere nella stessa posizione. Questo approccio riduce il numero di volte in cui gli utenti vengono richiesto di fornire informazioni sul percorso.
* Evitare di usare un parametro o una variabile per la versione dell'API per un tipo di risorsa. I valori e le proprietà delle risorse possono variare in base al numero di versione. Quando la versione dell'API è impostata su un parametro o una variabile, IntelliSense negli editor di codice non può determinare lo schema corretto. Impostare invece la versione dell'API come hardcoded nel modello.
* Evitare di specificare un nome di parametro nel modello che corrisponde a un parametro del comando di distribuzione. Gestione risorse consente di risolvere questo conflitto di denominazione aggiungendo il suffisso **FromTemplate** al parametro di modello. Se, ad esempio, si include un parametro denominato **ResourceGroupName** nel modello, si crea un conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Durante la distribuzione verrà quindi richiesto di specificare un valore per **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Modelli di esempio

Questi modelli di esempio vengono illustrati alcuni scenari per l'utilizzo di parametri. Distribuire in modo da verificare come parametri vengono gestiti in diversi scenari.

|Modello  |DESCRIZIONE  |
|---------|---------|
|[parametri con le funzioni per i valori predefiniti](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Viene illustrato come utilizzare le funzioni modello quando si definiscono i valori predefiniti per i parametri. Il modello di non distribuire le risorse. Costruisce i valori dei parametri e restituisce questi valori. |
|[oggetto Parameter](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Viene illustrato l'utilizzo di un oggetto per un parametro. Il modello di non distribuire le risorse. Costruisce i valori dei parametri e restituisce questi valori. |

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni sull'immissione di valori di parametro durante la distribuzione vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md). 
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per informazioni sull'utilizzo di un oggetto parametro, vedere [utilizzare un oggetto come parametro in un modello di gestione risorse di Azure](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).