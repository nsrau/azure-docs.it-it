---
title: SDK di Servizi multimediali di Azure v3 - Azure
description: Questo articolo fornisce una panoramica su come iniziare a sviluppare con l'API Servizi multimediali v3 usando gli SDK e/o gli strumenti.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/22/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 97123ec795353073b912bd83e0abb45e23549a75
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749801"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Iniziare a sviluppare con l'API Servizi multimediali v3 usando gli SDK e/o gli strumenti

Gli sviluppatori possono usare l'[API REST](https://aka.ms/ams-v3-rest-ref) Servizi multimediali o le librerie client che consentono di interagire con l'API REST per creare, gestire e mantenere facilmente flussi di lavoro multimediali personalizzati. L'API [Servizi multimediali v3](https://aka.ms/ams-v3-rest-sdk) è basata sulla specifica OpenAPI (in precedenza nota come Swagger).

Questo argomento fornisce collegamenti a SDK, strumenti, documentazione. Fornisce inoltre alcune informazioni utili per diversi ambienti di sviluppo.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a sviluppare con Servizi multimediali, è necessario:

- Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [Creare un account di Servizi multimediali di Azure.](create-account-cli-how-to.md)

## <a name="start-developing"></a>Iniziare a sviluppare

Servizi multimediali di Azure supporta gli SDK/strumenti seguenti 

- [Interfaccia della riga di comando di Azure](https://aka.ms/ams-v3-cli) 
- [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)
- [SDK per Java](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [Go SDK](https://aka.ms/ams-v3-go-sdk)
- [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Explorer di Servizi multimediali di Azure

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) è uno strumento disponibile per i clienti di Windows che vogliono informazioni su Servizi multimediali. AMSE è un'applicazione Winforms/C# che esegue operazioni di caricamento, download, codifica, streaming di VOD e contenuti live con Servizi multimediali. Lo strumento AMSE è stato pensato per i clienti che vogliono testare Servizi multimediali senza scrivere codice. Il codice AMSE viene fornito come risorsa per i clienti che vogliono sviluppare con Servizi multimediali.

AMSE è un progetto open source il cui supporto viene fornito dalla community (i problemi possono essere segnalati a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Questo progetto ha adottato il [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Codice di comportamento Open Source di Microsoft). Per altre informazioni, vedere [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Domande frequenti sul Codice di comportamento Open Source di Microsoft) oppure contattare opencode@microsoft.com per eventuali altre domande o commenti.

## <a name="rest"></a>REST

Per iniziare a sviluppare con le API REST Servizi multimediali, installare un client REST. Ad esempio **Postman**, **Visual Studio Code** con il plug-in REST o **Telerik Fiddler**. Per gli esempi di Servizi multimediali v3 verrà usato [Postman](media-rest-apis-with-postman.md).

Esaminare la documentazione [Informazioni di riferimento sull'API REST](https://aka.ms/ams-v3-rest-ref) di Servizi multimediali e vedere questi esempi:

- [Esercitazione: Codificare un file remoto basato su URL ed eseguire lo streaming del video - REST](stream-files-tutorial-with-rest.md)
- [Caricare file in un account di Servizi multimediali - REST](upload-files-rest-how-to.md)
- [Creare filtri con Servizi multimediali - REST](filters-dynamic-manifest-rest-howto.md)
- [API REST basate su Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Esaminare la documentazione [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](https://aka.ms/ams-v3-cli-ref) di Servizi multimediali e vedere questi esempi:

- [Ridimensionare le Media Reserved Units (S1/S2/S3) - Interfaccia della riga di comando](media-reserved-units-cli-how-to.md)
- [Creare un account di Servizi multimediali - Interfaccia della riga di comando](./scripts/cli-create-account.md) 
- [Reimpostare le credenziali dell'account - Interfaccia della riga di comando](./scripts/cli-reset-account-credentials.md)
- [Creare gli asset - Interfaccia della riga di comando](./scripts/cli-create-asset.md)
- [Caricare un file - Interfaccia della riga di comando](./scripts/cli-upload-file-asset.md)
- [Creare trasformazioni - Interfaccia della riga di comando](./scripts/cli-create-transform.md)
- [Creare i processi - Interfaccia della riga di comando](./scripts/cli-create-jobs.md)
- [Creare una Griglia di eventi - Interfaccia della riga di comando](./scripts/cli-create-event-grid.md)
- [Pubblicare un asset - Interfaccia della riga di comando](./scripts/cli-publish-asset.md)
- [Applicare un filtro - Interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Esaminare la documentazione [Informazioni di riferimento su .NET](https://aka.ms/ams-v3-dotnet-ref) di Servizi multimediali e vedere questi esempi:

- [Esercitazione: Caricare, codificare ed eseguire lo streaming dei video - .NET](stream-files-tutorial-with-api.md) 
- [Esercitazione: Eseguire lo streaming live con Servizi multimediali v3 - .NET](stream-live-tutorial-with-api.md)
- [Esercitazione: Analizzare i video con Servizi multimediali v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Creare un input del processo da un file locale - .NET](job-input-from-local-file-how-to.md)
- [Creare un input del processo da un URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Codificare con una trasformazione personalizzata - .NET](customize-encoder-presets-how-to.md)
- [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi - .NET](protect-with-aes128.md)
- [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM - .NET](protect-with-drm.md)
- [Ottenere una chiave di firma dai criteri esistenti - .NET](get-content-key-policy-dotnet-howto.md)
- [Creare filtri con Servizi multimediali - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Esempi di video on-demand avanzato di Funzioni di Azure v2 con Servizi multimediali v3](http://aka.ms/ams3functions)

## <a name="java"></a>Java

Esaminare la documentazione [Informazioni di riferimento su Java](https://aka.ms/ams-v3-java-ref) di Servizi multimediali.

## <a name="nodejs"></a>Node.js

Esaminare la documentazione [Informazioni di riferimento su Node.js](https://aka.ms/ams-v3-nodejs-ref) di Servizi multimediali e vedere gli [esempi](https://github.com/Azure-Samples/media-services-v3-node-tutorials) che mostrano come usare l'API Servizi multimediali con Node.js.

## <a name="python"></a>Python

Esaminare la documentazione [Informazioni di riferimento su Python](https://aka.ms/ams-v3-python-ref) di Servizi multimediali.

## <a name="go"></a>Go

Esaminare la documentazione [Informazioni di riferimento su Go](https://aka.ms/ams-v3-go-ref) di Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account - Interfaccia della riga di comando](create-account-cli-how-to.md)
- [Accedere alle API - Interfaccia della riga di comando](access-api-cli-how-to.md)

