<properties
    pageTitle="Aggiungere il connettore SMTP alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore SMTP con i parametri dell'API REST"
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
   ms.date="05/19/2016"
   ms.author="mandia"/>

# Introduzione al connettore SMTP
Connettersi a un server SMTP per inviare messaggi di posta elettronica. Il connettore SMTP può essere usato da:

- App per la logica trattate in questo argomento
- PowerApps. Vedere l'[elenco delle connessioni PowerApps](https://powerapps.microsoft.com/tutorials/connections-list/) per l'elenco completo.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con SMTP è possibile:

- Creare il flusso aziendale includendo l'invio di posta elettronica tramite SMTP. 
- Usare un'azione per inviare messaggi di posta elettronica. Questa azione ottiene una risposta e quindi rende l'output disponibile per altre azioni. Ad esempio, quando sul server FTP c'è un nuovo file, è possibile inviare tale file come allegato di posta elettronica con SMTP. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Per il connettore SMTP sono disponibili le azioni seguenti. Non sono disponibili trigger.

|Trigger | Azioni|
|--- | ---|
|Nessuno | Send email|

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a SMTP

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a SMTP](../../includes/connectors-create-api-smtp.md)]

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

<!---HONumber=AcomDC_0525_2016-->