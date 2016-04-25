<properties
	pageTitle="Aggiungere l'API di SMTP alle app per la logica | Microsoft Azure"
	description="Panoramica dell'API di SMTP con i parametri dell'API REST."
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Introduzione all'API di SMTP
Connettersi a un server SMTP per inviare messaggi di posta elettronica. L'API di SMTP può essere usata da:

- App per la logica

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con SMTP è possibile:

- Creare il flusso aziendale includendo l'invio di posta elettronica tramite SMTP. 
- Usare un'azione per inviare messaggi di posta elettronica. Questa azione ottiene una risposta e quindi rende l'output disponibile per altre azioni. Ad esempio, quando sul server FTP c'è un nuovo file, è possibile inviare tale file come allegato di posta elettronica con SMTP. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Per l'API SMTP sono disponibili le azioni seguenti. Non sono disponibili trigger.

|Trigger | Azioni|
|--- | ---|
|Nessuno | Send email|

Tutte le API supportano dati nei formati JSON e XML.

## Creare una connessione a SMTP
Quando si aggiunge questa API alle app per la logica, immettere i valori seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
| Nome server SMTP | Sì | Immettere il nome di dominio completo (FQDN) o l'indirizzo IP del server SMTP.|
| Nome utente |Sì |Immettere il nome utente per la connessione al server SMTP. |
| Password | Sì|Immettere la password del nome utente. |

Dopo aver creato la connessione, immettere le proprietà di SMTP, ad esempio i valori To e CC. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di SMTP in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Invia messaggio di posta elettronica
Invia un messaggio di posta elettronica a uno o più destinatari. ```POST: /SendEmail```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|emailMessage| many|yes|body|nessuno |Messaggio di posta elettronica|

## Definizioni degli oggetti

#### Posta elettronica: Messaggio di posta elettronica SMTP

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|To|string|no|
|CC|string|no|
|Oggetto|string|no|
|Corpo|string|no|
|Da|string|no|
|IsHtml|boolean|no|
|Bcc|string|no|
|Importance|string|no|
|Attachments|array|no|


#### Allegato: Allegato di posta elettronica

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|FileName|string|no|
|ContentId|string|no|
|ContentData|string|yes|
|ContentType|string|yes|
|ContentTransferEncoding|string|yes|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0413_2016-->