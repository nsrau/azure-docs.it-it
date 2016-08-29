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
   ms.date="08/11/2016"
   ms.author="tomfitz"/>  

# Funzioni del modello di Azure Resource Manager

Questo argomento descrive tutte le funzioni disponibili in un modello di Azure Resource Manager.

Le funzioni del modello e i relativi parametri non hanno la distinzione tra maiuscole e minuscole. Ad esempio, Gestione risorse consente di risolvere allo stesso modo le **variables('var1')** e le **VARIABLES('VAR1')**. Durante la valutazione, la funzione mantiene invariato l'uso delle maiuscole/minuscole, a meno che queste non vengano modificate espressamente dalla funzione, ad esempio toUpper o toLower. Alcuni tipi di risorse possono avere requisiti per le maiuscole e minuscole indipendentemente dalla modalità di valutazione delle funzioni.

## Funzioni numeriche

Gestione risorse fornisce le funzioni seguenti per usare i numeri interi:

- [add](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />  
### add

**add(operand1, operand2)**

Restituisce la somma dei due numeri interi forniti.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Primo numero intero da sommare.
| operand2 | Sì | Secondo numero intero da sommare.

L'esempio seguente aggiunge due parametri.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to add"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to add"
        }
      }
    },
    ...
    "outputs": {
      "addResult": {
        "type": "int",
        "value": "[add(parameters('first'), parameters('second'))]"
      }
    }

<a id="copyindex" />  
### copyIndex

**copyIndex(offset)**

Restituisce l'indice corrente di un ciclo di iterazione.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| offset | No | Quantità da aggiungere al valore di iterazione corrente.

Questa funzione viene sempre usata con un oggetto **copy**. Per una descrizione completa dell'uso di **copyIndex**, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md).

L'esempio seguente illustra un ciclo di copy e il valore di indice incluso nel nome.

    "resources": [ 
      { 
        "name": "[concat('examplecopy-', copyIndex())]", 
        "type": "Microsoft.Web/sites", 
        "copy": { 
          "name": "websitescopy", 
          "count": "[parameters('count')]" 
        }, 
        ...
      }
    ]


<a id="div" />  
### div

**div(operand1, operand2)**

Restituisce la divisione Integer dei due numeri interi forniti.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Numero intero che viene diviso.
| operand2 | Sì | Numero intero usato per dividere. Non può essere 0.

L'esempio seguente mostra come dividere un parametro per un altro parametro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "divResult": {
        "type": "int",
        "value": "[div(parameters('first'), parameters('second'))]"
      }
    }

<a id="int" />  
### int

**int(valueToConvert)**

Converte il valore specificato in numero intero.

| Parametro | Obbligatorio | Description
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
| operand1 | Sì | Numero intero che viene diviso.
| operand2 | Sì | Numero intero usato per dividere. Deve essere diverso da 0.

L'esempio seguente restituisce il resto della divisione di un parametro per un altro parametro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "modResult": {
        "type": "int",
        "value": "[mod(parameters('first'), parameters('second'))]"
      }
    }

<a id="mul" />  
### mul

**mul(operand1, operand2)**

Restituisce la moltiplicazione dei due numeri interi forniti.

| Parametro | Obbligatorio | Description
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Primo numero intero da moltiplicare.
| operand2 | Sì | Secondo numero intero da moltiplicare.

L'esempio seguente mostra come moltiplicare un parametro per un altro parametro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to multiply"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to multiply"
        }
      }
    },
    ...
    "outputs": {
      "mulResult": {
        "type": "int",
        "value": "[mul(parameters('first'), parameters('second'))]"
      }
    }

<a id="sub" />
### sub

**sub(operand1, operand2)**

Restituisce la sottrazione dei due numeri interi forniti.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1 | Sì | Numero intero da cui sottrarre.
| operand2 | Sì | Numero intero che viene sottratto.

L'esempio seguente mostra come sottrarre un parametro da un altro parametro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer subtracted from"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer to subtract"
        }
      }
    },
    ...
    "outputs": {
      "subResult": {
        "type": "int",
        "value": "[sub(parameters('first'), parameters('second'))]"
      }
    }

## Funzioni stringa

Gestione risorse fornisce le funzioni seguenti per usare le stringhe:

- [base64](#base64)
- [concat](#concat)
- [length](#lengthstring)
- [padLeft](#padleft)
- [replace](#replace)
- [skip](#skipstring)
- [split](#split)
- [string](#string)
- [substring](#substring)
- [take](#takestring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [Trim](#trim)
- [uniqueString](#uniquestring)
- [Uri](#uri)


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
### Funzione per valori stringa: concat

**concat (string1, string2, string3, ...)**

Combina più valori stringa e restituisce la stringa concatenata.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| string1 | Sì | Valore stringa da concatenare.
| stringhe aggiuntive | No | Valori stringa da concatenare.

Questa funzione può accettare qualsiasi numero di argomenti e può accettare stringhe o matrici per i parametri. Per un esempio di concatenazione di matrici, vedere [Funzione di matrice concat](#concatarray).

L'esempio seguente illustra come combinare più valori di stringa per restituire una stringa concatenata.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />
### Funzione per valori stringa: length

**length(string)**

Restituisce il numero di caratteri in una stringa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringa | Sì | Valore stringa da usare per ottenere il numero di caratteri.

Per un esempio relativo all'uso di length con una matrice, vedere [Funzione di matrice length](#length).

L'esempio seguente restituisce il numero di caratteri in una stringa.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }
        

<a id="padleft" />  
### padLeft

**padLeft(valueToPad, totalLength, paddingCharacter)**

Restituisce una stringa allineata a destra mediante l'aggiunta di caratteri a sinistra, fino a raggiungere la lunghezza totale specificata.
  
| Parametro | Obbligatorio | Description
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
| originalString | Sì | Stringa che sostituisce tutte le istanze di un carattere con un altro carattere.
| oldCharacter | Sì | Carattere da rimuovere dalla stringa originale.
| newCharacter | Sì | Carattere da aggiungere al posto del carattere rimosso.

Nell'esempio seguente viene illustrato come rimuovere tutti i trattini dalla stringa fornita dall'utente.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />
### Funzione per valori stringa: skip
**skip(originalValue, numberToSkip)**

Restituisce una stringa con tutti i caratteri dopo il numero specificato nella stringa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue | Sì | Stringa da usare.
| numberToSkip | Sì | Numero di caratteri da ignorare. Se il valore è minore o uguale a 0, vengono restituiti tutti i caratteri nella stringa. Se è maggiore della lunghezza della stringa, viene restituita una stringa vuota. 

Per un esempio relativo all'uso di skip con una matrice, vedere [Funzione di matrice skip](#skip).

L'esempio seguente ignora il numero specificato di caratteri nella stringa.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for skipping"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }


<a id="split" />  
### split

**split(inputString, delimiterString)**

**split(inputString, delimiterArray)**

Restituisce una matrice di stringhe che contiene le sottostringhe della stringa di input delimitate dai delimitatori specificati.

| Parametro | Obbligatorio | Description
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

L'esempio seguente mostra come suddividere la stringa di input usando una virgola o un punto e virgola.

    "variables": {
      "stringToSplit": "test1,test2;test3",
      "delimiters": [ ",", ";" ]
    },
    "resources": [ ],
    "outputs": {
      "exampleOutput": {
        "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
        "type": "array"
      }
    }

<a id="string" />  
### string

**string(valueToConvert)**

Converte il valore specificato in una stringa.

| Parametro | Obbligatorio | Description
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

<a id="takestring" />
### Funzione per valori stringa: take
**take(originalValue, numberToTake)**

Restituisce una stringa con il numero specificato di caratteri dall'inizio della stringa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue | Sì | Stringa da cui prendere i caratteri.
| numberToTake | Sì | Numero di caratteri da prendere. Se il valore è minore o uguale a 0, viene restituita una stringa vuota. Se è maggiore della lunghezza della stringa specificata, vengono restituiti tutti i caratteri nella stringa.

Per un esempio relativo all'uso di take con una matrice, vedere [Funzione di matrice take](#take).

L'esempio seguente prende il numero specificato di caratteri dalla stringa.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for taking"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[take(parameters('first'), parameters('second'))]"
      }
    }

<a id="tolower" />  
### toLower

**toLower(stringToChange)**

Converte la stringa specificata in caratteri minuscoli.

| Parametro | Obbligatorio | Description
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

| Parametro | Obbligatorio | Description
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

| Parametro | Obbligatorio | Description
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

**uniqueString (baseString, ...)**

Crea una stringa univoca in base ai valori forniti come parametri.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| baseString | Sì | Stringa usata nella funzione hash per creare una stringa univoca.
| parametri aggiuntivi in base alle esigenze | No | È possibile aggiungere tutte le stringhe necessarie per creare il valore che specifica il livello di univocità.

Questa funzione è utile quando è necessario creare un nome univoco per una risorsa. È possibile specificare i valori dei parametri che rappresentano il livello di univocità per il risultato. È possibile specificare se il nome è univoco per la sottoscrizione, il gruppo di risorse o la distribuzione.

Il valore restituito non è una stringa casuale, ma il risultato di una funzione hash. Il valore restituito ha una lunghezza di 13 caratteri. Non è necessariamente univoco a livello globale. È possibile combinare il valore con un prefisso dalla convenzione di denominazione scelta per creare un nome più facile da riconoscere. L'esempio seguente illustra il formato del valore restituito. Naturalmente, il valore effettivo varia in base ai parametri forniti.

    tcvhiyu5h2o5o

Gli esempi seguenti mostrano come usare uniqueString per creare un valore univoco per livelli di uso comune.

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

| Parametro | Obbligatorio | Description
| :--------------------------------: | :------: | :----------
| baseUri | Sì | La stringa URI di base.
| relativeUri | Sì | La stringa URI relativa da aggiungere alla stringa di URI di base.

Il valore per il parametro **baseUri** può includere un file specifico, ma solo il percorso di base viene usato per costruire l'URI. Ad esempio, passare **http://contoso.com/resources/azuredeploy.json** come parametro baseUri restituisce un URI di base **http://contoso.com/resources/**.

Nell'esempio seguente viene illustrato come costruire un collegamento a un modello annidato in base al valore del modello padre.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## Funzioni di matrice

Gestione risorse fornisce diverse funzioni per usare i valori di matrice:

- [concat](#concatarray)
- [length](#length)
- [skip](#skip)
- [take](#take)

Per ottenere una matrice di valori stringa delimitata da un valore, vedere [split](#split).

<a id="concatarray" />
### Funzione di matrice: concat

**concat (array1, array2, array3, ...)**

Combina più matrici e restituisce la matrice concatenata.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| array1 | Sì | Matrice da concatenare.
| matrici aggiuntive | No | Matrici da concatenare.

Questa funzione può accettare qualsiasi numero di argomenti e può accettare stringhe o matrici per i parametri. Per un esempio di concatenazione di valori stringa, vedere la sezione [Funzione per valori stringa concat](#concat).

L'esempio seguente illustra come combinare due matrici.

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
        

<a id="length" />
### Funzione di matrice: length

**length(array)**

Restituisce il numero di elementi in una matrice.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| array | Sì | Matrice da usare per ottenere il numero di elementi.

È possibile usare questa funzione con una matrice per specificare il numero di iterazioni durante la creazione di risorse. Nell'esempio seguente, il parametro **siteNames** fa riferimento a una matrice di nomi da usare durante la creazione di siti Web.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Per altre informazioni sull'uso di questa funzione con una matrice, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

Per un esempio relativo all'uso di length con un valore stringa, vedere la sezione [Funzione per valori stringa length](#lengthstring).

<a id="skip" />  
### Funzione di matrice: skip
**skip(originalValue, numberToSkip)**

Restituisce una matrice con tutti gli elementi dopo il numero specificato nella matrice.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue | Sì | Matrice da usare.
| numberToSkip | Sì | Numero di elementi da ignorare. Se il valore è minore o uguale a 0, vengono restituiti tutti gli elementi nella matrice. Se è maggiore della lunghezza della matrice, viene restituita una matrice vuota. 

Per un esempio relativo all'uso di skip con una stringa, vedere la sezione [Funzione per valori stringa skip](#skipstring).

L'esempio seguente ignora il numero specificato di elementi nella matrice.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for skipping"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'), parameters('second'))]"
      }
    }

<a id="take" />  
### Funzione di matrice: take
**take(originalValue, numberToTake)**

Restituisce una matrice con il numero specificato di elementi dall'inizio della matrice.

| Parametro | Obbligatorio | Description
| :--------------------------------: | :------: | :----------
| originalValue | Sì | Matrice da cui prendere gli elementi.
| numberToTake | Sì | Numero di elementi da prendere. Se il valore è minore o uguale a 0, viene restituita una matrice vuota. Se è maggiore della lunghezza della matrice specificata, vengono restituiti tutti gli elementi nella matrice.

Per un esempio relativo all'uso di take con una stringa, vedere la sezione [Funzione per valori stringa take](#takestring).

L'esempio seguente accetta il numero specificato di elementi dalla matrice.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for taking"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to take"
        }
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

## Funzioni dei valori della distribuzione

Gestione risorse fornisce le funzioni seguenti per ottenere i valori dalle sezioni del modello e i valori relativi alla distribuzione:

- [deployment](#deployment)
- [parameters](#parameters)
- [variables](#variables)

Per ottenere valori da risorse, gruppi di risorse o sottoscrizioni, vedere [Funzioni delle risorse](#resource-functions).

<a id="deployment" />
### deployment

**deployment()**

Restituisce informazioni sull'operazione di distribuzione corrente.

Questa funzione restituisce l'oggetto che viene passato durante la distribuzione. Le proprietà nell'oggetto restituito variano a seconda che l'oggetto di distribuzione venga passato come un collegamento o come un oggetto inline.

Quando l'oggetto di distribuzione viene passato inline, ad esempio quando si usa il parametro **-TemplateFile** in Azure PowerShell per puntare a un file locale, l'oggetto restituito è nel formato seguente:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
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
                "uri": ""
            },
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            },
            "parameters": {},
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

| Parametro | Obbligatorio | Description
| :--------------------------------: | :------: | :----------
| variable Name | Sì | Nome della variabile da restituire.

L'esempio seguente usa un valore della variabile.

    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
      }
    ],

## Funzioni delle risorse

Gestione risorse fornisce le funzioni seguenti per ottenere i valori delle risorse:

- [listKeys e list{Value}](#listkeys)
- [provider](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [sottoscrizione](#subscription)

Per ottenere valori dai parametri, dalle variabili o dalla distribuzione corrente, vedere [Funzioni dei valori della distribuzione](#deployment-value-functions).

<a id="listkeys" /> <a id="list" />
### listKeys e list{Value}

**listKeys (resourceName or resourceIdentifier, [apiVersion])**

**list{Value} (resourceName o resourceIdentifier, apiVersion)**

Restituisce i valori per qualsiasi tipo di risorsa che supporta l'operazione di tipo elenco. L'uso più comune è rappresentato da **listKeys**.
  
| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sì | Identificatore univoco della risorsa.
| apiVersion | Sì | Versione dell'API dello stato di runtime della risorsa.

Qualsiasi operazione che inizia con **list** può essere usata come funzione nel modello. Le operazioni disponibili non includono solo **listKeys**, ma anche operazioni come **list**, **listAdminKeys** e **listStatus**. Per determinare quali tipi di risorse dispongono di un'operazione list, usare il comando PowerShell seguente.

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

In alternativa, è possibile recuperare l'elenco con l'interfaccia della riga di comando di Azure. L'esempio seguente mostra come recuperare tutte le operazioni per **apiapps** e usa l'utilità JSON [jq](http://stedolan.github.io/jq/download/) per filtrare solo le operazioni list.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

È possibile specificare resourceId usando la [funzione resourceId](./#resourceid) o il formato **{providerNamespace}/{resourceType}/{resourceName}**.

L'esempio seguente mostra come restituire le chiavi primaria e secondaria da un account di archiviazione nella sezione outputs.

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

L'oggetto restituito da listKeys è nel formato seguente:

    {
      "keys": [
        {
          "keyName": "key1",
          "permissions": "Full",
          "value": "{value}"
        },
        {
          "keyName": "key2",
          "permissions": "Full",
          "value": "{value}"
        }
      ]
    }

<a id="providers" />  
### provider

**providers (providerNamespace, [resourceType])**

Restituisce informazioni su un provider di risorse e i relativi tipi di risorse supportati. Se non si specifica un tipo di risorsa, la funzione restituisce tutti i tipi supportati per il provider di risorse.

| Parametro | Obbligatorio | Description
| :--------------------------------: | :------: | :----------
| providerNamespace | Sì | Spazio dei nomi del provider
| resourceType | No | Il tipo di risorsa all'interno dello spazio dei nomi specificato.

Ogni tipo supportato viene restituito nel formato seguente: L'ordinamento della matrice non è garantito.

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

Restituisce un oggetto che rappresenta lo stato di runtime di un'altra risorsa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sì | Nome o identificatore univoco di una risorsa.
| apiVersion | No | Versione dell'API della risorsa specificata. Includere questo parametro quando non viene effettuato il provisioning della risorsa nello stesso modello.

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
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
			"type" : "object"
		}
	}

È possibile recuperare un valore specifico dall'oggetto restituito, ad esempio l'URI dell'endpoint BLOB, come illustrato nell'esempio seguente.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

L'esempio seguente fa riferimento a un account di archiviazione in un gruppo di risorse diverso.

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Le proprietà nell'oggetto restituito variano in base al tipo di risorsa.

<a id="resourcegroup" />  
### resourceGroup

**resourceGroup()**

Restituisce un oggetto che rappresenta il gruppo di risorse corrente.

L'oggetto restituito è nel formato seguente:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
      "tags": {
      },
      "properties": {
        "provisioningState": "{status}"
      }
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

Restituisce l'identificatore univoco di una risorsa.
      
| Parametro | Obbligatorio | Descrizione
| :---------------: | :------: | :----------
| subscriptionId | No | Il valore predefinito è la sottoscrizione corrente. Specificare questo valore quando si vuole recuperare una risorsa in un'altra sottoscrizione.
| resourceGroupName | No | Il valore predefinito è il gruppo di risorse corrente. Specificare questo valore quando si vuole recuperare una risorsa in un altro gruppo di risorse.
| resourceType | Sì | Tipo di risorsa, incluso lo spazio dei nomi del provider di risorse.
| resourceName1 | Sì | Nome della risorsa.
| resourceName2 | No | Segmento successivo del nome della risorsa se la risorsa è annidata.

Questa funzione viene usata quando il nome della risorsa è ambiguo o non è stato sottoposto a provisioning all'interno dello stesso modello. L'identificatore viene restituito nel formato seguente:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

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
### sottoscrizione

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

<!---HONumber=AcomDC_0817_2016-->