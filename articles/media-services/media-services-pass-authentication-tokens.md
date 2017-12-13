---
title: Passaggio di token di autenticazione a Servizi multimediale di Azure | Microsoft Docs
description: Informazioni su come inviare i token di autenticazione dal client al servizio di distribuzione delle chiavi di Servizi multimediali di Azure
services: media-services
keywords: protezione del contenuto, Digital Rights Management, autenticazione del token
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>Procedure di passaggio dei token al servizio di distribuzione delle chiavi di Servizi multimediali di Azure da parte dei client
Viene costantemente richiesto come un lettore possa passare i token ai servizi di distribuzione delle chiavi, per essere verificati e ottenere una chiave per il lettore. Sono supportati i due formati di token SWT (Simple Web Token) e JWT (JSON Web Token). L'autenticazione di token può essere applicata a qualsiasi tipo di chiave, indipendentemente dal fatto che si esegua la crittografia comune o la crittografia envelope AES.

Il token può essere passato con il lettore nei modi seguenti, a seconda del lettore e della piattaforma di destinazione:
- Tramite l'intestazione dell'autorizzazione HTTP.
> [!NOTE]
> Si noti che è previsto il prefisso "Bearer" per le specifiche di OAuth 2.0. Un lettore di esempio con la configurazione del token è ospitato nella [pagina di prova](http://ampdemo.azureedge.net/) di Azure Media Player. Scegliere AES (token JWT) o AES (token SWT) per impostare l'origine video. Il token viene passato tramite l'intestazione dell'autorizzazione.

- Tramite l'aggiunta di un parametro query URL con "token = tokenvalue".  
> [!NOTE]
> Si noti che non è previsto alcun prefisso "Bearer". Poiché il token viene inviato tramite un URL, è necessario per la blindatura della stringa del token. Di seguito è riportato un codice di esempio C# su come eseguire questa operazione:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- Tramite il campo CustomData.
Solo per l'acquisizione della licenza PlayReady tramite il campo CustomData della richiesta di acquisizione di licenze PlayReady. In questo caso, il token deve essere all'interno del documento xml descritto di seguito.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
Inserire il token di autenticazione nell'elemento <Token>.

- Tramite una playlist HLS alternativa. Se è necessario configurare l'autenticazione del token per AES + HLS la riproduzione su iOS/Safari, non esiste un modo per l'invio diretto nel token. Vedere il [post di blog](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) su come alternare la playlist per abilitare questo scenario.

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]