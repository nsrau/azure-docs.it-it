---
title: Gestire Azure Data Lake Analytics con Azure SDK per Node.js | Documentazione Microsoft
description: Informazioni su come gestire gli account, le origini dati, i processi e gli utenti di Data Lake Analytics tramite Azure SDK per Node.js
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 10ec0b63e1867f5f62d30d879a4497c0880d9494


---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Gestire Azure Data Lake Analytics tramite Azure SDK per Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure SDK per Node. js può essere usato per gestire account, processi e cataloghi di Analisi Azure Data Lake. Per visualizzare l'argomento relativo alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

Attualmente supporta:

* **Versione di Node.js: 0.10.0 o successiva**
* **Versione dell'API REST per l'account: 2015-10-01-preview**
* **Versione dell'API REST per il catalogo: 2015-10-01-preview**
* **Versione dell'API REST per il processo: 2016-03-20-preview**

## <a name="features"></a>Funzionalità
* Gestione account: creare, ottenere, elencare, aggiornare ed eliminare.
* Gestione dei processi: inviare, ottenere, elencare, annullare.
* Gestione del catalogo: ottenere, elencare, creare (segreti), aggiornare (segreti), eliminare (segreti).

## <a name="how-to-install"></a>Come eseguire l'installazione
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Eseguire l'autenticazione con Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Creare il client di Analisi Data Lake
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake
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

## <a name="get-a-list-of-jobs"></a>Ottenere un elenco di processi
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

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Ottenere un elenco di database nel catalogo di Analisi Data Lake
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

## <a name="see-also"></a>Vedere anche
* [Microsoft Azure SDK per Node.js](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK per Node. js - Gestione dell'Archivio Data Lake](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)




<!--HONumber=Nov16_HO2-->


