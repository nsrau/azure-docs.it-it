---
title: Connettersi all'API di servizi multimediali di Azure V3-node. js
description: Informazioni su come connettersi all'API di servizi multimediali V3 con node. js.
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
ms.openlocfilehash: 2ab620ec456610029d699952bb9625abfe4c21dc
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307875"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Connettersi a servizi multimediali V3 API-Node. js

Questo articolo illustra come connettersi a node. js SDK di servizi multimediali di Azure v3 usando il metodo di accesso dell'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

- Installare [Node.js](https://nodejs.org/en/download/).
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di servizi multimediali.

> [!IMPORTANT]
> Esaminare le [convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Creazione di Package. JSON

1. Creare un file Package. JSON usando l'editor preferito.
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

È necessario specificare i pacchetti seguenti:

|Pacchetto|Descrizione|
|---|---|
|`azure-arm-mediaservices`|SDK di servizi multimediali di Azure. <br/>Per assicurarsi di usare il pacchetto di servizi multimediali di Azure più recente, selezionare [NPM install Azure-ARM-MediaServices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK di archiviazione. Usato quando si caricano file in asset.|
|`ms-rest-azure`| Usato per l'accesso.|

È possibile eseguire il comando seguente per assicurarsi di usare il pacchetto più recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Connettersi al client Node. js

1. Creare un file con estensione js usando l'editor preferito.
1. Aprire il file e incollare il codice seguente.
1. Impostare i valori nella sezione "configurazione dell'endpoint" sui valori ottenuti dalle [API di accesso](access-api-cli-how-to.md).

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

Aprire un prompt dei comandi. Passare alla directory dell'esempio e quindi eseguire i comandi seguenti:

```
npm install 
node index.js
```

## <a name="see-also"></a>Vedere anche

- [Concetti relativi ai Servizi multimediali](concepts-overview.md)
- [Installazione di NPM tramite azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione [Informazioni di riferimento su Node.js](https://aka.ms/ams-v3-nodejs-ref) di Servizi multimediali e vedere gli [esempi](https://github.com/Azure-Samples/media-services-v3-node-tutorials) che mostrano come usare l'API Servizi multimediali con Node.js.

