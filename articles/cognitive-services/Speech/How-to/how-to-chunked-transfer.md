---
title: Come trasferire in blocchi il flusso audio | Microsoft Docs
description: Come usare il trasferimento in blocchi per inviare flusso audio al Servizio di riconoscimento vocale
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373788"
---
# <a name="chunked-transfer-encoding"></a>Codifica di trasferimento in blocchi

Per trascrivere il parlato in testo, l'API Riconoscimento vocale Microsoft consente di inviare l'audio come un unico blocco oppure di suddividere l'audio in blocchi di piccole dimensioni. Per ottenere uno streaming audio efficiente e ridurre la latenza di trascrizione, è consigliabile usare la [codifica di trasferimento in blocchi](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) per inviare il flusso audio al servizio. Altre implementazioni possono comportare una latenza maggiore percepita dall'utente. Per altre informazioni, vedere la pagina [Flussi audio](../concepts.md#audio-streams).

> [!NOTE]
> Non è possibile caricare più di 10 secondi audio in una richiesta singola e la durata totale della richiesta non può superare i 14 secondi.
> [!NOTE]
> È necessario specificare la codifica di trasferimento in blocchi solo se si usano [API REST](../GetStarted/GetStartedREST.md) per chiamare il Servizio di riconoscimento vocale. Per le applicazioni che usano [librerie client](../GetStarted/GetStartedClientLibraries.md) non è necessario configurare la codifica di trasferimento in blocchi.

Il codice seguente illustra come impostare la codifica di trasferimento in blocchi e come inviare un file audio da suddividere in blocchi di 1024 byte.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
