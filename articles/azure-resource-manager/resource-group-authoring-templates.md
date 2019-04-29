---
title: Struttura e sintassi del modello di Azure Resource Manger | Documentazione Microsoft
description: Descrive la struttura e le proprietà dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: tomfitz
ms.openlocfilehash: 94ed3c876ece827e4decd2b5b14332f5e854ab83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728017"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Comprendere la struttura e la sintassi dei modelli di Azure Resource Manger

Questo articolo descrive la struttura di un modello di Azure Resource Manager. Presenta le diverse sezioni di un modello e le proprietà disponibili in queste sezioni. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione.

Questo articolo è destinato agli utenti che hanno familiarità con i modelli di Resource Manager. Fornisce informazioni dettagliate sulla struttura e la sintassi del modello. Se si vuole un'introduzione alla creazione di un modello, vedere [creare il primo modello di Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato del modello

La struttura più semplice di un modello è costituita dagli elementi seguenti:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nome dell'elemento | Obbligatorio | DESCRIZIONE |
|:--- |:--- |:--- |
| $schema |Sì |Percorso del file di schema JSON che descrive la versione del linguaggio del modello.<br><br> Per le distribuzioni del gruppo di risorse, usare: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Per le distribuzioni della sottoscrizione, usare: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Sì |Versione del modello (ad esempio 1.0.0.0). Questo elemento accetta tutti i valori. Usare questo valore per documentare le modifiche significative al modello. Quando si distribuiscono risorse tramite il modello, è possibile usare questo valore per assicurarsi che venga usato il modello corretto. |
| apiProfile |No  | Una versione API che funge da una raccolta di versioni dell'API per i tipi di risorse. Utilizzare questo valore per evitare di dover specificare versioni dell'API per ogni risorsa nel modello. Quando si specifica una versione del profilo di API e non specifica una versione dell'API per il tipo di risorsa, Resource Manager usa la versione dell'API per quel tipo di risorsa che viene definito nel profilo.<br><br>La proprietà del profilo è particolarmente utile quando si distribuisce un modello in ambienti diversi, ad esempio Azure Stack e Azure globale. Usare la versione API del profilo per assicurarsi che il modello usa automaticamente le versioni supportate in entrambi gli ambienti. Per un elenco delle versioni di profilo API corrente e le versioni di API definite nel profilo di risorse, vedere [profilo API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Per altre informazioni, vedere [tiene traccia delle versioni tramite API profili](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |No  |Valori forniti durante la distribuzione per personalizzare la distribuzione di risorse. |
| [variables](#variables) |No  |Valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello. |
| [functions](#functions) |No  |Funzioni definite dall'utente disponibili nel modello. |
| [resources](#resources) |Sì |Tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse o sottoscrizione. |
| [outputs](#outputs) |No  |Valori restituiti dopo la distribuzione. |

Ogni elemento ha proprietà che è possibile impostare. In questo articolo le sezioni del modello vengono esaminate in modo dettagliato.

## <a name="syntax"></a>Sintassi

La sintassi di base del modello è JSON. Tuttavia, è possibile utilizzare espressioni per estendere i valori JSON disponibili all'interno del modello.  Le espressioni iniziare e terminare con parentesi quadre: `[` e `]`, rispettivamente. Il valore dell'espressione viene valutato quando viene distribuito il modello. Un'espressione può restituire una stringa, integer, valore booleano, matrice o oggetto. Nell'esempio seguente viene illustrata un'espressione nel valore predefinito di un parametro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

All'interno dell'espressione, la sintassi `resourceGroup()` chiama una delle funzioni disponibili in Gestione risorse per l'uso all'interno di un modello. Proprio come in JavaScript, le chiamate di funzione sono formattate come `functionName(arg1,arg2,arg3)`. La sintassi `.location` recupera una proprietà dall'oggetto restituito dalla funzione.

Le funzioni del modello e i relativi parametri non hanno la distinzione tra maiuscole e minuscole. Ad esempio, Gestione risorse consente di risolvere allo stesso modo le **variables('var1')** e le **VARIABLES('VAR1')**. Durante la valutazione, la funzione mantiene invariato l'uso delle maiuscole/minuscole, a meno che queste non vengano modificate espressamente dalla funzione, ad esempio toUpper o toLower. Alcuni tipi di risorse possono avere requisiti per le maiuscole e minuscole indipendentemente dalla modalità di valutazione delle funzioni.

Per avere una stringa letterale che inizi con una parentesi quadra `[`, ma che non venga interpretata come espressione, è necessario aggiungere un'altra parentesi in modo che la stringa inizi con `[[`.

Per passare un valore stringa come parametro a una funzione, usare le virgolette singole.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Per eseguire l'escape di virgolette doppie in un'espressione, ad esempio l'aggiunta di un oggetto JSON nel modello, usare la barra rovesciata.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Un'espressione del modello non può superare i 24.576 caratteri.

Per l’elenco completo delle funzioni del modello, vedere [Funzioni del modello di Gestione risorse di Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametri

Nella sezione parameters del modello si possono specificare i valori che è possibile immettere durante la distribuzione delle risorse. I valori dei parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente, ad esempio sviluppo, test e produzione. Non è obbligatorio specificare parametri nel modello, ma senza di essi il modello distribuisce sempre le stesse risorse con lo stesso nome, la stessa posizione e le stesse proprietà.

È previsto un limite di 256 parametri in un modello. Il numero di parametri può essere ridotto usando oggetti che contengono più proprietà, come illustrato in questo articolo.

### <a name="available-properties"></a>Proprietà disponibili

Le proprietà disponibili per un parametro sono:

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

| Nome dell'elemento | Obbligatorio | DESCRIZIONE |
|:--- |:--- |:--- |
| parameterName |Sì |Nome del parametro. Deve essere un identificatore JavaScript valido. |
| type |Sì |Tipo di valore del parametro. I tipi e i valori consentiti sono **string**, **securestring**, **int**, **bool**, **object**, **secureObject** e **array**. |
| defaultValue |No  |Valore predefinito per il parametro, se non viene fornito alcun valore per il parametro. |
| allowedValues |No  |Matrice di valori consentiti per il parametro per assicurare che venga fornito il valore corretto. |
| minValue |No  |Il valore minimo per i parametri di tipo int, questo valore è inclusivo. |
| maxValue |No  |Il valore massimo per i parametri di tipo int, questo valore è inclusivo. |
| minLength |No  |Lunghezza minima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| maxLength |No  |Lunghezza massima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| description |No  |Descrizione del parametro visualizzato agli utenti nel portale. Per altre informazioni, consultare la sezione [Comments in templates](#comments) (Commenti nel modello). |

### <a name="define-and-use-a-parameter"></a>Definire e usare un parametro

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

### <a name="template-functions-with-parameters"></a>Funzioni di modello con parametri

Quando si specifica il valore predefinito per un parametro, è possibile usare la maggior parte delle funzioni del modello. Si può usare il valore di un altro parametro per generare un valore predefinito. Il modello seguente illustra l'uso delle funzioni nel valore predefinito:

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

### <a name="objects-as-parameters"></a>Oggetti come parametri

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

### <a name="parameter-example-templates"></a>Modelli di esempio di parametro

Questi modelli di esempio illustrano alcuni scenari per l'uso dei parametri. Distribuirli per testare il modo in cui i parametri vengono gestiti in scenari diversi.

|Modello  |DESCRIZIONE  |
|---------|---------|
|[parametri con funzioni per i valori predefiniti](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Illustra come usare le funzioni di modello quando si definiscono valori predefiniti per i parametri. Il modello non distribuisce alcuna risorsa. Crea valori di parametro e restituisce questi valori. |
|[oggetto parametro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Illustra l'uso di un oggetto per un parametro. Il modello non distribuisce alcuna risorsa. Crea valori di parametro e restituisce questi valori. |

## <a name="variables"></a>Variabili

Nella sezione variables è possibile costruire valori da usare in tutto il modello. Non è obbligatorio definire le variabili. Queste tuttavia consentono spesso di semplificare il modello, riducendo le espressioni complesse.

### <a name="available-definitions"></a>Definizioni disponibili

Nell'esempio seguente mostra le opzioni disponibili per la definizione di una variabile:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Per informazioni sull'uso `copy` per creare valori diversi per una variabile, vedere [iterazione delle variabili](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definire e usare una variabile

L'esempio seguente illustra la definizione di una variabile: Crea un valore stringa per il nome di un account di archiviazione. Usa diverse funzioni di modello per ottenere un valore di parametro e si concatena a una stringa univoca.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

La variabile viene usata quando si definisce la risorsa.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Variabili di configurazione

È possibile usare i tipi JSON complessi per definire i valori associati a un ambiente.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

Nei parametri, creare un valore che indichi i valori di configurazione da usare.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Recuperare le impostazioni correnti con:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Modelli di esempio di variabile

Questi modelli di esempio illustrano alcuni scenari per l'uso delle variabili. Distribuirli per testare il modo in cui le variabili vengono gestite in scenari diversi. 

|Modello  |DESCRIZIONE  |
|---------|---------|
| [definizioni delle variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Illustra i diversi tipi di variabili. Il modello non distribuisce alcuna risorsa. Crea e restituisce valori variabili. |
| [variabile di configurazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Illustra l'uso di una variabile che definisce i valori di configurazione. Il modello non distribuisce alcuna risorsa. Crea e restituisce valori variabili. |
| [regole di sicurezza della rete](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [file dei parametri](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Crea una matrice nel formato corretto per assegnare le regole di sicurezza a un gruppo di sicurezza della rete. |


## <a name="functions"></a>Funzioni

Nel modello è possibile creare funzioni personalizzate. Tali funzioni sono disponibili per usare il modello. Si definiscono in genere espressioni complesse che non si vogliono ripetere in tutto il modello. Le funzioni definite dall'utente vengono create da espressioni e [funzioni](resource-group-template-functions.md) supportate nei modelli.

Quando si crea una funzione definita dall'utente, è necessario tenere presente alcune restrizioni:

* La funzione non può accedere alle variabili.
* La funzione può usare solo i parametri definiti in essa. Quando si usa la [funzione parameters](resource-group-template-functions-deployment.md#parameters) all'interno di una funzione definita dall'utente, è limitato ai parametri per tale funzione.
* La funzione non può chiamare altre funzioni definite dall'utente.
* La funzione non può usare la [funzione di riferimento](resource-group-template-functions-resource.md#reference).
* I parametri della funzione non possono avere valori predefiniti.

Le funzioni richiedono che sia definito un valore dello spazio dei nomi per evitare conflitti di denominazione con le funzioni del modello. L'esempio illustrata una funzione che restituisce un nome di account di archiviazione:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

La funzione viene chiamata con:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
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
]
```

## <a name="resources"></a>Risorse
Nella sezione risorse è possibile definire le risorse da distribuire o aggiornare.

### <a name="available-properties"></a>Proprietà disponibili

Le risorse vengono definite con la struttura seguente:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome dell'elemento | Obbligatorio | DESCRIZIONE |
|:--- |:--- |:--- |
| condition | No  | Valore booleano che indica se verrà eseguito il provisioning della risorsa durante questa distribuzione. Se `true`, la risorsa viene creata durante la distribuzione. Se `false`, la risorsa viene ignorata per questa distribuzione. Visualizzare [condizione](#condition). |
| apiVersion |Sì |Versione dell'API REST da utilizzare per la creazione della risorsa. Per determinare i valori disponibili, vedere [riferimento a un modello](/azure/templates/). |
| type |Sì |Tipo di risorsa. Questo valore è una combinazione dello spazio dei nomi del provider di risorse e del tipo di risorsa, ad esempio **Microsoft.Storage/storageAccounts**. Per determinare i valori disponibili, vedere [riferimento a un modello](/azure/templates/). Per una risorsa figlio, il formato del tipo dipende dal fatto che ha annidati all'interno della risorsa padre o definiti all'esterno dalla risorsa padre. Visualizzare [le risorse figlio](#child-resources). |
| name |Sì |Nome della risorsa. Il nome deve rispettare le restrizioni dei componenti URI definite dallo standard RFC3986. I servizi Azure che rendono visibile il nome della risorsa a terze parti convalidano anche il nome, per garantire che non si tratti di un tentativo di spoofing per un'identità alternativa. Per una risorsa figlio, il formato del nome dipende se ha annidati all'interno della risorsa padre o definiti all'esterno dalla risorsa padre. Visualizzare [le risorse figlio](#child-resources). |
| location |Variabile |Aree geografiche supportate della risorsa specificata. È possibile selezionare qualsiasi località disponibile, ma è in genere opportuno sceglierne una vicina agli utenti. Di solito è anche opportuno inserire le risorse che interagiscono tra loro nella stessa area. La maggior parte dei tipi di risorsa richiede una posizione, ma alcuni tipi (ad esempio un'assegnazione di ruolo) non la richiedono. |
| tags |No  |Tag associati alla risorsa. Applicare i tag per organizzare in modo logico le risorse nella sottoscrizione. |
| commenti |No  |Le note per documentare le risorse nel modello. Per altre informazioni, consultare la sezione [Commenti in template](resource-group-authoring-templates.md#comments). |
| copy |No  |Numero di risorse da creare, se sono necessarie più istanze. La modalità predefinita è parallela. Specificare la modalità seriale quando si desidera che non tutte le risorse vengano distribuite contemporaneamente. Per altre informazioni, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |No  |Risorse da distribuire prima della distribuzione di questa risorsa. Resource Manager valuta le dipendenze tra le risorse e le distribuisce nell'ordine corretto. Quando le risorse non sono interdipendenti, vengono distribuite in parallelo. Il valore può essere un elenco delimitato da virgole di nomi o identificatori univoci di risorse. Elencare solo le risorse distribuite in questo modello. Le risorse non definite in questo modello devono essere già esistenti. Evitare di aggiungere dipendenze non necessarie perché possono rallentare la distribuzione e creare dipendenze circolari. Per indicazioni sull'impostazione delle dipendenze, vedere l'articolo relativo alla [definizione delle dipendenze nei modelli di Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |No  |Impostazioni di configurazione specifiche delle risorse. I valori per l'elemento properties corrispondono esattamente a quelli specificati nel corpo della richiesta per l'operazione API REST (metodo PUT) per creare la risorsa. È inoltre possibile specificare una matrice di copia per creare diverse istanze di una proprietà. Per determinare i valori disponibili, vedere [riferimento a un modello](/azure/templates/). |
| sku | No  | Alcune risorse consentono valori che definiscono lo SKU da distribuire. Ad esempio, è possibile specificare il tipo di ridondanza per un account di archiviazione. |
| kind | No  | Alcune risorse consentono un valore che definisce il tipo di risorsa distribuito. Ad esempio, è possibile specificare il tipo di Cosmos DB da creare. |
| piano | No  | Alcune risorse consentono valori che definiscono il piano da distribuire. Ad esempio, è possibile specificare l'immagine del marketplace per una macchina virtuale. | 
| resources |No  |Risorse figlio che dipendono dalla risorsa in via di definizione. Specificare solo tipi di risorsa consentiti dallo schema della risorsa padre. La dipendenza dalla risorsa padre non è implicita. È necessario definirla in modo esplicito. Visualizzare [le risorse figlio](#child-resources). |

### <a name="condition"></a>Condizione

Quando è necessario decidere durante la distribuzione se si desidera creare una risorsa, usare il `condition` elemento. Il valore di questo elemento restituisce true o false. Quando il valore è true, la risorsa viene creata. Quando il valore è false, la risorsa non viene creata. Il valore può essere applicato solo all'intera risorsa.

In genere, si usa questo valore quando si vuole creare una nuova risorsa o usarne una esistente. Per indicare, ad esempio, se viene distribuito un nuovo account di archiviazione o se ne viene usato uno esistente, specificare:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Per un modello di esempio completo che usa l'elemento `condition`, vedere [Macchina virtuale con una Rete virtuale nuova o esistente, archiviazione e indirizzo IP pubblico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

Se si usa un' [riferimento](resource-group-template-functions-resource.md#reference) oppure [elenco](resource-group-template-functions-resource.md#list) funzione con una risorsa che viene distribuita in modo condizionale, la funzione viene valutata anche se la risorsa non è stata distribuita. Si verifica un errore se la funzione fa riferimento a una risorsa che non esiste. Usare la [se](resource-group-template-functions-logical.md#if) funzione per assicurarsi che la funzione viene valutata solo per le condizioni quando la risorsa viene distribuita. Vedere le [se funzione](resource-group-template-functions-logical.md#if) per un modello di esempio che usa se e riferimento a una risorsa distribuita in modo condizionale.

### <a name="resource-names"></a>Nomi di risorse

In genere vengono usati tre tipi di nomi di risorse in Resource Manager:

* Nomi di risorse che devono essere univoci.
* Nomi di risorse che non devono necessariamente essere univoci, ma che si desidera rendano possibile l'identificazione di una risorsa.
* Nomi di risorse che possono essere generici.

Fornire una **nome della risorsa univoco** per qualsiasi tipo di risorsa che ha un endpoint di accesso dati. Alcuni tipi di risorse comuni che richiedono un nome univoco includono:

* Archiviazione di Azure<sup>1</sup> 
* Funzionalità app Web del servizio app di Azure
* SQL Server
* Azure Key Vault
* Cache Redis di Azure
* Azure Batch
* Gestione traffico di Azure
* Ricerca di Azure
* HDInsight di Azure

<sup>1</sup> I nomi di account di archiviazione devono essere formati da lettere minuscole, un massimo di 24 caratteri e non devono includere alcun segno meno.

Quando si imposta il nome, è possibile creare manualmente un nome univoco o usare la funzione [uniqueString()](resource-group-template-functions-string.md#uniquestring) per generare un nome. È spesso opportuno aggiungere un prefisso o un suffisso al risultato di **uniqueString**. La modifica del nome univoco consente di identificare più facilmente il tipo di risorsa in base al nome. Ad esempio, è possibile generare un nome univoco per un account di archiviazione usando la variabile seguente:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Per alcuni tipi di risorse, si potrebbe voler fornire una **nome per l'identificazione**, ma il nome non deve essere univoco. Per questi tipi di risorse, specificare un nome che descrive lo utilizzo o le caratteristiche.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

Per tipi di risorse, che si soprattutto l'accesso tramite un'altra risorsa, è possibile usare una **nome generico** che sia hardcoded nel modello. Ad esempio, è possibile impostare un nome generico e standard per le regole del firewall in SQL server:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Percorso risorsa

Quando si distribuisce un modello, è necessario fornire la posizione per ogni risorsa. Diversi tipi di risorse sono supportati in posizioni diverse. Per conoscere le posizioni supportate per un tipo di risorsa, consultare [Provider e tipi di risorse di Azure](resource-manager-supported-services.md).

Usare un parametro per specificare la posizione delle risorse e impostare il valore predefinito su `resourceGroup().location`.

L'esempio seguente illustra un account di archiviazione che viene distribuito in una posizione specificata come parametro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>Risorse figlio

All'interno di alcuni tipi di risorsa è anche possibile definire una matrice di risorse figlio. Le risorse figlio sono risorse presenti solo all'interno del contesto di un'altra risorsa. Ad esempio, un database SQL non può esistere senza un server SQL, pertanto il database è un figlio del server. È possibile definire il database all'interno della definizione per il server.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  ...
  "resources": [
    {
      "apiVersion": "2017-10-01-preview",
      "type": "databases",
      "name": "exampledatabase",
      ...
    }
  ]
}
```

Tuttavia, non è necessario definire il database all'interno del server. È possibile definire la risorsa figlio al primo livello. Questo approccio potrebbe essere usato se la risorsa padre non viene distribuita nello stesso modello o se si vuole usare `copy` per creare più di una risorsa figlio. Con questo approccio, è necessario specificare il tipo di risorsa completo e includere il nome della risorsa padre nel nome della risorsa figlio.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  "resources": [ 
  ],
  ...
},
{
  "apiVersion": "2017-10-01-preview",
  "type": "Microsoft.Sql/servers/databases",
  "name": "exampleserver/exampledatabase",
  ...
}
```

I valori forniti per nome e il tipo variano a seconda che la risorsa figlio sia definita all'interno della risorsa padre o di fuori della risorsa padre.

Quando annidata nella risorsa padre, usare:

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Quando viene definito all'esterno dalla risorsa padre, usare:

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Quando annidata, il tipo è impostato su `databases` , ma il tipo di risorsa completo è ancora `Microsoft.Sql/servers/databases`. Non si specifica `Microsoft.Sql/servers/` perché viene ottenuto dal tipo della risorsa padre. Il nome della risorsa figlio è impostato su `exampledatabase`, ma il nome completo include il nome della risorsa padre. Non si specifica `exampleserver` perché viene ottenuto dalla risorsa padre.

Quando si crea un riferimento completo a una risorsa, l'ordine di combinazione dei segmenti dal tipo e dal nome non è semplicemente una concatenazione dei due elementi. Dopo lo spazio dei nomi, usare invece una sequenza di coppie *tipo/nome* dal meno specifico al più specifico:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Ad esempio: 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` è corretto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` non è corretto

## <a name="outputs"></a>Output

Nella sezione dell'output è possibile specificare i valori restituiti dalla distribuzione. In genere, per restituire valori dalle risorse distribuite.

### <a name="available-properties"></a>Proprietà disponibili

L'esempio seguente illustra la struttura di una definizione di output:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nome dell'elemento | Obbligatorio | DESCRIZIONE |
|:--- |:--- |:--- |
| outputName |Sì |Nome del valore di output. Deve essere un identificatore JavaScript valido. |
| condition |No  | Valore booleano che indica se questo valore di output viene restituito. Quando è `true`, il valore è incluso nell'output per la distribuzione. Quando è `false`, il valore dell'output viene ignorato per questa distribuzione. Quando non è specificato, il valore predefinito è `true`. |
| type |Sì |Tipo del valore di output. I valori di output supportano gli stessi tipi dei parametri di input del modello. Se si specifica **securestring** per il tipo di output, il valore non viene visualizzato nella cronologia di distribuzione e non può essere recuperato da un altro modello. Per usare un valore del segreto in più di un modello, archiviare il segreto in un insieme di credenziali chiave e il segreto nel file dei parametri di riferimento. Per altre informazioni, vedere [usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](resource-manager-keyvault-parameter.md). |
| value |Sì |Espressione del linguaggio di modello valutata e restituita come valore di output. |

### <a name="define-and-use-output-values"></a>Definire e usare i valori di output

L'esempio seguente illustra come restituire l'ID risorsa per un indirizzo IP pubblico:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

L'esempio seguente mostra come restituire in modo condizionale l'ID risorsa per un indirizzo IP pubblico in base al fatto che ne sia stato distribuito uno nuovo:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Per un semplice esempio dell'output condizionale, vedere [modello di output condizionale](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Dopo la distribuzione, è possibile recuperare il valore con uno script. Per PowerShell, usare:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

È possibile recuperare il valore di output da un modello collegato usando la funzione [reference](resource-group-template-functions-resource.md#reference). Per ottenere un valore di output da un modello collegato, recuperare il valore della proprietà con una sintassi analoga a: `"[reference('deploymentName').outputs.propertyName.value]"`.

Quando si ottiene una proprietà di output da un modello collegato, il nome della proprietà non può includere un trattino.

Nell'esempio seguente viene illustrato come impostare l'indirizzo IP in un servizio di bilanciamento del carico recuperando il valore da un modello collegato.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Non è possibile usare la funzione `reference` nella sezione outputs di un [modello annidato](resource-group-linked-templates.md#link-or-nest-a-template). Per restituire i valori per una risorsa distribuita in un modello annidato, convertire il modello annidato in un modello collegato.

### <a name="output-example-templates"></a>Modelli di esempio di output

|Modello  |DESCRIZIONE  |
|---------|---------|
|[Copia variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crea variabili complesse e restituisce i valori. Non distribuisce alcuna risorsa. |
|[Indirizzo IP pubblico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crea un indirizzo IP pubblico e restituisce l'ID risorsa. |
|[Bilanciamento del carico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | È collegato al modello precedente. Usa l'ID risorsa nell'output durante la creazione del dispositivo di bilanciamento del carico. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Commenti e metadati

Sono disponibili diverse opzioni per aggiungere commenti e metadati al modello.

È possibile aggiungere un oggetto `metadata` praticamente ovunque nel modello. Resource Manager ignora l'oggetto, ma l'editor JSON potrebbe segnalare che la proprietà non è valida. Nell'oggetto definire le proprietà necessarie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Per **parameters**, aggiungere un oggetto `metadata` con una proprietà `description`.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Durante la distribuzione del modello tramite il portale, il testo specificato nella descrizione viene usato automaticamente come suggerimento per tale parametro.

![Mostra il suggerimento relativo al parametro](./media/resource-group-authoring-templates/show-parameter-tip.png)

Per **resources**, aggiungere un elemento `comments` o un oggetto metadata. L'esempio seguente mostra sia un elemento comments sia un oggetto metadata.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Per **outputs**, aggiungere un oggetto metadata al valore di output.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Non è possibile aggiungere un oggetto metadata alle funzioni definite dall'utente.

Per i commenti inline, è possibile usare `//`, ma questa sintassi non funziona con tutti gli strumenti. Non è possibile usare l'interfaccia della riga di comando di Azure per distribuire il modello con commenti inline. Inoltre, non è possibile usare l'editor dei modelli del portale per operare sui modelli con commenti inline. Se si aggiunge questo stile di commento, assicurarsi che gli strumenti usati supportino i commenti JSON inline.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

In Visual Studio Code è possibile impostare la modalità linguaggio su JSON con commenti. In questo modo i commenti inline non verranno più contrassegnati come non validi. Per modificare la modalità:

1. Aprire la finestra di selezione della modalità linguaggio (CTRL+K M).

1. Selezionare **JSON with Comments** (JSON con commenti).

   ![Selezionare la modalità linguaggio](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per unire più modelli durante la distribuzione, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per suggerimenti sulla creazione di modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](template-best-practices.md).
* Per consigli su come creare modelli di Resource Manager da poter usare in tutti gli ambienti Azure e in Azure Stack, vedere [I modelli di Azure Resource Manager possono essere sviluppati per la coerenza cloud](templates-cloud-consistency.md).
