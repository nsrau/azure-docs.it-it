---
title: Connettersi all'API di servizi multimediali di Azure V3-Node.js
description: Questo articolo illustra come connettersi all'API di servizi multimediali V3 con Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 1fc9d38827b8c00a6c90280a89e520e28f1763b2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844302"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Connettersi all'API di servizi multimediali V3-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come connettersi a servizi multimediali di Azure V3 node.js SDK usando il metodo di accesso dell'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

- Installare [Node.js](https://nodejs.org/en/download/).
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md). Assicurarsi di ricordare il nome del gruppo di risorse e quello dell'account di Servizi multimediali.

> [!IMPORTANT]
> Vedere [Convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Crea package.jsin

1. Creare una package.jsnel file usando l'editor preferito.
1. Aprire il file e incollare il codice seguente:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^8.0.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

È necessario specificare i pacchetti seguenti:

|Pacchetto|Descrizione|
|---|---|
|`azure-arm-mediaservices`|SDK di Servizi multimediali di Azure. <br/>Per assicurarsi di usare il pacchetto di servizi multimediali di Azure più recente, selezionare [NPM install Azure-ARM-MediaServices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK di archiviazione. Usato quando si caricano file in asset.|
|`ms-rest-azure`| Usato per l'accesso.|

È possibile eseguire il comando seguente per assicurarsi di usare il pacchetto più recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Connetti a client Node.js

1. Creare un file con estensione js usando l'editor preferito.
1. Aprire il file e incollare il codice seguente.
1. Impostare i valori nella sezione "configurazione dell'endpoint" sui valori ottenuti dalle [API di accesso](./access-api-howto.md).

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

Esaminare la documentazione [Informazioni di riferimento su Node.js](/javascript/api/overview/azure/mediaservices/management) di Servizi multimediali e vedere gli [esempi](https://github.com/Azure-Samples/media-services-v3-node-tutorials) che mostrano come usare l'API Servizi multimediali con Node.js.
