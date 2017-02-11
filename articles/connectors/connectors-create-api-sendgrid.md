---
title: SendGrid | Microsoft Docs
description: Creare app per la logica in Servizio app di Azure. Il provider di connessione SendGrid consente di inviare messaggi di posta elettronica e gestire elenchi di destinatari.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ad35f6590a39972ec9be7168059738b6daae0574


---
# <a name="get-started-with-the-sendgrid-connector"></a>Introduzione al connettore SendGrid
Il provider di connessione SendGrid consente di inviare messaggi di posta elettronica e gestire elenchi di destinatari.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica. 
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore SendGrid può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML. 

 Nel connettore SendGrid sono disponibili le azioni seguenti. Non sono disponibili trigger.

### <a name="sendgrid-actions"></a>Azioni di SendGrid
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |Invia un messaggio di posta elettronica tramite API SendGrid (limite di 10.000 destinatari) |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |Aggiunge un unico destinatario a un elenco di destinatari |

## <a name="create-a-connection-to-sendgrid"></a>Creare una connessione a SendGrid
Per creare app per la logica con SendGrid, è prima necessario creare una **connessione**, quindi fornire i dettagli per le proprietà seguenti: 

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| ApiKey |Sì |Fornisce la chiave API per SendGrid |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

## <a name="reference-for-sendgrid"></a>Informazioni di riferimento per SendGrid
Si applica alla versione 1.0

## <a name="sendemail"></a>SendEmail
Invio di posta elettronica: invia un messaggio di posta elettronica tramite API SendGrid (limite di 10.000 destinatari) 

```POST: /api/mail.send.json``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| richiesta | |sì |body |nessuno |Messaggio di posta elettronica da inviare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 429 |Numero eccessivo di richieste |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="addrecipienttolist"></a>AddRecipientToList
Aggiunta destinatario all'elenco: aggiunge un unico destinatario a un elenco di destinatari 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| listId |stringa |Sì |path |nessuno |ID univoco dell'elenco di destinatari |
| recipientId |stringa |Sì |path |nessuno |ID univoco del destinatario |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetti
### <a name="emailrequest"></a>EmailRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Da |stringa |Sì |
| fromname |stringa |No |
| to |string |Sì |
| toname |stringa |No |
| subject |stringa |Sì |
| body |stringa |Sì |
| ishtml |boolean |No |
| cc |string |No |
| ccname |stringa |No |
| bcc |stringa |No |
| bccname |stringa |No |
| replyto |stringa |No |
| date |stringa |No |
| headers |string |No |
| input |array |No |
| filenames |array |No |

### <a name="emailresponse"></a>EmailResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| message |string |No |

### <a name="recipientlists"></a>RecipientLists
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| lists |array |No |

### <a name="recipientlist"></a>RecipientList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| name |stringa |No |
| recipient_count |integer |No |

### <a name="recipients"></a>Recipients
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| recipients |array |No |

### <a name="recipient"></a>Recipient
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| email |string |No |
| last_name |string |No |
| first_name |stringa |No |
| id |string |No |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


