---
title: Aggiungere il connettore Facebook alle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Facebook con i parametri dell&quot;API REST.
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: b8a66308c4f4f1df610cdacd092ef133bd605665


---
# <a name="get-started-with-the-facebook-connector"></a>Introduzione al connettore Facebook
Connettersi a Facebook e pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via. 

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.
> 
> 

Con Facebook è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Facebook. 
* Usare un trigger quando si riceve un nuovo post.
* Usare azioni per pubblicare un post nella sequenza temporale, recuperare il feed di una pagina e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando nella sequenza temporale è presente un nuovo post, è possibile selezionare tale post ed eseguirne il push al feed di Twitter. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore Facebook include i trigger e le azioni seguenti. 

| Trigger | Azioni |
| --- | --- |
| <ul><li>Quando nella sequenza temporale è presente un nuovo post</li></ul> |<ul><li>Recupera feed dalla sequenza temporale</li><li>Pubblica post nella sequenza temporale</li><li>Quando nella sequenza temporale è presente un nuovo post</li><li>Recupera feed della pagina</li><li>Recupera sequenza temporale utente</li><li>Pubblica post nella pagina</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## <a name="create-a-connection-to-facebook"></a>Creare una connessione a Facebook
Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Facebook.

1. Accedere al proprio account Facebook
2. Selezionare **Autorizza**e consentire alle app per la logica di connettersi e usare Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> È possibile usare la stessa connessione di Facebook in altre app per la logica.
> 
> 

## <a name="swagger-rest-api-reference"></a>Riferimento all'API REST Swagger
Si applica alla versione 1.0.

### <a name="get-feed-from-my-timeline"></a>Recupera feed dalla sequenza temporale
Recupera i feed dalla sequenza temporale dell'utente connesso.  
```GET: /me/feed```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| fields |string |no |query |nessuno |Specifica i campi da restituire, ad esempio ID, nome o immagine. |
| limit |integer |no |query |nessuno |Numero massimo di post da recuperare |
| con |string |no |query |nessuno |Limitare l'elenco dei post solo a quelli con percorso collegato. |
| filter |string |no |query |nessuno |Recuperare solo i post che corrispondono a un filtro di flusso specifico. |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="post-to-my-timeline"></a>Pubblica post nella sequenza temporale
Pubblica un messaggio di stato nella sequenza temporale dell'utente connesso.  
```POST: /me/feed```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| post |string |Sì |body |nessuno |Nuovo messaggio da pubblicare |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>Quando nella sequenza temporale è presente un nuovo post
Quando nella sequenza temporale dell'utente connesso è presente un nuovo post, attiva un nuovo flusso.  
```GET: /trigger/me/feed```

Non sono disponibili parametri. 

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="get-page-feed"></a>Recupera feed della pagina
Recupera i post dal feed di una pagina specificata.  
```GET: /{pageId}/feed```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| pageId |string |Sì |path |nessuno |ID della pagina da cui devono essere recuperati i post. |
| limit |integer |no |query |nessuno |Numero massimo di post da recuperare |
| include_hidden |boolean |no |query |nessuno |Includere o escludere i post che sono stati nascosti dalla pagina |
| fields |string |no |query |nessuno |Specifica i campi da restituire, ad esempio ID, nome o immagine. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="get-user-timeline"></a>Recupera sequenza temporale utente
Recupera i post dalla sequenza temporale di un utente.  
```GET: /{userId}/feed```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| userId |string |Sì |path |nessuno |ID dell'utente la cui cronologia deve essere recuperata. |
| limit |integer |no |query |nessuno |Numero massimo di post da recuperare |
| con |string |no |query |nessuno |Limitare l'elenco dei post solo a quelli con percorso collegato. |
| filter |string |no |query |nessuno |Recuperare solo i post che corrispondono a un filtro di flusso specifico. |
| fields |string |no |query |nessuno |Specifica i campi da restituire, ad esempio ID, nome o immagine. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="post-to-page"></a>Pubblica post nella pagina
Pubblica un messaggio in una pagina di Facebook con il nome dell'utente connesso.  
```POST: /{pageId}/feed```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| pageId |string |Sì |path |nessuno |ID della pagina da pubblicare. |
| post |many |Sì |body |nessuno |Nuovo messaggio da pubblicare. |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetto
#### <a name="getfeedresponse"></a>GetFeedResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| data |array |no |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| data |array |no |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: un singolo elemento nel feed di un profilo
Il profilo può essere un utente, una pagina, un'app o un gruppo. 

| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| admin_creator |array |no |
| caption |string |no |
| created_time |stringa |no |
| Descrizione |string |no |
| feed_targeting |non definito |no |
| from |non definito |no |
| icon |string |no |
| is_hidden |boolean |no |
| is_published |boolean |no |
| link |string |no |
| message |string |no |
| Nome |string |no |
| object_id |stringa |no |
| picture |string |no |
| place |non definito |no |
| privacy |non definito |no |
| properties |array |no |
| source |string |no |
| status_type |string |no |
| story |string |no |
| targeting |non definito |no |
| to |array |no |
| type |string |no |
| updated_time |string |no |
| with_tags |non definito |no |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: un singolo elemento nel feed di un profilo
Il profilo può essere un utente, una pagina, un'app o un gruppo.

| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| created_time |stringa |no |
| from |non definito |no |
| message |string |no |
| type |string |no |

#### <a name="adminitem"></a>AdminItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| link |string |no |

#### <a name="propertyitem"></a>PropertyItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |string |no |
| text |string |no |
| href |string |no |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| message |string |sì |
| link |string |no |
| picture |string |no |
| Nome |string |no |
| caption |string |no |
| Descrizione |string |no |
| place |string |no |
| tags |string |no |
| privacy |non definito |no |
| object_attachment |stringa |no |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| message |string |sì |
| link |string |no |
| picture |string |no |
| Nome |string |no |
| caption |string |no |
| Descrizione |string |no |
| Azioni |array |no |
| place |string |no |
| tags |string |no |
| object_attachment |stringa |no |
| targeting |non definito |no |
| feed_targeting |non definito |no |
| published |boolean |no |
| scheduled_publish_time |stringa |no |
| backdated_time |string |no |
| backdated_time_granularity |string |no |
| child_attachments |array |no |
| multi_share_end_card |boolean |no |

#### <a name="postfeedresponse"></a>PostFeedResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |

#### <a name="profilecollection"></a>ProfileCollection
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| data |array |no |

#### <a name="useritem"></a>UserItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| first_name |stringa |no |
| last_name |stringa |no |
| Nome |string |no |
| gender |string |no |
| about |string |no |

#### <a name="actionitem"></a>ActionItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |string |no |
| link |string |no |

#### <a name="targetitem"></a>TargetItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| countries |array |no |
| locales |array |no |
| regions |array |no |
| cities |array |no |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: oggetto che controlla i newsfeed di destinazione per questo post
I membri di questi gruppi hanno più probabilità di vedere questo post, gli altri utenti hanno meno probabilità. Si applica solo alle pagine.

| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| countries |array |no |
| regions |array |no |
| cities |array |no |
| age_min |integer |no |
| age_max |integer |no |
| genders |array |no |
| relationship_statuses |array |no |
| interested_in |array |no |
| college_years |array |no |
| interests |array |no |
| relevant_until |integer |no |
| education_statuses |array |no |
| locales |array |no |

#### <a name="placeitem"></a>PlaceItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| Nome |string |no |
| overall_rating |number |no |
| location |non definito |no |

#### <a name="locationitem"></a>LocationItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| city |string |no |
| country |string |no |
| latitudine |number |no |
| located_in |string |no |
| longitudine |number |no |
| Nome |string |no |
| region |string |no |
| state |string |no |
| street |string |no |
| zip |string |no |

#### <a name="privacyitem"></a>PrivacyItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Descrizione |string |no |
| value |string |sì |
| allow |string |no |
| deny |string |no |
| friends |string |no |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| link |string |no |
| picture |string |no |
| image_hash |stringa |no |
| Nome |string |no |
| Descrizione |string |no |

#### <a name="postphotorequest"></a>PostPhotoRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| URL |string |sì |
| caption |string |no |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |sì |
| post_id |stringa |sì |

#### <a name="postvideorequest"></a>PostVideoRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| videoData |string |sì |
| Descrizione |string |sì |
| title |string |sì |
| uploadedVideoName |string |no |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| data |non definito |sì |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| URL |string |sì |
| is_silhouette |boolean |sì |
| height |string |no |
| width |string |no |

#### <a name="geteventresponse"></a>GetEventResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| data |array |sì |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |sì |
| Nome |string |sì |
| start_time |string |no |
| end_time |string |no |
| timezone |string |no |
| location |string |no |
| Descrizione |string |no |
| ticket_uri |string |no |
| rsvp_status |stringa |Sì |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Jan17_HO3-->


