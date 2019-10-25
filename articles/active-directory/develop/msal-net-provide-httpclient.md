---
title: Fornire un HttpClient e un proxy (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Informazioni su come fornire un client HTTP e proxy personalizzato per la connessione ad Azure AD con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259c796cb3653ebdd330f5e65db76cc29c181467
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802768"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fornire un client HTTP e proxy personalizzato con MSAL.NET
Quando si [inizializza un'applicazione client pubblica](msal-net-initializing-client-applications.md), è possibile usare il metodo `.WithHttpClientFactory method` per fornire un client HTTP personalizzato.  Fornendo tale client, è possibile implementare scenari avanzati, come quelli per controllare in modo specifico un proxy HTTP, personalizzare le intestazioni dell'agente utente o forzare MSAL a usare un determinato cliente HTTP (ad esempio, in API o app Web ASP.NET Core).

## <a name="initialize-with-httpclientfactory"></a>Eseguire l'inizializzazione con HttpClientFactory
L'esempio seguente illustra come creare un elemento `HttpClientFactory` e quindi inizializzare un'applicazione client pubblica con tale elemento:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient e Xamarin iOS
Quando si usa Xamarin iOS, è consigliabile creare un client HTTP (`HttpClient`) che usi in modo esplicito il gestore basato su `NSURLSession` per iOS 7 e versioni successive. MSAL.NET crea automaticamente un `HttpClient` che usa `NSURLSessionHandler` per iOS 7 e versioni successive. Per altre informazioni, leggere la [documentazione di Xamarin iOS per HttpClient](/xamarin/cross-platform/macios/http-stack).