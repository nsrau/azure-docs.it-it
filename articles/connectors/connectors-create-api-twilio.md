---
title: Aggiungere il connettore Twilio alle app per la logica | Microsoft Docs
description: Panoramica del connettore Twilio con i parametri dell&quot;API REST
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0999d96b4819d6bf03faa843750c5c4b19649774


---
# <a name="get-started-with-the-twilio-connector"></a>Introduzione al connettore Twilio
Connettersi a Twilio per inviare e ricevere messaggi SMS, MMS e IP globali.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.
> 
> 

Con Twilio è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Twilio. 
* Usare le azioni per recuperare un messaggio, elencare i messaggi e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando si recupera un nuovo messaggio di Twilio, è possibile usarlo come flusso di lavoro del bus di servizio. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore Twilio include le azioni seguenti. Non sono disponibili trigger. 

| Trigger | Azioni |
| --- | --- |
| Nessuno |<ul><li>Recupera messaggio</li><li>Elenca messaggi</li><li>Invia messaggio</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML. 

## <a name="create-a-connection-to-twilio"></a>Creare una connessione a Twilio
Quando si aggiunge questo connettore alle app per la logica, immettere i valori di Twilio seguenti:

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Account ID |Sì |Immettere l'ID dell'account Twilio |
| Access Token |Sì |Immettere Il token di accesso di Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

In mancanza di un token di accesso, vedere [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) per crearne uno.

> [!TIP]
> È possibile usare la stessa connessione di Twilio in altre app per la logica.
> 
> 

## <a name="swagger-rest-api-reference"></a>Riferimento all'API REST di Swagger
#### <a name="this-documentation-is-for-version-10"></a>Questa documentazione è relativa alla versione 1.0
### <a name="get-message"></a>Recupera messaggio
Restituisce un singolo messaggio offerto dall'ID del messaggio specificato.  
```GET: /Messages/{MessageId}.json```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| MessageId |string |Sì |path |Nessuno |ID del messaggio |

### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 404 |Messaggio non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

### <a name="list-messages"></a>Elenca messaggi
Restituisce un elenco di messaggi associati all'account.  
```GET: /Messages.json```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| To |string |no |query |Nessuno |Al numero di telefono |
| Da |string |no |query |Nessuno |Dal numero di telefono |
| DateSent |string |no |query |Nessuno |Mostra solo i messaggi inviati in questa data (in formato GMT), specificata come AAAA-MM-GG. Ad esempio: DateSent = 2009-07-06. È inoltre possibile specificare la disuguaglianza, ad esempio DateSent<=AAAA-MM-GG per i messaggi che sono stati inviati a mezzanotte o prima in una data e DateSent>=AAAA-MM-GG per i messaggi inviati a mezzanotte o dopo in una data. |
| PageSize |integer |no |query |50 |Numero di risorse da restituire in ogni pagina dell'elenco. Il valore predefinito è 50 |
| Page |integer |no |query |0 |Numero di pagina. Il valore predefinito è 0. |

### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

### <a name="send-message"></a>Invia messaggio
Invia un nuovo messaggio a un numero di telefono cellulare.  
```POST: /Messages.json```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |Sì |body |Nessuno |Messaggio da inviare |

### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetto
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest: modello di richiesta per l'operazione di invio del messaggio
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Da |string |Sì |
| To |string |Sì |
| body |string |sì |
| media_url |array |no |
| status_callback |string |no |
| messaging_service_sid |string |no |
| application_sid |string |no |
| max_price |string |no |

#### <a name="message-model-for-message"></a>Message: modello per il messaggio
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| body |string |no |
| Da |string |no |
| To |string |no |
| status |string |no |
| sid |string |no |
| account_sid |string |no |
| api_version |string |no |
| num_segments |string |no |
| num_media |string |no |
| date_created |string |no |
| date_sent |string |no |
| date_updated |string |no |
| direction |string |no |
| error_code |string |no |
| error_message |string |no |
| price |string |no |
| price_unit |string |no |
| Uri |string |no |
| subresource_uris |array |no |
| messaging_service_sid |string |no |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList: modello di risposta per l'operazione di elenco dei messaggi
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| messages |array |no |
| Page |integer |no |
| page_size |integer |no |
| num_pages |integer |no |
| Uri |string |no |
| first_page_uri |string |no |
| next_page_uri |string |no |
| total |integer |no |
| previous_page_uri |string |no |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList: modello di risposta per l'operazione di elenco dei messaggi
| Nome proprietà | Tipo di dati | Obbligatoria |
| --- | --- | --- |
| incoming_phone_numbers |array |no |
| Page |integer |no |
| page_size |integer |no |
| num_pages |integer |no |
| Uri |string |no |
| first_page_uri |string |no |
| next_page_uri |string |no |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest: modello di richiesta per l'operazione di aggiunta dei numeri in ingresso
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| PhoneNumber |string |Sì |
| AreaCode |string |no |
| FriendlyName |string |no |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber: numero di telefono in ingresso
| Nome proprietà | Tipo di dati | Obbligatoria |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| sid |string |no |
| account_sid |string |no |
| date_created |string |no |
| date_updated |string |no |
| capabilities |non definito |no |
| status_callback |string |no |
| status_callback_method |string |no |
| api_version |string |no |

#### <a name="capabilities-phone-number-capabilities"></a>Capabilities: funzionalità dei numeri di telefono
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| mms |boolean |no |
| sms |boolean |no |
| voice |boolean |no |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers: numeri di telefono disponibili
| Nome proprietà | Tipo di dati | Obbligatoria |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| lata |string |no |
| latitudine |string |no |
| longitudine |string |no |
| postal_code |string |no |
| rate_center |string |no |
| region |string |no |
| mms |boolean |no |
| sms |boolean |no |
| voice |boolean |no |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords: classe dei record di utilizzo
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| category |string |no |
| utilizzo |string |no |
| usage_unit |string |no |
| Descrizione |string |no |
| price |number |no |
| price_unit |string |no |
| count |string |no |
| count_unit |string |no |
| start_date |string |no |
| end_date |string |no |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


