<properties
   pageTitle="Gestire Azure Data Lake Analytics tramite Azure SDK per Node.js | Azure"
   description="Informazioni su come gestire gli account, le origini dati, i processi e gli utenti di Data Lake Analytics tramite Azure SDK per Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Gestire Azure Data Lake Analytics tramite Azure SDK per Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure SDK per Node. js può essere usato per gestire account, processi e cataloghi di Analisi Azure Data Lake. Per visualizzare l'argomento relativo alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

Attualmente supporta:

  *  **Versione di Node.js: 0.10.0 o successiva**
  *  **Versione dell'API REST per l'account: 2015-10-01-preview**
  *  **Versione dell'API REST per il catalogo: 2015-10-01-preview**
  *  **Versione dell'API REST per il processo: 2016-03-20-preview**

## Funzionalità

- Gestione account: creare, ottenere, elencare, aggiornare ed eliminare.
- Gestione dei processi: inviare, ottenere, elencare, annullare.
- Gestione del catalogo: ottenere, elencare, creare (segreti), aggiornare (segreti), eliminare (segreti).

## Come eseguire l'installazione

```bash
npm install azure-arm-datalake-analytics
```

## Eseguire l'autenticazione con Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Creare il client di Analisi Data Lake

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## Creare un account di Analisi Data Lake

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
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

## Ottenere un elenco di processi

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Ottenere un elenco di database nel catalogo di Analisi Data Lake
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Vedere anche

- [Microsoft Azure SDK per Node.js](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK per Node. js - Gestione dell'Archivio Data Lake](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

<!---HONumber=AcomDC_0914_2016-->