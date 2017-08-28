---
title: Logica di ripetizione dei tentativi in Media Services SDK for .NET | Microsoft Docs
description: Questo argomento offre una panoramica della logica di ripetizione dei tentativi in Media Services SDK for .NET.
author: Juliako
manager: SyntaxC4
editor: 
services: media-services
documentationcenter: 
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e22a16c0929b28c475aa4caa0465651603713112
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logica di ripetizione dei tentativi in Media Services SDK for .NET
Quando si usano i servizi di Microsoft Azure, possono verificarsi errori temporanei. Se si verifica un errore temporaneo, nella maggior parte dei casi, dopo alcune ripetizioni dei tentativi l'operazione ha esito positivo. Media Services SDK for .NET implementa la logica di ripetizione dei tentativi per gestire gli errori temporanei associati a eccezioni ed errori causati da richieste Web, dall'esecuzione di query, dal salvataggio delle modifiche e dalle operazioni di archiviazione.  Per impostazione predefinita, Media Services SDK for .NET esegue quattro ripetizioni dei tentativi prima di rigenerare l'eccezione all'applicazione. Il codice nell'applicazione deve quindi gestire questa eccezione correttamente.  

 Di seguito sono riportate brevi indicazioni dei criteri di richiesta Web, archiviazione, query e salvataggio di modifiche:  

* I criteri di archiviazione vengono usati per le operazioni di archiviazione BLOB (caricamenti o download di file di asset).  
* I criteri di richiesta Web vengono usati per le richieste Web generiche (ad esempio, per ottenere un token di autenticazione e la risoluzione dell'endpoint cluster degli utenti).  
* I criteri di query vengono usati per l'interrogazione di entità da REST, ad esempio mediaContext.Assets.Where(…).  
* I criteri di salvataggio delle modifiche vengono usati per eseguire qualsiasi operazione che modifica i dati all'interno del servizio (ad esempio, creazione o aggiornamento di un'entità, chiamata di una funzione del servizio per un'operazione).  
  
  Questo argomento elenca i tipi di eccezione e i codici di errore che vengono gestiti da Media Services SDK for .NET.  

## <a name="exception-types"></a>Tipi di eccezioni
La tabella seguente descrive le eccezioni che Media Services SDK for .NET gestisce o meno per alcune operazioni che possono causare errori temporanei.  

| Eccezione | Richiesta Web | Archiviazione | Query | Salvataggio di modifiche |
| --- | --- | --- | --- | --- |
| WebException<br/>Per altre informazioni, vedere la sezione [Codici di stato di WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus). |Sì |Sì |Sì |Sì |
| DataServiceClientException<br/> Per altre informazioni, vedere [Codici di stato dell'errore HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Sì |Sì |Sì |
| DataServiceQueryException<br/> Per altre informazioni, vedere [Codici di stato dell'errore HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Sì |Sì |Sì |
| DataServiceRequestException<br/> Per altre informazioni, vedere [Codici di stato dell'errore HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Sì |Sì |Sì |
| DataServiceTransportException |No |No |Sì |Sì |
| TimeoutException |Sì |Sì |Sì |No |
| SocketException |Sì |Sì |Sì |Sì |
| StorageException |No |Sì |No |No |
| IOException |No |Sì |No |No |

### <a name="WebExceptionStatus"></a> Codici di stato di WebException
La tabella seguente mostra i codici di errore WebException per cui viene implementata la logica di ripetizione dei tentativi. L'enumerazione [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) definisce i codici di stato.  

| Stato | Richiesta Web | Archiviazione | Query | Salvataggio di modifiche |
| --- | --- | --- | --- | --- |
| ConnectFailure |Sì |Sì |Sì |Sì |
| NameResolutionFailure |Sì |Sì |Sì |Sì |
| ProxyNameResolutionFailure |Sì |Sì |Sì |Sì |
| SendFailure |Sì |Sì |Sì |Sì |
| PipelineFailure |Sì |Sì |Sì |No |
| ConnectionClosed |Sì |Sì |Sì |No |
| KeepAliveFailure |Sì |Sì |Sì |No |
| UnknownError |Sì |Sì |Sì |No |
| ReceiveFailure |Sì |Sì |Sì |No |
| RequestCanceled |Sì |Sì |Sì |No |
| Timeout |Sì |Sì |Sì |No |
| ProtocolError <br/>La ripetizione dei tentativi in ProtocolError viene controllata mediante la gestione del codice di stato HTTP. Per altre informazioni, vedere [Codici di stato dell'errore HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Sì |Sì |Sì |Sì |

### <a name="HTTPStatusCode"></a> Codici di stato dell'errore HTTP
Quando le operazioni di query e salvataggio di modifiche generano DataServiceClientException, DataServiceQueryException o DataServiceQueryException, nella proprietà StatusCode viene restituito il codice di stato dell'errore HTTP.  La tabella seguente mostra i codici di errore per cui viene implementata la logica di ripetizione dei tentativi.  

| Stato | Richiesta Web | Archiviazione | Query | Salvataggio di modifiche |
| --- | --- | --- | --- | --- |
| 401 |No |Sì |No |No |
| 403 |No |Sì<br/>Gestione della ripetizione dei tentativi con attese più lunghe. |No |No |
| 408 |Sì |Sì |Sì |Sì |
| 429 |Sì |Sì |Sì |Sì |
| 500 |Sì |Sì |Sì |No |
| 502 |Sì |Sì |Sì |No |
| 503 |Sì |Sì |Sì |Sì |
| 504 |Sì |Sì |Sì |No |

Se si vuole esaminare l'implementazione effettiva della logica di ripetizione dei tentativi di Media Services SDK for .NET, vedere [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


