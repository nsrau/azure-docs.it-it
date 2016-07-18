<properties 
   pageTitle="Gestire archivi Azure Data Lake mediante Azure SDK per Node.js | Microsoft Azure"
   description="Informazioni su come gestire gli account Archivio Data Lake e il file system." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/21/2016"
   ms.author="nitinme"/>

# Gestire Archivio Azure Data Lake utilizzando Azure SDK per Node.js

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [SDK per Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)


L’SDK di Azure per Node.js può essere usato per gestire account Archivio Azure Data Lake e anche operazioni del file system:

Attualmente supporta:

  *  **Versione di Node.js: 0.10.0 o successiva**
  *  **Versione dell'API REST per l'account: 2015-10-01-preview**
  *  **Versione dell'API REST per FileSystem: 2015-10-01-anteprima**

## Funzionalità

- Gestione account: creare, ottenere, elencare, aggiornare ed eliminare.
- Gestione file system: creare, ottenere, caricare, aggiungere, scaricare, leggere, eliminare, elencare.

## Come eseguire l'installazione

```bash
npm install azure-arm-datalake-store
```

## Eseguire l'autenticazione con Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Creare client di Analisi Data Lake

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, 'your-subscription-id');
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials, 'azuredatalakestore.net');
```

## Creare un account Archivio Data Lake

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Creare un file con contenuto
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.filesystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## Ottenere un elenco di file e cartelle

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.filesystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Vedere anche

- [Microsoft Azure SDK per Node.js](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK per Node. js - Gestione di Analisi Data Lake](https://www.npmjs.com/package/azure-arm-datalake-analytics)

<!---HONumber=AcomDC_0706_2016-->