<properties
   pageTitle="Funzioni del modello di gestione risorse | Microsoft Azure"
   description="Vengono descritte le funzioni da utilizzare in un modello di gestione risorse di Azure per recuperare valori, lavorare con stringhe e valori numerici, e recuperare informazioni sulla distribuzione."
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
   ms.date="06/16/2016"
   ms.author="tomfitz"/>

# Funzioni del modello di Gestione risorse di Azure

Questo argomento descrive tutte le funzioni che è possibile usare in un modello di Gestione risorse di Azure.

Le funzioni del modello e i relativi parametri non hanno la distinzione tra maiuscole e minuscole. Ad esempio, Gestione risorse consente di risolvere allo stesso modo le **variables('var1')** e le **VARIABLES('VAR1')**. Durante la valutazione, la distinzione tra maiuscole e minuscole sarà mantenuta, a meno che non venga modificata espressamente dalla funzione (ad esempio toUpper o toLower). Alcuni tipi di risorse possono avere requisiti per le maiuscole e minuscole indipendentemente dalla modalità di valutazione delle funzioni.

## Funzioni numeriche

Gestione risorse fornisce le funzioni seguenti per usare i numeri interi:

- [aggiungere](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [length](#length)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### aggiungere

**add(operand1, operand2)**

Restituisce la somma dei due numeri interi forniti.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Primo operando da usare.
| operand2 | Sì | Secondo operando da usare.


<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

Restituisce l'indice corrente di un ciclo di iterazione.

Questa funzione viene sempre usata con un oggetto **copy**. Per esempi di uso di **copyIndex**, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).


<a id="div" />
### div

**div(operand1, operand2)**

Restituisce la divisione Integer dei due numeri interi forniti.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Numero che viene diviso.
| operand2 | Sì | Numero usato per dividere, deve essere diverso da 0.


<a id="int" />
### int

**int(valueToConvert)**

Converte il valore specificato in numero intero.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| valueToConvert | Sì | Il valore da convertire in numero intero. Il tipo di valore può essere solo Stringa o Numero Intero.

Nell'esempio seguente il valore del parametro fornito dall'utente viene convertito in numero intero.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

Restituisce la parte rimanente della divisione Integer usando i due numeri interi forniti.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Numero che viene diviso.
| operand2 | Sì | Numero usato per dividere, deve essere diverso da 0.



<a id="mul" />
### mul

**mul(operand1, operand2)**

Restituisce la moltiplicazione dei due numeri interi forniti.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Primo operando da usare.
| operand2 | Sì | Secondo operando da usare.


<a id="sub" />
### sub

**sub(operand1, operand2)**

Restituisce la sottrazione dei due numeri interi forniti.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Numero da cui sottrarre.
| operand2 | Sì | Numero da sottrarre.


## Funzioni stringa

Gestione risorse fornisce le funzioni seguenti per usare le stringhe:

- [base64](#base64)
- [concat](#concat)
- [length](#length)
- [padLeft](#padleft)
- [replace](#replace)
- [split](#split)
- [string](#string)
- [substring](#substring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [Trim](#trim)
- [uniqueString](#uniquestring)
- [Uri](#uri)

Per ottenere il numero di caratteri in una stringa o in una matrice, vedere [length](#length).

<a id="base64" />
### base64

**base64 (inputString)**

Restituisce la rappresentazione base64 della stringa di input.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| inputString | Sì | Il valore di stringa da restituire come rappresentazione base64.

L'esempio seguente mostra come usare la funzione base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat

**concat (arg1, arg2, arg3, ...)**

Combina più valori e restituisce il risultato concatenato. Questa funzione può accettare qualsiasi numero di argomenti e può accettare stringhe o matrici per i parametri.

L'esempio seguente illustra come combinare più valori di stringa per restituire una stringa concatenata.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

L'esempio successivo illustra come combinare due matrici.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="padleft" />
### padLeft

**padLeft(valueToPad, totalLength, paddingCharacter)**

Restituisce una stringa allineata a destra mediante l'aggiunta di caratteri a sinistra, fino a raggiungere la lunghezza totale specificata.
  
| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| valueToPad | Sì | Stringa o numero intero allineato a destra.
| totalLength | Sì | Numero totale di caratteri della stringa restituita.
| paddingCharacter | No | Il carattere da utilizzare per la spaziatura interna a sinistra, fino a raggiungere la lunghezza totale. Il valore predefinito è uno spazio.

Nell'esempio seguente viene illustrato come il valore del parametro fornito dall'utente viene completato aggiungendo il carattere zero finché la stringa non raggiunge i 10 caratteri. Se il valore del parametro originale è più lungo di 10 caratteri, non vengono aggiunti caratteri.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### replace

**replace(originalString, oldCharacter, newCharacter)**

Restituisce una nuova stringa con tutte le istanze di un carattere della stringa specificata sostituito con un altro carattere.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalString | Sì | Stringa che disporrà di tutte le istanze di un carattere sostituito da un altro carattere.
| oldCharacter | Sì | Carattere da rimuovere dalla stringa originale.
| newCharacter | Sì | Carattere da aggiungere al posto del carattere rimosso.

Nell'esempio seguente viene illustrato come rimuovere tutti i trattini dalla stringa fornita dall'utente.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="split" />
### split

**split(inputString, delimiter)** **split(inputString, [delimiters])**

Restituisce una matrice di stringhe che contiene le sottostringhe della stringa di input che sono delimitate dai delimitatori inviati.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| inputString | Sì | Stringa da dividere.
| delimiter | Sì | Delimitatore da usare, può essere una stringa singola o una matrice di stringhe.

Nell'esempio seguente la stringa di input viene divisa con una virgola.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

<a id="string" />
### string

**string(valueToConvert)**

Converte il valore specificato in una stringa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| valueToConvert | Sì | Valore da convertire in stringa. È possibile convertire qualsiasi tipo di valore, inclusi gli oggetti e le matrici.

Nell'esempio seguente il valore del parametro fornito dall'utente viene convertito in stringhe.

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />
### substring

**substring (stringToParse, startIndex, lenght)**

Restituisce una sottostringa che inizia nella posizione del carattere specificato e contiene il numero di caratteri specificato.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToParse | Sì | La stringa originale da cui estrarre la sottostringa.
| startIndex | No | La posizione del carattere iniziale in base zero della sottostringa.
| length | No | Il numero di caratteri della sottostringa.

Nell'esempio seguente si estraggono i primi tre caratteri da un parametro.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="tolower" />
### toLower

**toLower(stringToChange)**

Converte la stringa specificata in caratteri minuscoli.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToChange | Sì | Stringa da convertire in lettere minuscole.

Nell'esempio seguente il valore del parametro fornito dall'utente viene convertito in lettere minuscole.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### toUpper

**toUpper(stringToChange)**

Converte la stringa specificata in lettere maiuscole.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToChange | Sì | Stringa da convertire in lettere maiuscole.

Nell'esempio seguente il valore del parametro fornito dall'utente viene convertito in lettere maiuscole.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### Trim

**trim (stringToTrim)**

Rimuove tutti i caratteri di spazi vuoti iniziali e finali dalla stringa specificata.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToTrim | Sì | La stringa da eliminare.

Nell'esempio seguente vengono eliminati i caratteri spazi vuoti dal valore del parametro fornito dall'utente.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (stringForCreatingUniqueString, ...)**

Crea una stringa univoca in base ai valori forniti come parametri. Questa funzione è utile quando è necessario creare un nome univoco per una risorsa. È possibile specificare i valori dei parametri che rappresentano il livello di univocità per il risultato. È possibile specificare se il nome è univoco per la sottoscrizione, il gruppo di risorse o la distribuzione.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | Sì | Stringa di base usata nella funzione hash per creare una stringa univoca.
| parametri aggiuntivi in base alle esigenze | No | È possibile aggiungere tutte le stringhe necessarie per creare il valore che specifica il livello di univocità.

Il valore restituito non è una stringa casuale, ma il risultato di una funzione hash. Il valore restituito ha una lunghezza di 13 caratteri. Non è necessariamente univoco a livello globale. È possibile combinare il valore con un prefisso dalla convenzione di denominazione scelta per creare un nome più facile da riconoscere.

Negli esempi seguenti viene illustrato come usare uniqueString per creare un valore univoco per diversi livelli di uso comune.

Valore univoco basato su una sottoscrizione

    "[uniqueString(subscription().subscriptionId)]"

Valore univoco basato su un gruppo di risorse

    "[uniqueString(resourceGroup().id)]"

Valore univoco basato su una distribuzione per un gruppo di risorse

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
Nell'esempio seguente viene illustrato come creare un nome univoco per un account di archiviazione in base al gruppo di risorse.

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...

<a id="uri" />
### Uri

**uri (baseUri, relativeUri)**

Crea un URI assoluto combinando la baseUri e la stringa relativeUri.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| baseUri | Sì | La stringa URI di base.
| relativeUri | Sì | La stringa URI relativa da aggiungere alla stringa di URI di base.

Il valore per il parametro **baseUri** può includere un file specifico, ma solo il percorso di base viene usato per costruire l'URI. Ad esempio, passare **http://contoso.com/resources/azuredeploy.json** come parametro baseUri restituirà un URI di base **http://contoso.com/resources/**.

Nell'esempio seguente viene illustrato come costruire un collegamento a un modello annidato in base al valore del modello padre.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## Funzioni di matrice

Gestione risorse fornisce diverse funzioni per usare i valori di matrice:

- [concat](#concat)
- [length](#length)
- [take](#take)
- [skip](#skip)
- [split](#split)

<a id="length" />
### length

**lunghezza (matrice o stringa)**

Restituisce il numero di elementi in una matrice o il numero di caratteri in una stringa. È possibile usare questa funzione con una matrice per specificare il numero di iterazioni durante la creazione di risorse. Nell'esempio seguente, il parametro **siteNames** fa riferimento a una matrice di nomi da usare durante la creazione di siti Web.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Per altre informazioni sull'uso di questa funzione con una matrice, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

In alternativa, è possibile utilizzarla con una stringa:

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }

<a id="take" />
### take
**take(originalValue, numberToTake)**

Restituisce una matrice o una stringa con il numero specificato di elementi o i caratteri dall'inizio della matrice o stringa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue | Sì | La matrice o stringa da cui accettare gli elementi o i caratteri.
| numberToTake | Sì | Il numero di elementi o caratteri da accettare. Se questo valore è 0 o minore, viene restituita una matrice o una stringa vuota. Se è maggiore della lunghezza della matrice o della stringa specificata, vengono restituiti tutti gli elementi nella matrice o stringa.

L'esempio seguente accetta il numero specificato di elementi dalla matrice.

    "parameters": {
      "first": {
        "type": "array",
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int"
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

<a id="skip" />
### skip
**skip(originalValue, numberToSkip)**

Restituisce una matrice o una stringa con tutti gli elementi o i caratteri dopo il numero specificato nella matrice o stringa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue | Sì | La matrice o stringa da usare per ignorare gli elementi o i caratteri.
| numberToSkip | Sì | Il numero di elementi o caratteri da ignorare. Se questo valore è 0 o minore, vengono restituiti tutti gli elementi nella matrice o stringa. Se è maggiore della lunghezza della matrice o stringa, viene restituita una matrice o stringa vuota. 

L'esempio seguente ignora il numero specificato di elementi nella matrice.

    "parameters": {
      "first": {
        "type": "array",
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int"
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }

## Funzioni dei valori della distribuzione

Gestione risorse fornisce le funzioni seguenti per ottenere i valori dalle sezioni del modello e i valori relativi alla distribuzione:

- [deployment](#deployment)
- [parameters](#parameters)
- [variables](#variables)

Per ottenere i valori dalle risorse, dai gruppi di risorse o dalle sottoscrizioni, vedere [Funzioni delle risorse](#resource-functions).

<a id="deployment" />
### deployment

**deployment()**

Restituisce informazioni sull'operazione di distribuzione corrente.

Questa funzione restituisce l'oggetto che viene passato durante la distribuzione. Le proprietà nell'oggetto restituito varieranno in base a se l'oggetto di distribuzione viene passato come un collegamento o come un oggetto inline. Quando l'oggetto di distribuzione viene passato inline, come quando si usa il parametro **- TemplateFile** in Azure PowerShell per puntare a un file locale, l'oggetto restituito è nel formato seguente:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Quando l'oggetto viene passato come collegamento, come quando si usa il parametro **- TemplateUri** per puntare a un oggetto remoto, l'oggetto viene restituito nel formato seguente.

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": "",
                "contentVersion": ""
            },
            "mode": "",
            "provisioningState": ""
        }
    }

Nell'esempio seguente viene illustrato come utilizzare la distribuzione () per collegarsi a un altro modello in base all'URI del modello padre.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  


<a id="parameters" />
### parameters

**parameters (parameterName)**

Restituisce un valore di parametro. Il nome del parametro specificato deve essere definito nella sezione parameters del modello.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| parameterName | Sì | Nome del parametro da restituire.

L'esempio seguente mostra un uso semplificato della funzione parameters.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### variables

**variables (variableName)**

Restituisce il valore della variabile. Il nome della variabile specificato deve essere definito nella sezione variables del modello.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| variable Name | Sì | Nome della variabile da restituire.



## Funzioni delle risorse

Gestione risorse fornisce le funzioni seguenti per ottenere i valori delle risorse:

- [listkeys](#listkeys)
- [list*](#list)
- [provider](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [sottoscrizione](#subscription)

Per ottenere i valori dai parametri, dalle variabili o dalla distribuzione corrente, vedere [Funzioni dei valori della distribuzione](#deployment-value-functions).

<a id="listkeys" />
### listKeys

**listKeys (resourceName or resourceIdentifier, [apiVersion])**

Restituisce le chiavi per qualsiasi tipo di risorsa che supporta l'operazione listKeys. È possibile specificare resourceId usando la [funzione resourceId](./#resourceid) o il formato **providerNamespace/resourceType/resourceName**. È possibile usare la funzione per ottenere i valori primaryKey e secondaryKey.
  
| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sì | Identificatore univoco della risorsa.
| apiVersion | Sì | Versione dell'API dello stato di runtime della risorsa.

L'esempio seguente mostra come restituire le chiavi da un account di archiviazione nella sezione outputs.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

<a id="list" />
### list*

**list* (resourceName or resourceIdentifier, apiVersion)**

Qualsiasi operazione che inizia con **list** può essere usata come funzione nel modello. Queste operazioni includono **listKeys**, come illustrato in precedenza, ma anche operazioni quali **list**, **listAdminKeys** e **listStatus**. Per chiamare la funzione, usare il nome effettivo della funzione, non list*. Per determinare quali tipi di risorse dispongono di un'operazione list, usare il comando PowerShell seguente.

    PS C:\> Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

In alternativa, è possibile recuperare l'elenco con l'interfaccia della riga di comando di Azure. L'esempio seguente recupera tutte le operazioni per **apiapps** e usa l'utilità JSON [jq](http://stedolan.github.io/jq/download/) per filtrare solo le operazioni list.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

<a id="providers" />
### provider

**providers (providerNamespace, [resourceType])**

Restituisce informazioni su un provider di risorse e i relativi tipi di risorse supportati. Se non viene specificato un tipo, vengono restituiti tutti i tipi supportati.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| providerNamespace | Sì | Spazio dei nomi del provider
| resourceType | No | Il tipo di risorsa all'interno dello spazio dei nomi specificato.

Ogni tipo supportato viene restituito nel formato seguente. Non è garantito l'ordine della matrice:

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

L'esempio seguente mostra come usare la funzione provider:

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

<a id="reference" />
### reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

Consente a un'espressione di derivare il valore dallo stato di runtime di un'altra risorsa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sì | Nome o identificatore univoco di una risorsa.
| apiVersion | No | Versione dell'API della risorsa specificata. È necessario includere questo parametro quando non viene effettuato il provisioning della risorsa nello stesso modello.

La funzione **reference** deriva il proprio valore da uno stato di runtime, quindi non può essere usata nella sezione variables. Può essere usata, invece, nella sezione outputs di un modello.

Usando la funzione di riferimento, si dichiara implicitamente che una risorsa dipende da un'altra se il provisioning della risorsa cui si fa riferimento viene effettuato nello stesso modello. Non è necessario usare anche la proprietà **dependsOn**. La funzione non viene valutata fino a quando la risorsa cui si fa riferimento ha completato la distribuzione.

L'esempio seguente fa riferimento a un account di archiviazione distribuito nello stesso modello.

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

L'esempio seguente fa riferimento a un account di archiviazione non distribuito nello stesso modello, ma esistente nello stesso gruppo di risorse delle risorse da distribuire.

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15')]",
			"type" : "object"
		}
	}

È possibile recuperare un particolare valore dall'oggetto restituito, ad esempio l'URI dell'endpoint BLOB, come illustrato di seguito.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

L'esempio seguente fa riferimento a un account di archiviazione in un gruppo di risorse diverso.

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

Restituisce un oggetto strutturato che rappresenta il gruppo di risorse corrente. L'oggetto sarà nel seguente formato:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

L'esempio seguente usa il percorso del gruppo di risorse per assegnare il percorso per un sito Web.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### resourceId

**resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

Restituisce l'identificatore univoco di una risorsa. Questa funzione viene usata quando il nome della risorsa è ambiguo o non è stato sottoposto a provisioning all'interno dello stesso modello. L'identificatore viene restituito nel formato seguente:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Parametro | Obbligatorio | Descrizione
| :---------------: | :------: | :----------
| subscriptionId | No | ID sottoscrizione facoltativo. Il valore predefinito è la sottoscrizione corrente. Specificare questo valore quando si recupera una risorsa in un'altra sottoscrizione.
| resourceGroupName | No | Nome del gruppo di risorse facoltativo. Il valore predefinito è il gruppo di risorse corrente. Specificare questo valore quando si recupera una risorsa in un altro gruppo di risorse.
| resourceType | Sì | Tipo di risorsa, incluso lo spazio dei nomi del provider di risorse.
| resourceName1 | Sì | Nome della risorsa.
| resourceName2 | No | Segmento successivo del nome della risorsa se la risorsa è annidata.

L'esempio seguente mostra come recuperare gli ID risorsa per un sito Web e un database. Il sito Web si trova in un gruppo di risorse denominato **myWebsitesGroup**, mentre il database si trova nel gruppo di risorse corrente per questo modello.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
Spesso è necessario usare questa funzione quando si usa un account di archiviazione o una rete virtuale in un gruppo di risorse alternative. L'account di archiviazione o la rete virtuale possono essere usati in più gruppi di risorse, quindi non devono essere eliminati quando si elimina un singolo gruppo di risorse. L'esempio seguente mostra come usare facilmente una risorsa di un gruppo di risorse esterno:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### subscription

**subscription()**

Restituisce informazioni dettagliate sulla sottoscrizione nel formato seguente.

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

L'esempio seguente mostra la funzione subscription chiamata nella sezione outputs.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## Passaggi successivi
- Per una descrizione delle sezioni in un modello di Gestione risorse di Azure, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md)
- Per unire più modelli, vedere [Uso di modelli collegati con Gestione risorse di Azure](resource-group-linked-templates.md)
- Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
- Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md)

<!---HONumber=AcomDC_0622_2016-->