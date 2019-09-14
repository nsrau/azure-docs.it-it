---
title: Struttura e sintassi del modello di Azure Resource Manger | Documentazione Microsoft
description: Descrive la struttura e le proprietà dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 4a5c1a99911c31f539d4f55adefb2c5f06243dd0
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984087"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Comprendere la struttura e la sintassi dei modelli di Azure Resource Manger

Questo articolo descrive la struttura di un modello di Azure Resource Manager. Presenta le diverse sezioni di un modello e le proprietà disponibili in queste sezioni.

Questo articolo è destinato agli utenti che hanno familiarità con i modelli Gestione risorse. Fornisce informazioni dettagliate sulla struttura del modello. Per un'introduzione alla creazione di un modello, vedere [Azure Resource Manager Templates](template-deployment-overview.md).

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
| apiProfile |No | Versione API che funge da raccolta di versioni API per i tipi di risorse. Usare questo valore per evitare di dover specificare le versioni API per ogni risorsa nel modello. Quando si specifica una versione del profilo API e non si specifica una versione API per il tipo di risorsa, Gestione risorse usa la versione dell'API per quel tipo di risorsa definito nel profilo.<br><br>La proprietà del profilo API è particolarmente utile quando si distribuisce un modello in ambienti diversi, ad esempio Azure Stack e Azure globale. Usare la versione del profilo API per assicurarsi che il modello usi automaticamente le versioni supportate in entrambi gli ambienti. Per un elenco delle versioni correnti del profilo API e delle versioni API delle risorse definite nel profilo, vedere [profilo API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Per altre informazioni, vedere [tenere traccia delle versioni usando i profili API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |No |Valori forniti durante la distribuzione per personalizzare la distribuzione di risorse. |
| [variables](#variables) |No |Valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello. |
| [funzioni](#functions) |No |Funzioni definite dall'utente disponibili nel modello. |
| [resources](#resources) |Sì |Tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse o sottoscrizione. |
| [uscite](#outputs) |No |Valori restituiti dopo la distribuzione. |

Ogni elemento ha proprietà che è possibile impostare. In questo articolo le sezioni del modello vengono esaminate in modo dettagliato.

## <a name="parameters"></a>Parametri

Nella sezione parameters del modello si possono specificare i valori che è possibile immettere durante la distribuzione delle risorse. È previsto un limite di 256 parametri in un modello. È possibile ridurre il numero di parametri utilizzando oggetti che contengono più proprietà.

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

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--- |:--- |:--- |
| Nome parametro |Sì |Nome del parametro. Deve essere un identificatore JavaScript valido. |
| type |Sì |Tipo di valore del parametro. I tipi e i valori consentiti sono **string**, **securestring**, **int**, **bool**, **object**, **secureObject** e **array**. |
| defaultValue |No |Valore predefinito per il parametro, se non viene fornito alcun valore per il parametro. |
| allowedValues |No |Matrice di valori consentiti per il parametro per assicurare che venga fornito il valore corretto. |
| minValue |No |Il valore minimo per i parametri di tipo int, questo valore è inclusivo. |
| maxValue |No |Il valore massimo per i parametri di tipo int, questo valore è inclusivo. |
| minLength |No |Lunghezza minima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| maxLength |No |Lunghezza massima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| description |No |Descrizione del parametro visualizzato agli utenti nel portale. Per altre informazioni, consultare la sezione [Comments in templates](#comments) (Commenti nel modello). |

Per esempi relativi all'uso dei parametri, vedere [parametri nei modelli Azure Resource Manager](template-parameters.md).

## <a name="variables"></a>Variabili

Nella sezione variables è possibile costruire valori da usare in tutto il modello. Non è obbligatorio definire le variabili. Queste tuttavia consentono spesso di semplificare il modello, riducendo le espressioni complesse.

Nell'esempio seguente vengono illustrate le opzioni disponibili per la definizione di una variabile:

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

Per informazioni sull'uso `copy` di per creare più valori per una variabile, vedere [iterazione delle variabili](resource-group-create-multiple.md#variable-iteration).

Per esempi relativi all'uso delle variabili, vedere [variabili nel modello di Azure Resource Manager](template-variables.md).

## <a name="functions"></a>Funzioni

Nel modello è possibile creare funzioni personalizzate. Tali funzioni sono disponibili per usare il modello. Si definiscono in genere espressioni complesse che non si vogliono ripetere in tutto il modello. Le funzioni definite dall'utente vengono create da espressioni e [funzioni](resource-group-template-functions.md) supportate nei modelli.

Quando si crea una funzione definita dall'utente, è necessario tenere presente alcune restrizioni:

* La funzione non può accedere alle variabili.
* La funzione può usare solo i parametri definiti in essa. Quando si usa la [funzione Parameters](resource-group-template-functions-deployment.md#parameters) in una funzione definita dall'utente, si è limitati ai parametri della funzione.
* La funzione non può chiamare altre funzioni definite dall'utente.
* La funzione non può usare la [funzione di riferimento](resource-group-template-functions-resource.md#reference).
* I parametri della funzione non possono avere valori predefiniti.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--- |:--- |:--- |
| namespace |Yes |Spazio dei nomi per le funzioni personalizzate. Usare per evitare conflitti di denominazione con le funzioni di modello. |
| Nome funzione |Sì |Nome della funzione personalizzata. Quando si chiama la funzione, combinare il nome della funzione con lo spazio dei nomi. Ad esempio, per chiamare una funzione denominata UniqueName nello spazio dei nomi contoso, `"[contoso.uniqueName()]"`usare. |
| Nome parametro |No |Nome del parametro da usare all'interno della funzione personalizzata. |
| valore parametro |No |Tipo di valore del parametro. I tipi e i valori consentiti sono **string**, **securestring**, **int**, **bool**, **object**, **secureObject** e **array**. |
| tipo di output |Yes |Tipo del valore di output. I valori di output supportano gli stessi tipi dei parametri di input della funzione. |
| valore di output |Sì |Espressione del linguaggio del modello valutata e restituita dalla funzione. |

Per esempi relativi all'uso di funzioni personalizzate, vedere [funzioni definite dall'utente in Azure Resource Manager modello](template-user-defined-functions.md).

## <a name="resources"></a>Risorse

Nella sezione risorse è possibile definire le risorse da distribuire o aggiornare.

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

| Nome dell'elemento | Obbligatorio | Descrizione |
|:--- |:--- |:--- |
| condition | No | Valore booleano che indica se verrà eseguito il provisioning della risorsa durante questa distribuzione. Se `true`, la risorsa viene creata durante la distribuzione. Se `false`, la risorsa viene ignorata per questa distribuzione. Vedere [Condition](conditional-resource-deployment.md). |
| apiVersion |Yes |Versione dell'API REST da utilizzare per la creazione della risorsa. Per determinare i valori disponibili, vedere [riferimento ai modelli](/azure/templates/). |
| type |Sì |Tipo di risorsa. Questo valore è una combinazione dello spazio dei nomi del provider di risorse e del tipo di risorsa, ad esempio **Microsoft.Storage/storageAccounts**. Per determinare i valori disponibili, vedere [riferimento ai modelli](/azure/templates/). Per una risorsa figlio, il formato del tipo dipende dal fatto che sia annidato all'interno della risorsa padre o definito all'esterno della risorsa padre. Vedere [Impostare il nome e il tipo per le risorse figlio](child-resource-name-type.md). |
| name |Sì |Nome della risorsa. Il nome deve rispettare le restrizioni dei componenti URI definite dallo standard RFC3986. I servizi di Azure che espongono il nome della risorsa alle parti esterne convalidano il nome per assicurarsi che non sia un tentativo di falsificare un'altra identità. Per una risorsa figlio, il formato del nome dipende dal fatto che sia annidato all'interno della risorsa padre o definito all'esterno della risorsa padre. Vedere [Impostare il nome e il tipo per le risorse figlio](child-resource-name-type.md). |
| location |Variabile |Aree geografiche supportate della risorsa specificata. È possibile selezionare qualsiasi località disponibile, ma è in genere opportuno sceglierne una vicina agli utenti. Di solito è anche opportuno inserire le risorse che interagiscono tra loro nella stessa area. La maggior parte dei tipi di risorsa richiede una posizione, ma alcuni tipi (ad esempio un'assegnazione di ruolo) non la richiedono. Vedere [set Resource Location](resource-location.md). |
| tags |No |Tag associati alla risorsa. Applicare i tag per organizzare in modo logico le risorse nella sottoscrizione. |
| commenti |No |Le note per documentare le risorse nel modello. Per altre informazioni, consultare la sezione [Commenti in template](resource-group-authoring-templates.md#comments). |
| copia |No |Numero di risorse da creare, se sono necessarie più istanze. La modalità predefinita è parallela. Specificare la modalità seriale quando si desidera che non tutte le risorse vengano distribuite contemporaneamente. Per altre informazioni, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |No |Risorse da distribuire prima della distribuzione di questa risorsa. Resource Manager valuta le dipendenze tra le risorse e le distribuisce nell'ordine corretto. Quando le risorse non sono interdipendenti, vengono distribuite in parallelo. Il valore può essere un elenco delimitato da virgole di nomi o identificatori univoci di risorse. Elencare solo le risorse distribuite in questo modello. Le risorse non definite in questo modello devono essere già esistenti. Evitare di aggiungere dipendenze non necessarie perché possono rallentare la distribuzione e creare dipendenze circolari. Per indicazioni sull'impostazione delle dipendenze, vedere l'articolo relativo alla [definizione delle dipendenze nei modelli di Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |No |Impostazioni di configurazione specifiche delle risorse. I valori per l'elemento properties corrispondono esattamente a quelli specificati nel corpo della richiesta per l'operazione API REST (metodo PUT) per creare la risorsa. È inoltre possibile specificare una matrice di copia per creare diverse istanze di una proprietà. Per determinare i valori disponibili, vedere [riferimento ai modelli](/azure/templates/). |
| sku | No | Alcune risorse consentono valori che definiscono lo SKU da distribuire. Ad esempio, è possibile specificare il tipo di ridondanza per un account di archiviazione. |
| kind | No | Alcune risorse consentono un valore che definisce il tipo di risorsa distribuito. Ad esempio, è possibile specificare il tipo di Cosmos DB da creare. |
| piano | No | Alcune risorse consentono valori che definiscono il piano da distribuire. Ad esempio, è possibile specificare l'immagine del marketplace per una macchina virtuale. | 
| risorse |No |Risorse figlio che dipendono dalla risorsa in via di definizione. Specificare solo tipi di risorsa consentiti dallo schema della risorsa padre. La dipendenza dalla risorsa padre non è implicita. È necessario definirla in modo esplicito. Vedere [Impostare il nome e il tipo per le risorse figlio](child-resource-name-type.md). |

## <a name="outputs"></a>Output

Nella sezione dell'output è possibile specificare i valori restituiti dalla distribuzione. In genere, i valori vengono restituiti dalle risorse distribuite.

L'esempio seguente illustra la struttura di una definizione di output:

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nome dell'elemento | Obbligatorio | DESCRIZIONE |
|:--- |:--- |:--- |
| nome di output |Sì |Nome del valore di output. Deve essere un identificatore JavaScript valido. |
| condition |No | Valore booleano che indica se questo valore di output viene restituito. Quando è `true`, il valore è incluso nell'output per la distribuzione. Quando è `false`, il valore dell'output viene ignorato per questa distribuzione. Quando non è specificato, il valore predefinito è `true`. |
| type |Yes |Tipo del valore di output. I valori di output supportano gli stessi tipi dei parametri di input del modello. Se si specifica **SecureString** per il tipo di output, il valore non viene visualizzato nella cronologia di distribuzione e non può essere recuperato da un altro modello. Per usare un valore segreto in più di un modello, archiviare il segreto in un Key Vault e fare riferimento al segreto nel file dei parametri. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](resource-manager-keyvault-parameter.md). |
| value |Yes |Espressione del linguaggio di modello valutata e restituita come valore di output. |

Per esempi relativi all'uso degli output, vedere [output nel modello di Azure Resource Manager](template-outputs.md).

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

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per unire più modelli durante la distribuzione, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per suggerimenti sulla creazione di modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](template-best-practices.md).
* Per consigli su come creare modelli di Resource Manager da poter usare in tutti gli ambienti Azure e in Azure Stack, vedere [I modelli di Azure Resource Manager possono essere sviluppati per la coerenza cloud](templates-cloud-consistency.md).
