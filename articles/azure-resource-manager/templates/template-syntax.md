---
title: Struttura e sintassi dei modelli
description: Descrive la struttura e le proprietà dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 4e8334e4ddfaee52c5d1aa68fb8689fcde0a6cbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459991"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Comprendere la struttura e la sintassi dei modelli ARM

Questo articolo descrive la struttura di un modello di Azure Resource Manager (ARM). Presenta le diverse sezioni di un modello e le proprietà disponibili in queste sezioni.

Questo articolo è destinato agli utenti che hanno una certa familiarità con i modelli ARM. Fornisce informazioni dettagliate sulla struttura del modello. Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere Esercitazione: Creare e distribuire il primo modello di Azure Resource Manager.For a step-by-step tutorial that guides you through the process of creating a template, see [Tutorial: Create and deploy your first Azure Resource Manager template.](template-tutorial-create-first-template.md)

## <a name="template-format"></a>Formato del modello

La struttura più semplice di un modello è costituita dagli elementi seguenti:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nome dell'elemento | Obbligatoria | Descrizione |
|:--- |:--- |:--- |
| $schema |Sì |Percorso del file di schema JSON che descrive la versione del linguaggio del modello. Il numero di versione utilizzato dipende dall'ambito della distribuzione e dall'editor JSON.<br><br>Se si usa [codice VS con l'estensione degli strumenti](use-vs-code-to-create-template.md)di Azure Resource Manager , usare la versione più recente per le distribuzioni di gruppi di risorse:If you're using VS Code with the Azure Resource Manager tools extension , use the latest version for resource group deployments:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Altri editor (incluso Visual Studio) potrebbero non essere in grado di elaborare questo schema. Per questi editor, utilizzare:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Per le distribuzioni della sottoscrizione, usare: <br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Per le distribuzioni dei gruppi di gestione, usare:For management group deployments, use:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Per le distribuzioni tenant, usare:For tenant deployments, use:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Sì |Versione del modello (ad esempio 1.0.0.0). Questo elemento accetta tutti i valori. Usare questo valore per documentare le modifiche significative al modello. Quando si distribuiscono risorse tramite il modello, è possibile usare questo valore per assicurarsi che venga usato il modello corretto. |
| Profilo api |No | Una versione API che funge da raccolta di versioni API per i tipi di risorsa. Usare questo valore per evitare di dover specificare le versioni API per ogni risorsa nel modello. Quando si specifica una versione del profilo API e non si specifica una versione dell'API per il tipo di risorsa, Resource Manager usa la versione dell'API per il tipo di risorsa definita nel profilo.<br><br>La proprietà del profilo API è particolarmente utile quando si distribuisce un modello in ambienti diversi, ad esempio Azure Stack e Azure globale. Usare la versione del profilo API per assicurarsi che il modello utilizzi automaticamente le versioni supportate in entrambi gli ambienti. Per un elenco delle versioni correnti del profilo API e delle versioni dell'API delle risorse definite nel profilo, vedere [Profilo API.](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)<br><br>Per altre informazioni, vedere [Tenere traccia delle versioni tramite profili API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [Parametri](#parameters) |No |Valori forniti durante la distribuzione per personalizzare la distribuzione di risorse. |
| [Variabili](#variables) |No |Valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello. |
| [Funzioni](#functions) |No |Funzioni definite dall'utente disponibili nel modello. |
| [Risorse](#resources) |Sì |Tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse o sottoscrizione. |
| [Uscite](#outputs) |No |Valori restituiti dopo la distribuzione. |

Ogni elemento ha proprietà che è possibile impostare. In questo articolo le sezioni del modello vengono esaminate in modo dettagliato.

## <a name="parameters"></a>Parametri

Nella sezione parameters del modello si possono specificare i valori che è possibile immettere durante la distribuzione delle risorse. È previsto un limite di 256 parametri in un modello. È possibile ridurre il numero di parametri utilizzando oggetti che contengono più proprietà.

Le proprietà disponibili per un parametro sono:The available properties for a parameter are:

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

| Nome dell'elemento | Obbligatoria | Descrizione |
|:--- |:--- |:--- |
| nome-parametro |Sì |Nome del parametro. Deve essere un identificatore JavaScript valido. |
| type |Sì |Tipo di valore del parametro. I tipi e i valori consentiti sono **string**, **securestring**, **int**, **bool**, **object**, **secureObject** e **array**. Vedere [Tipi di dati](#data-types). |
| defaultValue |No |Valore predefinito per il parametro, se non viene fornito alcun valore per il parametro. |
| allowedValues |No |Matrice di valori consentiti per il parametro per assicurare che venga fornito il valore corretto. |
| minValue |No |Il valore minimo per i parametri di tipo int, questo valore è inclusivo. |
| maxValue |No |Il valore massimo per i parametri di tipo int, questo valore è inclusivo. |
| minLength |No |Lunghezza minima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| maxLength |No |Lunghezza massima per i parametri di tipo string, secureString e array. Questo valore è inclusivo. |
| description |No |Descrizione del parametro visualizzato agli utenti nel portale. Per altre informazioni, consultare la sezione [Comments in templates](#comments) (Commenti nel modello). |

Per esempi su come usare i parametri, vedere Parametri nei modelli di [Azure Resource Manager.](template-parameters.md)

### <a name="data-types"></a>Tipi di dati

Per i numeri interi passati come parametri inline, l'intervallo di valori può essere limitato dall'SDK o dallo strumento da riga di comando utilizzato per la distribuzione. Ad esempio, quando si usa PowerShell per distribuire un modello, i tipi Integer possono essere compresi tra -2147483648 e 2147483647.For example, when using PowerShell to deploy a template, Integer types can range from -2147483648 to 2147483647. Per evitare questa limitazione, specificare valori interi di grandi dimensioni in un [file di parametri](parameter-files.md). I tipi di risorsa applicano i propri limiti per le proprietà Integer.Resource types apply their own limits for integer properties.

Quando si specificano valori booleani e interi nel modello, non racchiudere il valore tra virgolette. Valori di stringa iniziale e finale racchiusi tra virgolette doppie.

Gli oggetti iniziano con una parentesi graffa sinistra e terminano con una parentesi graffa destra. Le matrici iniziano con una parentesi quadra aperta e terminano con una parentesi quadra destra.

Le stringhe sicure e gli oggetti protetti non possono essere letti dopo la distribuzione delle risorse.

Per esempi di formattazione dei tipi di dati, vedere Formati dei tipi di [parametro](parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Variabili

Nella sezione variables è possibile costruire valori da usare in tutto il modello. Non è obbligatorio definire le variabili. Queste tuttavia consentono spesso di semplificare il modello, riducendo le espressioni complesse.

L'esempio seguente mostra le opzioni disponibili per la definizione di una variabile:The following example shows the available options for defining a variable:

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

Per informazioni `copy` sull'utilizzo per creare più valori per una variabile, vedere [Iterazione variabile](copy-variables.md).

Per esempi su come usare le variabili, vedere Variabili nel modello di Azure Resource Manager.For examples of how to use variables, see [Variables in Azure Resource Manager template.](template-variables.md)

## <a name="functions"></a>Funzioni

Nel modello è possibile creare funzioni personalizzate. Tali funzioni sono disponibili per usare il modello. In genere, si definiscono espressioni complesse che non si desidera ripetere in tutto il modello. Le funzioni definite dall'utente vengono create da espressioni e [funzioni](template-functions.md) supportate nei modelli.

Quando si crea una funzione definita dall'utente, è necessario tenere presente alcune restrizioni:

* La funzione non può accedere alle variabili.
* La funzione può usare solo i parametri definiti in essa. Quando si utilizza la [funzione parameters](template-functions-deployment.md#parameters) all'interno di una funzione definita dall'utente, si è limitati ai parametri per tale funzione.
* La funzione non può chiamare altre funzioni definite dall'utente.
* La funzione non può usare la [funzione di riferimento](template-functions-resource.md#reference).
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

| Nome dell'elemento | Obbligatoria | Descrizione |
|:--- |:--- |:--- |
| namespace |Sì |Spazio dei nomi per le funzioni personalizzate. Utilizzare per evitare conflitti di denominazione con le funzioni del modello. |
| nome-funzione |Sì |Nome della funzione personalizzata. Quando si chiama la funzione, combinare il nome della funzione con lo spazio dei nomi. Ad esempio, per chiamare una funzione denominata `"[contoso.uniqueName()]"`uniqueName nello spazio dei nomi contoso, utilizzare . |
| nome-parametro |No |Nome del parametro da utilizzare all'interno della funzione personalizzata. |
| parameter-value |No |Tipo di valore del parametro. I tipi e i valori consentiti sono **string**, **securestring**, **int**, **bool**, **object**, **secureObject** e **array**. |
| tipo di output |Sì |Tipo del valore di output. I valori di output supportano gli stessi tipi dei parametri di input della funzione. |
| valore di output |Sì |Espressione del linguaggio del modello valutata e restituita dalla funzione. |

Per esempi su come usare funzioni personalizzate, vedere Funzioni definite dall'utente nel modello di Azure Resource Manager.For examples of how to use custom functions, see [User-defined functions in Azure Resource Manager template.](template-user-defined-functions.md)

## <a name="resources"></a>Risorse

Nella sezione risorse è possibile definire le risorse da distribuire o aggiornare.

Le risorse vengono definite con la struttura seguente:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
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
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome dell'elemento | Obbligatoria | Descrizione |
|:--- |:--- |:--- |
| condizione | No | Valore booleano che indica se verrà eseguito il provisioning della risorsa durante questa distribuzione. Se `true`, la risorsa viene creata durante la distribuzione. Se `false`, la risorsa viene ignorata per questa distribuzione. Vedere [condizione](conditional-resource-deployment.md). |
| type |Sì |Tipo di risorsa. Questo valore è una combinazione dello spazio dei nomi del provider di risorse e del tipo di risorsa, ad esempio **Microsoft.Storage/storageAccounts**. Per determinare i valori disponibili, vedere riferimento al [modello](/azure/templates/). Per una risorsa figlio, il formato del tipo dipende dal fatto che sia annidato all'interno della risorsa padre o definito all'esterno della risorsa padre. Vedere [Impostare il nome e il tipo per le risorse figlio](child-resource-name-type.md). |
| apiVersion |Sì |Versione dell'API REST da utilizzare per la creazione della risorsa. Per determinare i valori disponibili, vedere riferimento al [modello](/azure/templates/). |
| name |Sì |Nome della risorsa. Il nome deve rispettare le restrizioni dei componenti URI definite dallo standard RFC3986. I servizi di Azure che espongono il nome della risorsa a parti esterne convalidano il nome per assicurarsi che non si tratti di un tentativo di spoofing di un'altra identità. Per una risorsa figlio, il formato del nome dipende dal fatto che sia annidato all'interno della risorsa padre o definito all'esterno della risorsa padre. Vedere [Impostare il nome e il tipo per le risorse figlio](child-resource-name-type.md). |
| comments |No |Le note per documentare le risorse nel modello. Per altre informazioni, consultare la sezione [Comments in templates](template-syntax.md#comments) (Commenti nel modello). |
| posizione |Variabile |Aree geografiche supportate della risorsa specificata. È possibile selezionare qualsiasi località disponibile, ma è in genere opportuno sceglierne una vicina agli utenti. Di solito è anche opportuno inserire le risorse che interagiscono tra loro nella stessa area. La maggior parte dei tipi di risorsa richiede una posizione, ma alcuni tipi (ad esempio un'assegnazione di ruolo) non la richiedono. Consultate [Impostare la posizione delle risorse.](resource-location.md) |
| dependsOn |No |Risorse da distribuire prima della distribuzione di questa risorsa. Resource Manager valuta le dipendenze tra le risorse e le distribuisce nell'ordine corretto. Quando le risorse non sono interdipendenti, vengono distribuite in parallelo. Il valore può essere un elenco delimitato da virgole di nomi o identificatori univoci di risorse. Elencare solo le risorse distribuite in questo modello. Le risorse non definite in questo modello devono essere già esistenti. Evitare di aggiungere dipendenze non necessarie perché possono rallentare la distribuzione e creare dipendenze circolari. Per indicazioni sull'impostazione delle dipendenze, vedere l'articolo relativo alla [definizione delle dipendenze nei modelli di Azure Resource Manager](define-resource-dependency.md). |
| tags |No |Tag associati alla risorsa. Applicare i tag per organizzare in modo logico le risorse nella sottoscrizione. |
| sku | No | Alcune risorse consentono valori che definiscono lo SKU da distribuire. Ad esempio, è possibile specificare il tipo di ridondanza per un account di archiviazione. |
| kind | No | Alcune risorse consentono un valore che definisce il tipo di risorsa distribuito. Ad esempio, è possibile specificare il tipo di Cosmos DB da creare. |
| copy |No |Numero di risorse da creare, se sono necessarie più istanze. La modalità predefinita è parallela. Specificare la modalità seriale quando si desidera che non tutte le risorse vengano distribuite contemporaneamente. Per altre informazioni, vedere [Creare più istanze di risorse in Azure Resource Manager](copy-resources.md). |
| piano | No | Alcune risorse consentono valori che definiscono il piano da distribuire. Ad esempio, è possibile specificare l'immagine del marketplace per una macchina virtuale. |
| properties |No |Impostazioni di configurazione specifiche delle risorse. I valori per l'elemento properties corrispondono esattamente a quelli specificati nel corpo della richiesta per l'operazione API REST (metodo PUT) per creare la risorsa. È inoltre possibile specificare una matrice di copia per creare diverse istanze di una proprietà. Per determinare i valori disponibili, vedere riferimento al [modello](/azure/templates/). |
| resources |No |Risorse figlio che dipendono dalla risorsa in via di definizione. Specificare solo tipi di risorsa consentiti dallo schema della risorsa padre. La dipendenza dalla risorsa padre non è implicita. È necessario definirla in modo esplicito. Vedere [Impostare il nome e il tipo per le risorse figlio](child-resource-name-type.md). |

## <a name="outputs"></a>Output

Nella sezione dell'output è possibile specificare i valori restituiti dalla distribuzione. In genere, si restituiscono valori dalle risorse distribuite.

L'esempio seguente illustra la struttura di una definizione di output:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Nome dell'elemento | Obbligatoria | Descrizione |
|:--- |:--- |:--- |
| nome-output |Sì |Nome del valore di output. Deve essere un identificatore JavaScript valido. |
| condizione |No | Valore booleano che indica se questo valore di output viene restituito. Quando è `true`, il valore è incluso nell'output per la distribuzione. Quando è `false`, il valore dell'output viene ignorato per questa distribuzione. Quando non è specificato, il valore predefinito è `true`. |
| type |Sì |Tipo del valore di output. I valori di output supportano gli stessi tipi dei parametri di input del modello. Se si specifica **securestring** per il tipo di output, il valore non viene visualizzato nella cronologia di distribuzione e non può essere recuperato da un altro modello. Per utilizzare un valore segreto in più di un modello, archiviare il segreto in un insieme di credenziali delle chiavi e fare riferimento al segreto nel file dei parametri. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](key-vault-parameter.md). |
| value |No |Espressione del linguaggio di modello valutata e restituita come valore di output. Specificare **il valore** o la **copia**. |
| copy |No | Utilizzato per restituire più di un valore per un output. Specificare **il valore** o **copiare**. Per altre informazioni, vedere Iterazione dell'output nei modelli di Azure Resource Manager.For more information, see [Output iteration in Azure Resource Manager templates.](copy-outputs.md) |

Per esempi su come usare gli output, vedere Output nel modello di Azure Resource Manager.For examples of how to use [outputs, see Outputs in Azure Resource Manager template.](template-outputs.md)

<a id="comments" />

## <a name="comments-and-metadata"></a>Commenti e metadati

Sono disponibili diverse opzioni per aggiungere commenti e metadati al modello.

### <a name="comments"></a>Commenti

Per i commenti inline, `//` è `/* ... */` possibile utilizzare o ma questa sintassi non funziona con tutti gli strumenti. Non è possibile utilizzare l'editor di modelli del portale per lavorare su modelli con commenti in linea. Se si aggiunge questo stile di commento, assicurarsi che gli strumenti usati supportino i commenti JSON inline.

> [!NOTE]
> Per distribuire modelli con commenti tramite l'interfaccia della riga di comando di Azure, è necessario usare l'opzione. `--handle-extended-json-format`

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

In Visual Studio Code, [l'estensione strumenti](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) di Gestione risorse di Azure può rilevare automaticamente il modello di Resource Manager e modificare la modalità della lingua di conseguenza. Se nell'angolo inferiore destro di Codice VS viene visualizzato **il modello di Azure Resource Manager,** è possibile usare i commenti inline. In questo modo i commenti inline non verranno più contrassegnati come non validi.

![Modalità modello di Visual Studio Code Azure Resource Manager](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadati

È possibile aggiungere un oggetto `metadata` praticamente ovunque nel modello. Resource Manager ignora l'oggetto, ma l'editor JSON potrebbe segnalare che la proprietà non è valida. Nell'oggetto definire le proprietà necessarie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

![Mostra il suggerimento relativo al parametro](./media/template-syntax/show-parameter-tip.png)

Per **resources**, aggiungere un elemento `comments` o un oggetto metadata. L'esempio seguente mostra sia un elemento comments sia un oggetto metadata.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
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

## <a name="multi-line-strings"></a>Stringhe su più righe

È possibile suddividere una stringa in più righe. Ad esempio, vedere la proprietà location e uno dei commenti nell'esempio JSON seguente.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Per distribuire modelli con stringhe su più righe tramite l'interfaccia della riga della riga della riga di comando di Azure, è necessario usare l'opzione. `--handle-extended-json-format`

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](template-functions.md).
* Per unire più modelli durante la distribuzione, vedere [Uso di modelli collegati con Azure Resource Manager](linked-templates.md).
* Per suggerimenti sulla creazione di modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](template-best-practices.md).
* Per consigli su come creare modelli di Resource Manager da poter usare in tutti gli ambienti Azure e in Azure Stack, vedere [I modelli di Azure Resource Manager possono essere sviluppati per la coerenza cloud](templates-cloud-consistency.md).
