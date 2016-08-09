<properties
pageTitle="SendGrid | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. Il provider di connessione SendGrid consente di inviare messaggi di posta elettronica e gestire elenchi di destinatari."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# Introduzione al connettore SendGrid



Il connettore SendGrid può essere usato da:

- [App per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flusso](http://flows.microsoft.com)

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore SendGrid può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore SendGrid sono disponibili le azioni e/o i trigger seguenti:

### Azioni di SendGrid
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Invia un messaggio di posta elettronica tramite API SendGrid (limite di 10.000 destinatari)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Aggiunge un unico destinatario a un elenco di destinatari|
### Trigger di SendGrid
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|


## Creare una connessione a SendGrid
Per creare app per la logica con SendGrid, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|ApiKey|Sì|Fornisce la chiave API per SendGrid|
 

>[AZURE.INCLUDE [Passaggi per creare una connessione a SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

## Informazioni di riferimento per SendGrid
Si applica alla versione: 1.0

## SendEmail
Invio di posta elettronica: invia un messaggio di posta elettronica tramite API SendGrid (limite di 10.000 destinatari)

```POST: /api/mail.send.json```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|richiesta| |sì|body|nessuno|Messaggio di posta elettronica da inviare|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|429|Numero eccessivo di richieste|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## AddRecipientToList
Aggiunta destinatario all'elenco: aggiunge un unico destinatario a un elenco di destinatari

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|listId|string|yes|path|nessuno|ID univoco dell'elenco di destinatari|
|recipientId|string|yes|path|nessuno|ID univoco del destinatario|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## Definizioni oggetti 

### EmailRequest


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|from|string|Sì |
|fromname|string|No |
|to|string|Sì |
|toname|string|No |
|subject|string|Sì |
|body|string|Sì |
|ishtml|boolean|No |
|cc|string|No |
|ccname|string|No |
|bcc|string|No |
|bccname|string|No |
|replyto|string|No |
|date|string|No |
|headers|string|No |
|input|array|No |
|filenames|array|No |



### EmailResponse


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|message|string|No |



### RecipientLists


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|lists|array|No |



### RecipientList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|name|string|No |
|recipient\_count|numero intero|No |



### Recipients


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|recipients|array|No |



### Recipient


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|email|string|No |
|last\_name|string|No |
|first\_name|string|No |
|id|string|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->