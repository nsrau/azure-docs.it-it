<properties
   pageTitle="Creazione di modelli di Azure Resource Manager | Microsoft Azure"
   description="Creare modelli di Gestione risorse di Azure usando la sintassi dichiarativa JSON per distribuire applicazioni ad Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Creazione di modelli di Gestione risorse di Azure

In un modello di Azure Resource Manager si definiscono le risorse da distribuire per una soluzione e si specificano i parametri e le variabili che consentono di immettere i valori per diversi ambienti. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. In questo argomento vengono descritte le sezioni del modello.

Visual Studio fornisce strumenti per la creazione di modelli. Per altre informazioni sull'utilizzo di Visual Studio con i modelli, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

Per indicazioni sulla creazione di un modello, vedere [Procedura dettagliata per un modello di Azure Resource Manager](resource-manager-template-walkthrough.md).

## Pianificare il modello

Prima di iniziare a creare il modello, è consigliabile stabilire cosa distribuire e a quale uso sarà destinato il modello. In particolare, è opportuno prendere in considerazione:

1. Tipi di risorse da distribuire
2. Località in cui dovranno risiedere tali risorse
3. Versione dell'API del provider di risorse da usare durante la distribuzione delle risorse
4. Eventuale ordine di distribuzione delle risorse
5. Valori da passare durante la distribuzione e valori da definire direttamente nel modello
6. Eventuale necessità di valori restituiti dalla distribuzione

Per scoprire quali tipi di risorse sono disponibili per la distribuzione, quali aree sono supportate per i tipi e le versioni di API disponibili per ogni tipo, vedere l'articolo relativo a [provider, aree, versioni API e schemi di Gestione risorse](resource-manager-supported-services.md).

È necessario limitare le dimensioni del modello a 1 MB e ogni file dei parametri a 64 KB. Il limite di 1 MB si applica allo stato finale del modello dopo che è stato espanso con le definizioni delle risorse iterative e i valori di variabili e parametri.

## Formato del modello

La struttura più semplice di un modello è costituita dagli elementi seguenti.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nome dell'elemento | Obbligatorio | Descrizione
| :------------: | :------: | :----------
| $schema | Sì | Percorso del file di schema JSON che descrive la versione del linguaggio del modello. È consigliabile usare l'URL indicato in precedenza.
| contentVersion | Sì | Versione del modello (ad esempio 1.0.0.0). Questo elemento accetta tutti i valori. Quando si distribuiscono risorse tramite il modello, è possibile usare questo valore per assicurarsi che venga usato il modello corretto.
| parameters | No | Valori forniti durante la distribuzione per personalizzare la distribuzione di risorse.
| variables | No | Valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello.
| resources | Sì | Tipi di risorse che vengono distribuite o aggiornate in un gruppo di risorse.
| outputs | No | Valori restituiti dopo la distribuzione.

Più avanti in questo argomento verranno esaminate le sezioni del modello in maggiore dettaglio. Per ora, verrà descritta parte della sintassi che compone il modello.

## Espressioni e funzioni

La sintassi di base del modello è JSON. Tuttavia, espressioni e funzioni estendono il codice JSON disponibile nel modello e consentono di creare valori diversi da quelli strettamente letterali. Le espressioni sono racchiuse tra parentesi quadre [ e ] e vengono valutate al momento della distribuzione del modello. Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON e restituiscono sempre un altro valore JSON. Se è necessario usare una stringa letterale che inizia con una parentesi quadra [, usare due parentesi quadre [[.

Solitamente, si usano espressioni con funzioni per eseguire operazioni per la configurazione della distribuzione. Proprio come in JavaScript, le chiamate di funzione sono formattate come **functionName(arg1,arg2,arg3)**. Per i riferimenti alle proprietà si usano il punto e gli operatori [index].

L'esempio seguente illustra come usare diverse funzioni al momento di costruite i valori:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Per l’elenco completo delle funzioni del modello, vedere [Funzioni del modello di Gestione risorse di Azure](resource-group-template-functions.md).


## Parametri

Nella sezione parameters del modello si possono specificare i valori che è possibile immettere durante la distribuzione delle risorse. I valori dei parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente, ad esempio sviluppo, test e produzione. Non è obbligatorio specificare i parametri nel modello, ma senza di essi il modello distribuisce sempre le stesse risorse con lo stesso nome, località e proprietà.

È possibile usare questi valori dei parametri in tutto il modello per impostare valori per le risorse distribuite. Solo i parametri dichiarati nella sezione dei parametri possono essere usati in altre sezioni del modello.

I parametri vengono definiti con la struttura seguente:

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| Nome dell'elemento | Obbligatorio | Descrizione
| :------------: | :------: | :----------
| parameterName | Sì | Nome del parametro. Deve essere un identificatore JavaScript valido.
| type | Sì | Tipo di valore del parametro. Vedere di seguito l'elenco dei tipi consentiti.
| defaultValue | No | Valore predefinito per il parametro, se non viene fornito alcun valore per il parametro.
| allowedValues | No | Matrice di valori consentiti per il parametro per assicurare che venga fornito il valore corretto.
| minValue | No | Il valore minimo per i parametri di tipo int, questo valore è inclusivo.
| maxValue | No | Il valore massimo per i parametri di tipo int, questo valore è inclusivo.
| minLength | No | La lunghezza minima per la stringa, i parametri di tipo secureString e matrice, questo valore è inclusivo.
| maxLength | No | La lunghezza massima per la stringa, i parametri di tipo secureString e matrice, questo valore è inclusivo.
| description | No | Descrizione del parametro che verrà mostrato agli utenti del modello tramite l'interfaccia del modello personalizzato del portale.

I valori e i tipi consentiti sono:

- string o secureString: tutte le stringhe JSON valide
- int: tutti i valori integer JSON validi
- bool: tutti i valori booleani JSON validi
- object o secureObject - tutti gli oggetti JSON sono validi
- array: tutte le matrici JSON valide

Per specificare un parametro come facoltativo, fornire un valore defaultValue (che può essere anche una stringa vuota).

Se si specifica un nome di parametro corrispondente a uno dei parametri nel comando per la distribuzione del modello, ad esempio un parametro denominato **ResourceGroupName** nel modello che corrisponde al parametro **ResourceGroupName** nel cmdlet [New AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx), verrà richiesto di specificare un valore per un parametro con il suffisso **FromTemplate**, ad esempio **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione non attribuendo ai parametri lo stesso nome dei parametri usati per operazioni di distribuzione.

>[AZURE.NOTE] Per tutte le password, le chiavi e altre informazioni riservate si consiglia di usare il tipo **secureString**. Non è possibile leggere i parametri di modello di tipo secureString dopo la distribuzione delle risorse.

Il seguente esempio mostra come definire i parametri:

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

Per informazioni sull'immissione di valori di parametro durante la distribuzione vedere [Distribuire le risorse con i modelli di Azure Resource Manager](resource-group-template-deploy.md#parameter-file).

## Variabili

Nella sezione variables è possibile costruire valori da usare in tutto il modello. Solitamente, queste variabili sono basate sui valori forniti dai parametri. Non è obbligatorio definire le variabili, che però permettono spesso di semplificare il modello riducendo le espressioni complesse.

Le variabili vengono definite con la struttura seguente:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

Nell'esempio seguente viene illustrato come definire una variabile creata da due valori di parametro:

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

Nell'esempio successivo viene illustrata una variabile che rappresenta un tipo JSON complesso e variabili create da altre variabili:

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

## Risorse

Nella sezione delle risorse è possibile definire le risorse che vengono distribuite o aggiornate. È qui che il modello può diventare più complicato, perché per fornire i valori corretti è necessario conoscere i tipi da distribuire. Per informazioni utili sui provider di risorse, vedere [Provider, aree, versioni API e schemi di Gestione risorse](resource-manager-supported-services.md).

Le risorse vengono definite con la struttura seguente:

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
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nome dell'elemento | Obbligatorio | Descrizione
| :----------------------: | :------: | :----------
| apiVersion | Sì | Versione dell'API REST da utilizzare per la creazione della risorsa. Per determinare i numeri di versione disponibili per un determinato tipo, vedere [Versioni dell'API supportate](resource-manager-supported-services.md#supported-api-versions).
| type | Sì | Tipo di risorsa. Questo valore è una combinazione dello spazio dei nomi del provider di risorse e del tipo di risorsa che supporta il provider di risorse.
| name | Sì | Nome della risorsa. Il nome deve rispettare le restrizioni dei componenti URI definite dallo standard RFC3986. I servizi Azure che rendono visibile il nome della risorsa a terze parti convalidano anche il nome, per garantire che non si tratti di un tentativo di spoofing per un'identità alternativa. Vedere [Controllare il nome della risorsa](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| location | Variabile | Aree geografiche supportate della risorsa specificata. Per determinare le posizioni disponibili vedere [Aree supportate](resource-manager-supported-services.md#supported-regions). La maggior parte dei tipi di risorsa richiede una posizione, ma alcuni tipi (ad esempio un'assegnazione di ruolo) non la richiedono.
| tags | No | Tag associati alla risorsa.
| commenti | No | Le note per documentare le risorse nel modello
| dependsOn | No | Risorse da cui dipende la risorsa in via di definizione. Le dipendenze tra risorse vengono valutate e le risorse vengono distribuite in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, si cerca di distribuirle in parallelo. Il valore può essere un elenco delimitato da virgole di nomi di risorse o di identificatori univoci di risorse.
| properties | No | Impostazioni di configurazione specifiche delle risorse. I valori per l'elemento properties corrispondono esattamente a quelli forniti nel corpo della richiesta per l'operazione API REST (metodo PUT) per creare la risorsa. Per collegamenti alla documentazione dello schema di risorse o all'API REST, vedere [Provider, aree, versioni API e schemi di Gestione risorse](resource-manager-supported-services.md).
| resources | No | Risorse figlio che dipendono dalla risorsa in via di definizione. È possibile specificare solo i tipi di risorse consentiti dallo schema della risorsa padre. Il nome completo del tipo di risorsa figlio include il tipo di risorsa padre, ad esempio **Microsoft.Web/sites/extensions**. La dipendenza dalla risorsa padre non è implicita, è necessario definirla in modo esplicito. 


Se il nome della risorsa non è univoco, è possibile usare la funzione di supporto **resourceId** (descritta di seguito) per ottenere l'identificatore univoco per qualsiasi risorsa.

La sezione resources contiene una matrice delle risorse da distribuire. All'interno di ogni risorsa, è anche possibile definire una matrice delle relative risorse figlio. La sezione resources potrebbe quindi avere una struttura simile a questa:

    "resources": [
       {
           "name": "resourceA",
           ...
       },
       {
           "name": "resourceB",
           ...
           "resources": [
               {
                   "name": "firstChildResourceB",
                   ...
               },
               {   
                   "name": "secondChildResourceB",
                   ...
               }
           ]
       },
       {
           "name": "resourceC",
           ...
       }
    ]



L'esempio seguente illustra una risorsa **Microsoft.Web/serverfarms** e una risorsa **Microsoft.Web/sites** con una risorsa figlio **Extensions**: Si noti che il sito è contrassegnato come dipendente nella server farm perché per poter distribuire il sito deve esistere la server farm. Si noti anche che la risorsa **Extensions** è un elemento figlio del sito.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "ARM"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## Output

Nella sezione dell'output è possibile specificare i valori restituiti dalla distribuzione. Ad esempio, è possibile restituire l'URI per accedere a una risorsa distribuita.

L'esempio seguente illustra la struttura di una definizione di output:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| Nome dell'elemento | Obbligatorio | Descrizione
| :------------: | :------: | :----------
| outputName | Sì | Nome del valore di output. Deve essere un identificatore JavaScript valido.
| type | Sì | Tipo del valore di output. I valori di output supportano gli stessi tipi dei parametri di input del modello.
| value | Sì | Espressione di linguaggio del modello che verrà valutata e restituita come valore di output.


L'esempio seguente illustra un valore che viene restituito nella sezione dell'output.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## Passaggi successivi
- Per visualizzare modelli completi per molti tipi diversi di soluzioni vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
- Per informazioni dettagliate sulle funzioni che è possibile usare in un modello vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
- Per unire più modelli durante la distribuzione vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
- Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
- Potrebbe essere necessario usare le risorse esistenti all'interno di un gruppo di risorse diverso. Questo è comune quando si usano account di archiviazione o reti virtuali condivisi tra più gruppi di risorse. Per altre informazioni, vedere la [funzione resourceId](resource-group-template-functions.md#resourceid).

<!---HONumber=AcomDC_0615_2016-->