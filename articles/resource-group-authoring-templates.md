<properties
   pageTitle="Creazione di modelli di Gestione risorse di Azure"
   description="Creare modelli di Gestione risorse di Azure usando la sintassi dichiarativa JSON per distribuire applicazioni ad Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>


<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="tomfitz"/>


# Creazione di modelli di Gestione risorse di Azure

Le applicazioni Azure richiedono in genere una combinazione di risorse (ad esempio un server di database, un database o un sito Web) per raggiungere gli obiettivi desiderati. Anziché distribuire e gestire separatamente ogni risorsa, è possibile creare un modello di Gestione risorse di Azure che distribuisce e fornisce tutte le risorse per l'applicazione in un'unica operazione coordinata. Nel modello vengono definite le risorse necessarie per l'applicazione e vengono specificati i parametri di distribuzione per immettere valori nei diversi ambienti di distribuzione. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione.

In questo argomento vengono descritte le sezioni del modello. Per gli schemi effettivi, vedere [Schemi di Gestione risorse di Azure](https://github.com/Azure/azure-resource-manager-schemas).

## Formato del modello

L'esempio seguente illustra le sezioni che compongono la struttura di base di un modello.

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
| $schema | Sì | Percorso del file di schema JSON che descrive la versione del linguaggio del modello.
| contentVersion | Sì | Versione del modello (ad esempio 1.0.0.0). Quando si distribuiscono risorse tramite il modello, è possibile usare questo valore per assicurarsi che venga usato il modello corretto.
| parameters | No | Valori forniti durante la distribuzione per personalizzare la distribuzione di risorse.
| variables | No | Valori usati come frammenti JSON nel modello per semplificare le espressioni di linguaggio del modello.
| resources | Sì | Tipi di servizi che vengono distribuiti o aggiornati in un gruppo di risorse.
| outputs | No | Valori restituiti dopo la distribuzione.

Più avanti in questo argomento verranno esaminate le sezioni del modello in maggiore dettaglio. Per ora, verrà descritta parte della sintassi che compone il modello.

## Espressioni e funzioni

La sintassi di base del modello è JSON. Tuttavia, espressioni e funzioni estendono il codice JSON disponibile nel modello e consentono di creare valori diversi da quelli strettamente letterali. Le espressioni sono racchiuse tra parentesi quadre ([ e ]) e vengono valutate al momento della distribuzione del modello. Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON e restituiscono sempre un altro valore JSON. Se è necessario usare una stringa letterale che inizia con una parentesi quadra [, usare due parentesi quadre [[.

Solitamente, si usano espressioni con funzioni per eseguire operazioni per la configurazione della distribuzione. Proprio come in JavaScript, le chiamate di funzione sono formattate come **functionName(arg1,arg2,arg3)**. Per i riferimenti alle proprietà si usano il punto e gli operatori [index].

Nell'elenco seguente vengono riportate le funzioni comuni.

- **parameters(parameterName)**

    Restituisce un valore di parametro che viene fornito al momento dell'esecuzione della distribuzione.

- **variables(variableName)**

    Restituisce una variabile che viene definita nel modello.

- **concat(arg1,arg2,arg3,...)**

    Combina più valori di stringa. Questa funzione può accettare qualsiasi numero di argomenti.

- **base64(inputString)**

    Restituisce la rappresentazione base64 della stringa di input.

- **resourceGroup()**

    Restituisce un oggetto strutturato (dotato di proprietà ID, nome e percorso) che rappresenta il gruppo di risorse corrente.

- **resourceId([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

    Restituisce l'identificatore univoco di una risorsa. Può essere usato per recuperare risorse da un altro gruppo di risorse.

L'esempio seguente illustra come usare diverse funzioni per la costruzione di valori:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Per ora, si hanno a disposizione conoscenze sufficienti sulle espressioni e sulle funzioni per comprendere le sezioni del modello. Per informazioni più dettagliate su tutte le funzioni dei modelli, inclusi i parametri e il formato dei valori restituiti, vedere [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md).


## Parametri

Nella sezione dei parametri del modello è possibile specificare i valori che un utente può immettere durante la distribuzione delle risorse. È possibile usare questi valori dei parametri in tutto il modello per impostare valori per le risorse distribuite. Solo i parametri dichiarati nella sezione dei parametri possono essere usati in altre sezioni del modello.

All'interno della sezione dei parametri non è possibile usare un valore di parametro per creare un altro valore di parametro. Questo tipo di operazione viene solitamente eseguita nella sezione delle variabili.

I parametri vengono definiti con la struttura seguente:

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ]
       }
    }

| Nome dell'elemento | Obbligatorio | Descrizione
| :------------: | :------: | :----------
| parameterName | Sì | Nome del parametro. Deve essere un identificatore JavaScript valido.
| type | Sì | Tipo di valore del parametro. Vedere di seguito l'elenco dei tipi consentiti.
| defaultValue | No | Valore predefinito per il parametro, se non viene fornito alcun valore per il parametro.
| allowedValues | No | Matrice di valori consentiti per il parametro per assicurare che venga fornito il valore corretto.

I valori e i tipi consentiti sono:

- string o secureString: tutte le stringhe JSON valide
- int: tutti i valori integer JSON validi
- bool: tutti i valori booleani JSON validi
- object o secureObject - tutti gli oggetti JSON sono validi
- array: tutte le matrici JSON valide


>[AZURE.NOTE]Per tutte le password, le chiavi e altre informazioni riservate si consiglia di usare il tipo **secureString**. Non è possibile leggere i parametri di modello di tipo secureString dopo la distribuzione delle risorse.

Il seguente esempio mostra come definire i parametri:

    "parameters": {
       "siteName": {
          "type": "string"
       },
       "siteLocation": {
          "type": "string"
       },
       "hostingPlanName": {
          "type": "string"
       },  
       "hostingPlanSku": {
          "type": "string",
          "allowedValues": [
            "Free",
            "Shared",
            "Basic",
            "Standard",
            "Premium"
          ],
          "defaultValue": "Free"
       }
    }

## Variabili

Nella sezione delle variabili è possibile creare valori da usare per semplificare le espressioni di linguaggio del modello. Solitamente, queste variabili sono basate sui valori forniti dai parametri.

Nell'esempio seguente viene illustrato come definire una variabile creata da due valori di parametro:

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
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
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount"
    }

## Risorse

Nella sezione delle risorse è possibile definire le risorse che vengono distribuite o aggiornate.

Le risorse vengono definite con la struttura seguente:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-dependent-resources>"
         ]
       }
    ]

| Nome dell'elemento | Obbligatorio | Descrizione
| :----------------------: | :------: | :----------
| apiVersion | Sì | Versione dell'API che supporta la risorsa. Per le versioni disponibili e gli schemi per le risorse, vedere[Schemi di Gestione risorse di Azure](https://github.com/Azure/azure-resource-manager-schemas).
| type | Sì | Tipo di risorsa. Questo valore è una combinazione dello spazio dei nomi del provider di risorse e del tipo di risorsa che supporta il provider di risorse.
| name | Sì | Nome della risorsa. Il nome deve rispettare le restrizioni dei componenti URI definite dallo standard RFC3986.
| location | No | Aree geografiche supportate della risorsa specificata.
| tags | No | Tag associati alla risorsa.
| dependsOn | No | Risorse da cui dipende la risorsa in via di definizione. Le dipendenze tra risorse vengono valutate e le risorse vengono distribuite in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, si cerca di distribuirle in parallelo. Il valore può essere un elenco delimitato da virgole di nomi di risorse o di identificatori univoci di risorse.
| properties | No | Impostazioni di configurazione specifiche delle risorse.
| resources | No | Risorse figlio che dipendono dalla risorsa in via di definizione.

Se il nome della risorsa non è univoco, è possibile usare la funzione di supporto **resourceId** (descritta di seguito) per ottenere l'identificatore univoco per qualsiasi risorsa.

L'esempio seguente illustra una risorsa **Microsoft.Web/serverfarms** e una risorsa **Microsoft.Web/sites** con una risorsa **Extensions** nidificata:

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
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

## Scenari più avanzati
Questo argomento offre un'analisi iniziale del modello. Tuttavia, il proprio scenario potrebbe richiedere attività più avanzate.

Potrebbe essere necessario unire due modelli o usare un modello figlio all'interno di un modello padre. Per altre informazioni, vedere [Uso di modelli collegati con Gestione risorse di Azure](resource-group-linked-templates.md).

Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

Potrebbe essere necessario usare le risorse esistenti all'interno di un gruppo di risorse diverso. Questo è comune quando si usano account di archiviazione o reti virtuali condivisi tra più gruppi di risorse. Per altre informazioni, vedere la [funzione resourceId](../resource-group-template-functions#resourceid).

## Modello completo
Il modello seguente distribuisce un'app Web e fornisce il codice da un file con estensione zip.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
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
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## Passaggi successivi
- Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Gestione risorse di Azure](resource-group-template-functions.md)
- Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](azure-portal/resource-group-template-deploy.md)
- Per un esempio dettagliato di distribuzione di un'applicazione, vedere [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- Per visualizzare gli schemi disponibili, vedere [Schemi di Gestione risorse di Azure](https://github.com/Azure/azure-resource-manager-schemas)

<!---HONumber=August15_HO6-->