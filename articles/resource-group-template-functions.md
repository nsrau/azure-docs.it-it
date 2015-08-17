<properties
   pageTitle="Funzioni del modello di Gestione risorse di Azure"
   description="Vengono descritte le funzioni da utilizzare in un modello di gestione risorse di Azure per recuperare valori, stringhe di formato e informazioni sulla distribuzione."
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
   ms.date="07/27/2015"
   ms.author="tomfitz"/>

# Funzioni del modello di Gestione risorse di Azure

Questo argomento descrive tutte le funzioni che è possibile usare in un modello di Gestione risorse di Azure.

## base64

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

## concat

**concat (arg1, arg2, arg3, ...)**

Combina più valori di stringa e restituisce il valore di stringa risultante. Questa funzione può accettare qualsiasi numero di argomenti.

L'esempio seguente mostra come combinare più valori per restituirne uno solo.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## copyIndex

**copyIndex(offset)**

Restituisce l'indice corrente di un ciclo di iterazione. Per esempio di uso di questa funzione, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

## deployment

**deployment()**

Restituisce informazioni sull'operazione di distribuzione corrente.

Le informazioni sulla distribuzione vengono restituite come oggetto con le seguenti proprietà:

    {
      "name": "",
      "properties": {
        "template": {},
        "parameters": {},
        "mode": "",
        "provisioningState": ""
      }
    }

L'esempio seguente mostra come restituire le informazioni di distribuzione nella sezione dell’output.

    "outputs": {
      "exampleOutput": {
        "value": "[deployment()]",
        "type" : "object"
      }
    }

## listKeys

**listKeys (resourceName or resourceIdentifier, [apiVersion])**

Restituisce le chiavi di un account di archiviazione. È possibile specificare resourceId usando la [funzione resourceId](./#resourceid) o il formato **providerNamespace/resourceType/resourceName**. È possibile usare la funzione per ottenere i valori primaryKey e secondaryKey.
  
| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sì | Identificatore univoco di un account di archiviazione.
| apiVersion | Sì | Versione dell'API dello stato di runtime della risorsa.

L'esempio seguente mostra come restituire le chiavi da un account di archiviazione nella sezione outputs.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## padLeft

**padLeft(stringToPad, totalLength, paddingCharacter)**

Restituisce una stringa allineata a destra mediante l'aggiunta di caratteri a sinistra, fino a raggiungere la lunghezza totale specificata.
  
| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToPad | Sì | Stringa allineata a destra.
| totalLength | Sì | Numero totale di caratteri della stringa restituita.
| paddingCharacter | Sì | Il carattere da utilizzare per la spaziatura interna a sinistra, fino a raggiungere la lunghezza totale.

Nell'esempio seguente viene illustrato come il valore del parametro fornito dall'utente viene completato aggiungendo il carattere zero finché la stringa non raggiunge i 10 caratteri. Se il valore del parametro originale è più lungo di 10 caratteri, non vengono aggiunti caratteri.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }


## parameters

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

## provider

**provider (providerNamespace, [resourceType])**

Restituisce informazioni su un provider di risorse e i relativi tipi di risorse supportati. Se non viene specificato un tipo, vengono restituiti tutti i tipi supportati.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| providerNamespace | Sì | Spazio dei nomi del provider
| resourceType | No | Il tipo di risorsa all'interno dello spazio dei nomi specificato.

Ogni tipo supportato viene restituito nel formato seguente:

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

## reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

Consente a un'espressione di derivare il valore dallo stato di runtime di un'altra risorsa.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sì | Nome o identificatore univoco di una risorsa.
| apiVersion | No | Versione dell'API dello stato di runtime della risorsa. Il parametro deve essere usato se il provisioning della risorse non viene eseguito all'interno dello stesso modello.

La funzione **reference** deriva il proprio valore da uno stato di runtime, quindi non può essere usata nella sezione variables. Può essere usata, invece, nella sezione outputs di un modello.

Usando l'espressione di riferimento, si dichiara implicitamente che una risorsa dipende da un'altra se il provisioning della risorsa cui si fa riferimento viene eseguito all'interno dello stesso modello. Non è necessario usare anche la proprietà **dependsOn**. L'espressione non viene valutata fino a quando la risorsa cui si fa riferimento ha completato la distribuzione.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## replace

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

## resourceGroup

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

## resourceId

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

Restituisce l'identificatore univoco di una risorsa. Questa funzione viene usata quando il nome della risorsa è ambiguo o non è stato sottoposto a provisioning all'interno dello stesso modello. L'identificatore viene restituito nel formato seguente:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Parametro | Obbligatorio | Descrizione
| :---------------: | :------: | :----------
| resourceGroupName | No | Nome del gruppo di risorse facoltativo. Il valore predefinito è il gruppo di risorse corrente. Specificare questo valore quando si recupera una risorsa in un altro gruppo di risorse.
| resourceType | Sì | Tipo di risorsa, incluso lo spazio dei nomi del provider di risorse.
| resourceName1 | Sì | Nome della risorsa.
| resourceName2 | No | Segmento successivo del nome della risorsa se la risorsa è annidata.

L'esempio seguente mostra come recuperare gli ID risorsa per un sito Web e un database. Il sito Web si trova in un gruppo di risorse denominato **myWebsitesGroup**, mentre il database si trova nel gruppo di risorse corrente per questo modello.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
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


## sottoscrizione

**subscription()**

Restituisce informazioni dettagliate sulla sottoscrizione nel formato seguente.

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

L'esempio seguente mostra la funzione subscription chiamata nella sezione outputs.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 

## toLower

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

## toUpper

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


## variables

**variables (variableName)**

Restituisce il valore della variabile. Il nome della variabile specificato deve essere definito nella sezione variables del modello.

| Parametro | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| variable Name | Sì | Nome della variabile da restituire.


## Passaggi successivi
- Per una descrizione delle sezioni in un modello di Gestione risorse di Azure, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
- Per unire più modelli, vedere [Uso di modelli collegati con Gestione risorse di Azure](resource-group-linked-templates.md)
- Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md)
- Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](azure-portal/resource-group-template-deploy.md)

<!---HONumber=August15_HO6-->