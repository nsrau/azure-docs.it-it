---
title: Connettersi all'API di Servizi multimediali di Azure - Node.jsConnect to Azure Media Services v3 API - Node.js
description: In questo articolo viene illustrato come connettersi all'API di Servizi multimediali v3 con Node.js.This article demonstrates how to connect to Media Services v3 API with Node.js.
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
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896108"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Connettersi all'API di Servizi multimediali v3 - Node.js

Questo articolo illustra come connettersi all'SDK node.js di Servizi multimediali di Azure usando il metodo di accesso dell'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

- Installare [Node.js](https://nodejs.org/en/download/).
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.

> [!IMPORTANT]
> Esaminare [le convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Creare package.jsonCreate package.json

1. Creare un file package.json utilizzando l'editor preferito.
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

Devono essere specificati i seguenti pacchetti:

|Pacchetto|Descrizione|
|---|---|
|`azure-arm-mediaservices`|SDK di Servizi multimediali di Azure.Azure Media Services SDK. <br/>Per assicurarsi di usare il pacchetto di Servizi multimediali di Azure più recente, selezionare [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK di archiviazione. Utilizzato durante il caricamento dei file nelle risorse.|
|`ms-rest-azure`| Utilizzato per l'accesso.|

È possibile eseguire il comando seguente per assicurarsi di utilizzare il pacchetto più recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Connettersi al client Node.js

1. Crea un file .js utilizzando il tuo editor preferito.
1. Aprire il file e incollare il codice seguente.
1. Impostare i valori nella sezione "configurazione endpoint" sui valori ottenuti dalle API di [accesso.](access-api-cli-how-to.md)

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

Esaminare la documentazione [Informazioni di riferimento su Node.js](/javascript/api/overview/azure/mediaservices/management) di Servizi multimediali e vedere gli [esempi](https://github.com/Azure-Samples/media-services-v3-node-tutorials) che mostrano come usare l'API Servizi multimediali con Node.js.

