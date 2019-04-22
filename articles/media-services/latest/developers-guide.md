---
title: SDK di Servizi multimediali di Azure v3 - Azure
description: Questo articolo fornisce una panoramica su come iniziare a sviluppare con l'API Servizi multimediali v3 usando gli SDK.
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
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564510"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Sviluppare con l'API Servizi multimediali v3 usando gli SDK

Gli sviluppatori possono usare l'[API REST](https://aka.ms/ams-v3-rest-ref) Servizi multimediali o le librerie client che consentono di interagire con l'API REST per creare, gestire e mantenere facilmente flussi di lavoro multimediali personalizzati. L'API [Servizi multimediali v3](https://aka.ms/ams-v3-rest-sdk) è basata sulla specifica OpenAPI (in precedenza nota come Swagger).

> [!NOTE]
> Gli SDK di Servizi multimediali di Azure v3 non sono garantiti come thread-safe. Quando si sviluppa un'applicazione multithreading, è necessario aggiungere la propria logica di sincronizzazione thread per proteggere il client oppure usare un nuovo oggetto AzureMediaServicesClient per ogni thread. È anche necessario prestare attenzione ai problemi di multithreading introdotti da oggetti facoltativi forniti dal codice al client (ad esempio, un'istanza di HttpClient in .NET).

Questo argomento fornisce collegamenti a SDK, strumenti e guide pratiche.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a sviluppare con Servizi multimediali, è necessario:

- Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [Informazioni sui concetti fondamentali](concepts-overview.md)
- Consultare [Developing with Media Services v3 APIs](media-services-apis-overview.md) (Sviluppare con le API Servizi multimediali v3)
- [Creare un account di Servizi multimediali - Interfaccia della riga di comando](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Iniziare a sviluppare con gli SDK

### <a name="net"></a>.NET

Usare [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk) per [connettersi a Servizi multimediali](configure-connect-dotnet-howto.md).

Esplorare la documentazione [Informazioni di riferimento su .NET](https://aka.ms/ams-v3-dotnet-ref) di Servizi multimediali.

### <a name="java"></a>Java

Usare [Java SDK](https://aka.ms/ams-v3-java-sdk) per [connettersi a Servizi Multimediali](configure-connect-java-howto.md).

Esaminare la documentazione [Informazioni di riferimento su Java](https://aka.ms/ams-v3-java-ref) di Servizi multimediali.

### <a name="nodejs"></a>Node.js

Usare [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) per [connettersi a Servizi Multimediali](configure-connect-nodejs-howto.md).

Esaminare la documentazione [Informazioni di riferimento su Node.js](https://aka.ms/ams-v3-nodejs-ref) di Servizi multimediali e vedere gli [esempi](https://github.com/Azure-Samples/media-services-v3-node-tutorials) che mostrano come usare l'API Servizi multimediali con Node.js.

### <a name="python"></a>Python

Usare [Python SDK](https://aka.ms/ams-v3-python-sdk).

Esaminare la documentazione [Informazioni di riferimento su Python](https://aka.ms/ams-v3-python-ref) di Servizi multimediali.

### <a name="go"></a>Go

Usare [Go SDK](https://aka.ms/ams-v3-go-sdk).

Esaminare la documentazione [Informazioni di riferimento su Go](https://aka.ms/ams-v3-go-ref) di Servizi multimediali.

### <a name="ruby"></a>Ruby

Usare [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Explorer di Servizi multimediali di Azure

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) è uno strumento disponibile per i clienti di Windows che vogliono informazioni su Servizi multimediali. AMSE è un'applicazione Winforms/C# che esegue operazioni di caricamento, download, codifica, streaming di VOD e contenuti live con Servizi multimediali. Lo strumento AMSE è stato pensato per i clienti che vogliono testare Servizi multimediali senza scrivere codice. Il codice AMSE viene fornito come risorsa per i clienti che vogliono sviluppare con Servizi multimediali.

AMSE è un progetto open source il cui supporto viene fornito dalla community (i problemi possono essere segnalati a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Questo progetto ha adottato il [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Codice di comportamento Open Source di Microsoft). Per altre informazioni, vedere [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Domande frequenti sul Codice di comportamento Open Source di Microsoft) oppure contattare opencode@microsoft.com per eventuali altre domande o commenti.

## <a name="next-steps"></a>Passaggi successivi

[Overview](media-services-overview.md)
