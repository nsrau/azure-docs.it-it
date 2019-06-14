---
title: Connettersi all'API v3 di servizi multimediali di Azure - Node. js
description: Informazioni su come connettersi all'API di servizi multimediali v3 con Node. js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 40880a2c28ce28a671930ef8837082247e61e24b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733131"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Connettersi all'API servizi multimediali di v3 - Node. js

Questo articolo illustra come connettersi a Node. js v3 di servizi multimediali di Azure SDK usando l'accesso dell'entità servizio nel metodo.

## <a name="prerequisites"></a>Prerequisiti

- Installare [Node.js](https://nodejs.org/en/download/).
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account servizi multimediali.

## <a name="create-packagejson"></a>Creazione di package. JSON

1. Creare un file package. JSON usando l'editor preferito.
1. Aprire il file e incollare il codice seguente:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

I pacchetti seguenti può essere specificati:

|Pacchetto|Descrizione|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Per assicurarsi che si usa il pacchetto di servizi multimediali di Azure più recente, controllare [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK di archiviazione. Usato durante il caricamento di file nell'asset.|
|`ms-rest-azure`| Usato per l'accesso.|

È possibile eseguire il comando seguente per assicurarsi che si usa il pacchetto più recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Connettersi al client Node. js

1. Creare un file. js usando l'editor preferito.
1. Aprire il file e incollare il codice seguente.
1. Impostare i valori nella sezione "configurazione dell'endpoint" per valori ottenuto [accedere alle API](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Eseguire l'app

Aprire un prompt dei comandi. Passare alla directory dell'esempio ed eseguire i comandi seguenti:

```
npm install 
node index.js
```

## <a name="see-also"></a>Vedere anche

- [Concetti relativi ai Servizi multimediali](concepts-overview.md)
- [Installazione di NPM tramite azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione [Informazioni di riferimento su Node.js](https://aka.ms/ams-v3-nodejs-ref) di Servizi multimediali e vedere gli [esempi](https://github.com/Azure-Samples/media-services-v3-node-tutorials) che mostrano come usare l'API Servizi multimediali con Node.js.

