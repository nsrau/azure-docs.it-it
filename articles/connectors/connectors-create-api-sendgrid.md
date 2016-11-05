---
title: SendGrid | Microsoft Docs
description: Creare app per la logica in Servizio app di Azure. Il provider di connessione SendGrid consente di inviare messaggi di posta elettronica e gestire elenchi di destinatari.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# Introduzione al connettore SendGrid
Il provider di connessione SendGrid consente di inviare messaggi di posta elettronica e gestire elenchi di destinatari.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Il connettore SendGrid può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore SendGrid sono disponibili le azioni seguenti. Non sono disponibili trigger.

### Azioni di SendGrid
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |Invia un messaggio di posta elettronica tramite API SendGrid (limite di 10.000 destinatari) |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |Aggiunge un unico destinatario a un elenco di destinatari |

## Creare una connessione a SendGrid
Per creare app per la logica con SendGrid, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| ApiKey |Sì |Fornisce la chiave API per SendGrid |

> [!INCLUDE [Passaggi per creare una connessione a SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

## Informazioni di riferimento per SendGrid
Si applica alla versione 1.0

## SendEmail
Invio di posta elettronica: invia un messaggio di posta elettronica tramite API SendGrid (limite di 10.000 destinatari)

```POST: /api/mail.send.json```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| richiesta | |sì |body |nessuno |Messaggio di posta elettronica da inviare |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 429 |Numero eccessivo di richieste |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## AddRecipientToList
Aggiunta destinatario all'elenco: aggiunge un unico destinatario a un elenco di destinatari

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| listId |stringa |yes |path |nessuno |ID univoco dell'elenco di destinatari |
| recipientId |stringa |yes |path |nessuno |ID univoco del destinatario |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## Definizioni oggetti
### EmailRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| from |stringa |Sì |
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

### EmailResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| message |string |No |

### RecipientLists
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| lists |array |No |

### RecipientList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| name |stringa |No |
| recipient\_count |integer |No |

### Recipients
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| recipients |array |No |

### Recipient
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| email |stringa |No |
| last\_name |string |No |
| first\_name |stringa |No |
| id |stringa |No |

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->