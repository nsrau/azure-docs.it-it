---
title: Passare i token di autenticazione a servizi multimediali di Azure | Documenti Microsoft
description: Informazioni su come inviare i token di autenticazione dal client al servizio di distribuzione delle chiavi di servizi multimediali di Azure
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
ms.openlocfilehash: 7d143242231444b8557a303d1b504d5311693f1a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Informazioni su come i client passare token al servizio di distribuzione delle chiavi di servizi multimediali di Azure
I clienti spesso chiedono come un lettore può passare token al servizio di distribuzione delle chiavi di servizi multimediali di Azure per la verifica in modo che il lettore può ottenere la chiave. Servizi multimediali supporta i token web semplice (SWT) e formatta JSON Web Token (JWT). L'autenticazione del token viene applicato a qualsiasi tipo di chiave, sia che si utilizzi la crittografia comune o busta Advanced Encryption Standard (AES) nel sistema.

 A seconda del lettore e piattaforma di destinazione, è possibile passare il token con il lettore nei modi seguenti:

- Tramite l'intestazione dell'autorizzazione HTTP.
    > [!NOTE]
    > Il prefisso "Bearer" è previsto per le specifiche di OAuth 2.0. Un lettore di esempio con la configurazione del token è ospitato in Azure Media Player [pagina demo](http://ampdemo.azureedge.net/). Per impostare l'origine video, scegliere **AES (Token JWT)** o **AES (Token SWT)**. Il token viene passato tramite l'intestazione di autorizzazione.

- Tramite l'aggiunta di un URL di parametro con query "token = tokenvalue."  
    > [!NOTE]
    > Il prefisso "Bearer" non è quello previsto. Poiché il token viene inviato tramite un URL, è necessario per la blindatura la stringa del token. Di seguito è riportato un codice di esempio c# che illustra come eseguire questa operazione:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Tramite il campo CustomData.
Questa opzione viene utilizzata per l'acquisizione della licenza PlayReady solo tramite il campo della proprietà CustomData della richiesta di acquisizione di licenze PlayReady. In questo caso, il token deve essere all'interno del documento XML come descritto di seguito:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Inserire il token di autenticazione nell'elemento Token.

- Tramite una playlist HTTP Live Streaming (HLS) alternativo. Se è necessario configurare l'autenticazione del token per AES + HLS la riproduzione su iOS/Safari, non è un modo è possibile inviare direttamente nel token. Per ulteriori informazioni su come alternare la playlist per abilitare questo scenario, vedere questo [post di blog](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]