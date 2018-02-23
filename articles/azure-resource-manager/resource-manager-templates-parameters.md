---
title: Sezione parameters del modello di Azure Resource Manager | Microsoft Docs
description: Descrive la sezione dei parametri dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON.
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
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: 5a519908f43193e41da9237a236d720fe2db58eb
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Sezione parameters dei modelli di Azure Resource Manager
Nella sezione parameters del modello si possono specificare i valori che è possibile immettere durante la distribuzione delle risorse. I valori dei parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente, ad esempio sviluppo, test e produzione. Non è obbligatorio specificare i parametri nel modello, ma senza di essi il modello distribuisce sempre le stesse risorse con lo stesso nome, località e proprietà.

È previsto un limite di 255 parametri per modello. Si può ridurre il numero di parametri usando oggetti che contengono più proprietà, come illustrato in questo articolo.

## <a name="define-and-use-a-parameter"></a>Definire e usare un parametro

L'esempio seguente illustra la definizione di un parametro semplice. Definisce il nome del parametro e specifica che accetta un valore stringa. Il parametro accetta solo valori significativi per l'uso previsto. Specifica un valore predefinito quando durante la distribuzione non viene fornito alcun valore. Infine, il parametro include una descrizione del relativo uso. 

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

Nel modello si fa riferimento al valore per il parametro con la sintassi seguente:

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

L'esempio precedente mostra solo alcune delle proprietà che è possibile usare nella sezione dei parametri. Le proprietà disponibili sono:

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
| type |Sì |Tipo di valore del parametro. I tipi e i valori consentiti sono **string**, **secureString**, **int**, **bool**, **object**, **secureObject** e **array**. |
| defaultValue |No  |Valore predefinito per il parametro, se non viene fornito alcun valore per il parametro. |
| allowedValues |No  |Matrice di valori consentiti per il parametro per assicurare che venga fornito il valore corretto. |
| minValue |No  |Il valore minimo per i parametri di tipo int, questo valore è inclusivo. |
| maxValue |No  |Il valore massimo per i parametri di tipo int, questo valore è inclusivo. |
| minLength |No  |Lunghezza minima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| maxLength |No  |Lunghezza massima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| description |No  |Descrizione del parametro visualizzato agli utenti nel portale. |

## <a name="template-functions-with-parameters"></a>Funzioni di modello con parametri

Quando si specifica il valore predefinito per un parametro, è possibile usare la maggior parte delle funzioni di modello. Si può usare il valore di un altro parametro per generare un valore predefinito. Il modello seguente illustra l'uso delle funzioni nel valore predefinito:

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

Non è possibile usare la funzione `reference` nella sezione dei parametri. I parametri vengono valutati prima della distribuzione, quindi la funzione `reference` non può ottenere lo stato di runtime di una risorsa. 

## <a name="objects-as-parameters"></a>Oggetti come parametri

Può essere più semplice organizzare i valori correlati passandoli come oggetto. Questo approccio riduce anche il numero di parametri nel modello.

Definire il parametro nel modello e specificare un oggetto JSON invece di un solo valore durante la distribuzione. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
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

Quindi, fare riferimento alle sottoproprietà del parametro usando l'operatore punto.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
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

* Usare **SecureString** per tutte le password e i segreti. Se si passano dati sensibili in un oggetto JSON, usare il tipo **secureObject**. Non è possibile leggere i parametri di modello di tipo secureString o secureObject dopo la distribuzione delle risorse. 
   
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

* Usare un parametro per specificare la posizione e condividere per quanto possibile il relativo valore con le risorse che potrebbero essere nella stessa posizione. Questo approccio permette di ridurre al minimo il numero di volte in cui gli utenti devono dare informazioni sulla posizione. Se un tipo di risorsa è supportato solo in un numero limitato di posizioni, provare a specificare una posizione valida direttamente nel modello oppure aggiungere un altro parametro location. Se un'organizzazione limita le aree consentite per gli utenti, l'espressione **resourceGroup().location** potrebbe impedire a un utente di distribuire il modello. Ad esempio, un utente crea un gruppo di risorse in un'area. Un secondo utente deve distribuire in quel gruppo di risorse, ma non ha accesso all'area. 
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[parameters('location')]",
         ...
     }
   ]
   ```
    
* Evitare di usare un parametro o una variabile per la versione dell'API per un tipo di risorsa. I valori e le proprietà delle risorse possono variare in base al numero di versione. Quando la versione dell'API è impostata su un parametro o una variabile, IntelliSense negli editor di codice non può determinare lo schema corretto. Impostare invece la versione dell'API come hardcoded nel modello.
* Evitare di specificare nel modello un nome di parametro che corrisponde a un parametro del comando di distribuzione. Resource Manager risolve questo conflitto di denominazione aggiungendo il suffisso **FromTemplate** al parametro del modello. Se, ad esempio, si include un parametro denominato **ResourceGroupName** nel modello, si crea un conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Durante la distribuzione verrà quindi richiesto di specificare un valore per **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Modelli di esempio

Questi modelli di esempio illustrano alcuni scenari per l'uso dei parametri. Distribuirli per testare il modo in cui i parametri vengono gestiti in scenari diversi.

|Modello  |DESCRIZIONE  |
|---------|---------|
|[parametri con funzioni per i valori predefiniti](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Illustra come usare le funzioni di modello quando si definiscono valori predefiniti per i parametri. Il modello non distribuisce alcuna risorsa. Crea valori di parametro e restituisce questi valori. |
|[oggetto parametro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Illustra l'uso di un oggetto per un parametro. Il modello non distribuisce alcuna risorsa. Crea valori di parametro e restituisce questi valori. |

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni sull'immissione di valori di parametro durante la distribuzione vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md). 
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per informazioni sull'uso di un oggetto parametro, vedere [Usare un oggetto come parametro in un modello di Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).