---
title: Struttura e sintassi del modello di Azure Resource Manger | Documentazione Microsoft
description: "Descrive la struttura e le proprietà dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: de6016130666774577fb4ef598a44f6191ba9a76
ms.lasthandoff: 04/14/2017


---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Comprendere la struttura e la sintassi dei modelli di Azure Resource Manger
Questo argomento descrive la struttura di un modello di Azure Resource Manager. Presenta le diverse sezioni di un modello e le proprietà disponibili in queste sezioni. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. Per un'esercitazione dettagliata sulla creazione di un modello, vedere [Creare il primo modello di Azure Resource Manager](resource-manager-create-first-template.md).

Limitare le dimensioni del modello a 1 MB e ogni file di parametri a 64 KB. Il limite di 1 MB si applica allo stato finale del modello dopo che è stato espanso con le definizioni delle risorse iterative e i valori di variabili e parametri. 

## <a name="template-format"></a>Formato del modello
La struttura più semplice di un modello è costituita dagli elementi seguenti:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--- |:--- |:--- |
| $schema |Sì |Percorso del file di schema JSON che descrive la versione del linguaggio del modello. Usare l'URL riportato nell'esempio precedente. |
| contentVersion |Sì |Versione del modello (ad esempio 1.0.0.0). Questo elemento accetta tutti i valori. Quando si distribuiscono risorse tramite il modello, è possibile usare questo valore per assicurarsi che venga usato il modello corretto. |
| parameters |No |Valori forniti durante la distribuzione per personalizzare la distribuzione di risorse. |
| variables |No |Valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello. |
| resources |Sì |Tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse. |
| outputs |No |Valori restituiti dopo la distribuzione. |

Ogni elemento contiene proprietà che è possibile impostare. L'esempio seguente contiene la sintassi completa per un modello:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
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
    },
    "variables": {  
        "<variable-name>": "<variable-value>",
        "<variable-name>": { 
            <variable-complex-type-value> 
        }
    },
    "resources": [
      {
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": "<name-value-pairs-for-resource-tagging>",
          "comments": "<your-reference-notes>",
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": "<settings-for-the-resource>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>"
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Le sezioni del modello verranno esaminate in modo dettagliato più avanti in questo argomento.

## <a name="expressions-and-functions"></a>Espressioni e funzioni
La sintassi di base del modello è JSON. Le espressioni e le funzioni estendono ad ogni modo i valori JSON disponibili all'interno del modello.  Le espressioni vengono scritte all'interno di valori letterali stringa JSON il cui primo e ultimo carattere sono le parentesi quadre: rispettivamente`[` e `]`. Il valore dell'espressione viene valutato quando viene distribuito il modello. Sebbene sia scritto come valore letterale stringa, il risultato della valutazione dell'espressione può essere di un tipo JSON diverso, ad esempio una matrice o un numero intero, a seconda dell'espressione effettiva.  Per avere una stringa letterale che inizi con una parentesi quadra `[`, ma che non venga interpretata come espressione, è necessario aggiungere un'altra parentesi in modo che la stringa inizi con `[[`.

Solitamente, si usano espressioni con funzioni per eseguire operazioni per la configurazione della distribuzione. Proprio come in JavaScript, le chiamate di funzione sono formattate come `functionName(arg1,arg2,arg3)`. Per i riferimenti alle proprietà si usano il punto e gli operatori [index].

L'esempio seguente illustra come usare diverse funzioni al momento di costruite i valori:

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Per l’elenco completo delle funzioni del modello, vedere [Funzioni del modello di Gestione risorse di Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>parameters
Nella sezione parameters del modello si possono specificare i valori che è possibile immettere durante la distribuzione delle risorse. I valori dei parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente, ad esempio sviluppo, test e produzione. Non è obbligatorio specificare i parametri nel modello, ma senza di essi il modello distribuisce sempre le stesse risorse con lo stesso nome, località e proprietà.

I parametri vengono definiti con la struttura seguente:

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

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--- |:--- |:--- |
| parameterName |Sì |Nome del parametro. Deve essere un identificatore JavaScript valido. |
| type |Sì |Tipo di valore del parametro. Vedere l'elenco dei tipi consentiti riportato dopo questa tabella. |
| defaultValue |No |Valore predefinito per il parametro, se non viene fornito alcun valore per il parametro. |
| allowedValues |No |Matrice di valori consentiti per il parametro per assicurare che venga fornito il valore corretto. |
| minValue |No |Il valore minimo per i parametri di tipo int, questo valore è inclusivo. |
| maxValue |No |Il valore massimo per i parametri di tipo int, questo valore è inclusivo. |
| minLength |No |Lunghezza minima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| maxLength |No |Lunghezza massima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| Descrizione |No |Descrizione del parametro visualizzato agli utenti nel portale. |

I valori e i tipi consentiti sono:

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

Per specificare un parametro come facoltativo, fornire un valore defaultValue (che può essere anche una stringa vuota). 

Se nel modello si specifica un nome di parametro che corrisponde a un parametro nel comando per distribuire il modello, si crea una potenziale ambiguità in merito ai valori forniti. Resource Manager risolve questa confusione aggiungendo il suffisso **FromTemplate** al parametro del modello. Se si include un parametro denominato **ResourceGroupName** nel modello, ad esempio, questo sarà in conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment][deployment2cmdlet]. Durante la distribuzione verrà quindi richiesto di specificare un valore per **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione non attribuendo ai parametri lo stesso nome dei parametri usati per operazioni di distribuzione.

> [!NOTE]
> Per tutte le password, le chiavi e altre informazioni riservate si consiglia di usare il tipo **secureString** . Se si passano dati sensibili in un oggetto JSON, usare il tipo **secureObject**. Non è possibile leggere i parametri di modello di tipo secureString o secureObject dopo la distribuzione delle risorse. 
> 
> La voce seguente nella cronologia di distribuzione indica ad esempio il valore per una stringa e un oggetto, ma non per secureString e secureObject.
>
> ![visualizzare i valori della distribuzione](./media/resource-group-authoring-templates/show-parameters.png)  
>

Il seguente esempio mostra come definire i parametri:

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

Per informazioni sull'immissione di valori di parametro durante la distribuzione vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md). 

## <a name="variables"></a>variables
Nella sezione variables è possibile costruire valori da usare in tutto il modello. Non è obbligatorio definire le variabili, che però permettono spesso di semplificare il modello riducendo le espressioni complesse.

Le variabili vengono definite con la struttura seguente:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

Nell'esempio seguente viene illustrato come definire una variabile creata da due valori di parametro:

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

Nell'esempio successivo viene illustrata una variabile che rappresenta un tipo JSON complesso e variabili create da altre variabili:

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
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>resources
Nella sezione risorse è possibile definire le risorse da distribuire o aggiornare. Questa sezione può risultare complicata perché per specificare i valori corretti è necessario conoscere i tipi da distribuire. Per i valori specifici della risorsa (apiVersion, tipo e proprietà) che è necessario impostare, vedere [Define resources in Azure Resource Manager templates](/azure/templates/) (Definire risorse nei modelli di Azure Resource Manager). 

Le risorse vengono definite con la struttura seguente:

```json
"resources": [
  {
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": "<name-value-pairs-for-resource-tagging>",
      "comments": "<your-reference-notes>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": "<settings-for-the-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--- |:--- |:--- |
| apiVersion |Sì |Versione dell'API REST da utilizzare per la creazione della risorsa. |
| type |Sì |Tipo di risorsa. Questo valore è una combinazione dello spazio dei nomi del provider di risorse e del tipo di risorsa, ad esempio **Microsoft.Storage/storageAccounts**. |
| name |Sì |Nome della risorsa. Il nome deve rispettare le restrizioni dei componenti URI definite dallo standard RFC3986. I servizi Azure che rendono visibile il nome della risorsa a terze parti convalidano anche il nome, per garantire che non si tratti di un tentativo di spoofing per un'identità alternativa. |
| location |Variabile |Aree geografiche supportate della risorsa specificata. È possibile selezionare qualsiasi località disponibile, ma è in genere opportuno sceglierne una vicina agli utenti. Di solito è anche opportuno inserire le risorse che interagiscono tra loro nella stessa area. La maggior parte dei tipi di risorsa richiede una posizione, ma alcuni tipi (ad esempio un'assegnazione di ruolo) non la richiedono. Vedere [Impostare la posizione delle risorse nei modelli di Azure Resource Manager](resource-manager-template-location.md). |
| tags |No |Tag associati alla risorsa. Vedere [Applicare i tag alle risorse nei modelli di Azure Resource Manager](resource-manager-template-tags.md). |
| commenti |No |Le note per documentare le risorse nel modello |
| dependsOn |No |Risorse da distribuire prima della distribuzione di questa risorsa. Resource Manager valuta le dipendenze tra le risorse e le distribuisce nell'ordine corretto. Quando le risorse non sono interdipendenti, vengono distribuite in parallelo. Il valore può essere un elenco delimitato da virgole di nomi o identificatori univoci di risorse. Elencare solo le risorse distribuite in questo modello. Le risorse non definite in questo modello devono essere già esistenti. Evitare di aggiungere dipendenze non necessarie perché possono rallentare la distribuzione e creare dipendenze circolari. Per indicazioni sull'impostazione delle dipendenze, vedere l'articolo relativo alla [definizione delle dipendenze nei modelli di Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |No |Impostazioni di configurazione specifiche delle risorse. I valori per l'elemento properties corrispondono esattamente a quelli specificati nel corpo della richiesta per l'operazione API REST (metodo PUT) per creare la risorsa. |
| copy |No |Numero di risorse da creare, se sono necessarie più istanze. Per altre informazioni, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md). |
| resources |No |Risorse figlio che dipendono dalla risorsa in via di definizione. Specificare solo tipi di risorsa consentiti dallo schema della risorsa padre. Il nome di tipo completo della risorsa figlio include il tipo della risorsa padre, ad esempio **Microsoft.Web/sites/extensions**. La dipendenza dalla risorsa padre non è implicita. È necessario definirla in modo esplicito. |

La sezione resources contiene una matrice delle risorse da distribuire. All'interno di ogni risorsa è anche possibile definire una matrice di risorse figlio. La sezione resources potrebbe quindi avere una struttura simile a questa:

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

Per altre informazioni sulla definizione delle risorse figlio, vedere [Impostare il nome e il tipo di una risorsa figlio in un modello di Resource Manager](resource-manager-template-child-resource.md).

## <a name="outputs"></a>outputs
Nella sezione dell'output è possibile specificare i valori restituiti dalla distribuzione. Ad esempio, è possibile restituire l'URI per accedere a una risorsa distribuita.

L'esempio seguente illustra la struttura di una definizione di output:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--- |:--- |:--- |
| outputName |Sì |Nome del valore di output. Deve essere un identificatore JavaScript valido. |
| type |Sì |Tipo del valore di output. I valori di output supportano gli stessi tipi dei parametri di input del modello. |
| value |Sì |Espressione del linguaggio di modello valutata e restituita come valore di output. |

L'esempio seguente illustra un valore che viene restituito nella sezione dell'output.

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

Per altre informazioni sull'utilizzo dell'output, vedere [Condivisione dello stato in modelli di Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per unire più modelli durante la distribuzione, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Potrebbe essere necessario usare le risorse esistenti all'interno di un gruppo di risorse diverso. Questo scenario è comune quando si usano account di archiviazione o reti virtuali condivisi tra più gruppi di risorse. Per altre informazioni, vedere la [funzione resourceId](resource-group-template-functions.md#resourceid).

[deployment2cmdlet]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment

