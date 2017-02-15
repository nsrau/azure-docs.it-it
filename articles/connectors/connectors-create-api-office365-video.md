---
title: Usare il connettore Office 365 Video nelle app per la logica | Microsoft Azure
description: Introduzione all&quot;uso del connettore Office 365 Video (connettore) nelle app per la logica del Servizio app di Microsoft Azure
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 959eaca7aafd8516cfc6f3b5dd2a44ce21ec4825


---
# <a name="get-started-with-the-office365-video-connector"></a>Introduzione al connettore Office 365 Video
Connettersi a Office 365 Video per ottenere informazioni su un video di Office 365, recuperare un elenco di video e altro ancora. Il connettore Office 365 Video può essere usato da:

* App per la logica 

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Questo connettore non è supportato da alcuna versione precedente dello schema.
> 
> 

Con Office 365 Video è possibile:

* Creare un flusso aziendale basato sui dati ottenuti da Office 365 Video. 
* Usare azioni per controllare lo stato del portale video, recuperare l'elenco di tutti video in un canale e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, è possibile usare il connettore di ricerca di Bing per cercare video di Office 365 e quindi usare il connettore Office 365 Video per ottenere informazioni relative a un video specifico. Se il video soddisfa le esigenze, è possibile pubblicarlo su Facebook. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Nel connettore Office 365 Video sono disponibili le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni |
| --- | --- |
| Nessuno |<ul><li>Controlla stato portale video</li><li>Recupera tutti i canali visualizzabili</li><li>Recupera l'URL di riproduzione del manifesto dei Servizi multimediali di Azure per un video</li><li>Recupera il token di connessione per poter accedere a un video da decrittografare</li><li>Recupera informazioni su un particolare video di Office 365</li><li>Elenca tutti i video di Office 365 presenti in un canale</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML. 

## <a name="create-a-connection-to-office365-video-connector"></a>Creare una connessione al connettore Office 365 Video
Quando si aggiunge questo connettore alle app per la logica, è necessario accedere all'account Office 365 Video e consentire alle app per la logica di connettersi all'account.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Dopo aver creato la connessione, immettere le proprietà di Office 365 Video, ad esempio, il nome del tenant o l'ID canale. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

> [!TIP]
> È possibile usare la stessa connessione di Office 365 Video in altre app per la logica.
> 
> 

## <a name="swagger-rest-api-reference"></a>Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### <a name="checks-video-portal-status"></a>Controlla stato portale video
Controlla lo stato del portale video per vedere se i servizi video sono abilitati.  
```GET: /{tenant}/IsEnabled``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tenant |string |Sì |path |Nessuno |Il nome del tenant della directory di cui l'utente fa parte |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 404 |Non trovato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="get-all-viewable-channels"></a>Recupera tutti i canali visualizzabili
Recupera tutti i canali a cui l'utente ha accesso in visualizzazione.  
```GET: /{tenant}/Channels``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tenant |string |Sì |path |Nessuno |Il nome del tenant della directory di cui l'utente fa parte |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 404 |Non trovato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Elenca tutti i video di Office 365 presenti in un canale
Elenca tutti i video di Office 365 presenti in un canale.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tenant |string |Sì |path |Nessuno |Il nome del tenant della directory di cui l'utente fa parte |
| channelId |string |Sì |path |Nessuno |L'ID canale da cui devono essere recuperati i video |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 404 |Non trovato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="gets-information-about-a-particular-office365-video"></a>Recupera informazioni su un particolare video di Office 365
Recupera informazioni su un particolare video di Office 365.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tenant |string |Sì |path |Nessuno |Il nome del tenant della directory di cui l'utente fa parte |
| channelId |string |Sì |path |Nessuno |L'ID del canale |
| videoId |string |Sì |path |Nessuno |L'ID del video |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 404 |Non trovato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Recupera l'URL di riproduzione del manifesto dei Servizi multimediali di Azure per un video
Recupera l'URL di riproduzione del manifesto dei Servizi multimediali di Azure per un video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tenant |string |Sì |path |Nessuno |Il nome del tenant della directory di cui l'utente fa parte |
| channelId |string |Sì |path |Nessuno |L'ID del canale |
| videoId |string |Sì |path |Nessuno |L'ID del video |
| streamingFormatType |string |yes |query |Nessuno |Tipo di formato di streaming. 1 - Smooth Streaming o MPEG-DASH. 0 - Streaming HLS |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 404 |Non trovato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Recupera il token di connessione per poter accedere a un video da decrittografare
Recupera il token di connessione per poter accedere a un video da decrittografare.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tenant |string |Sì |path |Nessuno |Il nome del tenant della directory di cui l'utente fa parte |
| channelId |string |Sì |path |Nessuno |L'ID del canale |
| videoId |string |Sì |path |Nessuno |L'ID del video |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 404 |Non trovato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni degli oggetti
#### <a name="channel-channel-class"></a>Channel: classe del canale
| Nome | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| Titolo |string |no |
| Descrizione |string |no |

#### <a name="video"></a>Video
| Nome | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| Titolo |string |no |
| Descrizione |string |no |
| CreationDate |string |no |
| Proprietario |string |no |
| ThumbnailUrl |string |no |
| VideoUrl |string |no |
| VideoDuration |integer |no |
| VideoProcessingStatus |integer |no |
| ViewCount |integer |no |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).




<!--HONumber=Nov16_HO3-->


