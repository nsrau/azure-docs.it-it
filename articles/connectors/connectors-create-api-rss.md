---
title: RSS | Microsoft Docs
description: Creare app per la logica in Servizio app di Azure. Il connettore RSS consente agli utenti di pubblicare e recuperare elementi di feed. Consente anche agli utenti di attivare operazioni quando viene pubblicato un nuovo elemento nel feed.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7a43c010945c6b1601fac5b2fbf934c3d48811e5


---
# <a name="get-started-with-the-rss-connector"></a>Introduzione al connettore RSS
RSS è un formato di diffusione nel Web molto usato per la pubblicazione di contenuti aggiornati di frequente, come post di blog e notizie.  Molti autori di contenuti offrono feed RSS per consentire agli utenti di eseguire la sottoscrizione.  È possibile usare il connettore RSS per recuperare informazioni sui feed e attivare flussi quando vengono pubblicati nuovi elementi in un feed RSS.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica. 
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore RSS può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML. 

 Nel connettore RSS sono disponibili le azioni e/o i trigger seguenti:

### <a name="rss-actions"></a>Azioni di RSS
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [ListFeedItems](connectors-create-api-rss.md#listfeeditems) |Ottiene tutti gli elementi del feed RSS. |

### <a name="rss-triggers"></a>Trigger di RSS
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| Quando viene pubblicato un nuovo elemento di feed |Attiva un flusso di lavoro quando viene pubblicato un nuovo feed |

## <a name="create-a-connection-to-rss"></a>Creare una connessione a RSS
> [!INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## <a name="reference-for-rss"></a>Informazioni di riferimento per RSS
Si applica alla versione 1.0

## <a name="onnewfeed"></a>OnNewFeed
Quando viene pubblicato un nuovo elemento di feed: attiva un flusso di lavoro quando viene pubblicato un nuovo feed 

```GET: /OnNewFeed``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |stringa |yes |query |nessuno |URL del feed |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="listfeeditems"></a>ListFeedItems
Elenco di tutti gli elementi del feed RSS: ottiene tutti gli elementi del feed RSS. 

```GET: /ListFeedItems``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |stringa |yes |query |nessuno |URL del feed |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetti
### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse[FeedItem]
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="feeditem"></a>FeedItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |Sì |
| title |stringa |Sì |
| content |string |Sì |
| links |array |No |
| updatedOn |string |No |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


