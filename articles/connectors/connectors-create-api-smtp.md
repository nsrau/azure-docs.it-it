<properties
pageTitle="SMTP | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. Connettersi a SMTP per inviare messaggi di posta elettronica."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# Introduzione al connettore SMTP

Connettersi a SMTP per inviare messaggi di posta elettronica.

Per usare [qualsiasi connettore](./apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi a un server SMTP

Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](./connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a un server SMTP, è necessaria innanzitutto una *connessione* SMTP. Per creare una connessione, è necessario fornire le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Pertanto, nell'esempio di SMTP, per creare la connessione SMTP sono necessarie le credenziali riguardanti il nome della connessione, l'indirizzo del server SMTP e le informazioni di accesso utente. [Altre informazioni sulle connessioni]()

### Creare una connessione a SMTP

>[AZURE.INCLUDE [Passaggi per creare una connessione a SMTP](../../includes/connectors-create-api-smtp.md)]

## Usare un trigger SMTP

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

In questo esempio, poiché SMTP non dispone di un trigger proprio, si userà il trigger **Salesforce - When an object is created** (Salesforce - Quando viene creato un oggetto). Questo trigger viene attivato quando viene creato un nuovo oggetto in Salesforce. Per questo esempio lo si imposterà in modo che, ogni volta che viene creato un nuovo cliente potenziale in Salesforce, venga eseguita un'azione *Send email* (Invia messaggio di posta elettronica) tramite il connettore SMTP con una notifica della creazione del nuovo cliente potenziale.

1. Immettere *salesforce* nella casella di ricerca della finestra di progettazione dell'app per la logica, quindi selezionare il trigger **Salesforce - When an object is created** (Salesforce - Quando viene creato un oggetto). ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)

2. Viene visualizzato il controllo **Quando viene creato un oggetto**. ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)

3. Selezionare **Tipo di oggetto** e *Lead* dall'elenco di oggetti. In questo passaggio si indica che si sta creando un trigger che invierà una notifica all’app per la logica ogni volta che viene creato un nuovo lead in Salesforce. ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)

4. Il trigger è stato creato. ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)

## Usare un'azione SMTP

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Ora che il trigger è stato aggiunto, seguire questi passaggi per aggiungere un'azione SMTP che viene eseguita quando viene creato un nuovo cliente potenziale in Salesforce.

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione che si vuole venga eseguita quando viene creato un nuovo cliente potenziale. ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)

2. Selezionare **Aggiungi un'azione**. Viene aperta la casella di ricerca ed è possibile cercare l'azione che si vuole eseguire. ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)

3. Immettere *smtp* per cercare le azioni correlate a SMTP.

4. Selezionare **SMTP - Invia messaggio di posta elettronica** come azione da eseguire quando viene creato il cliente potenziale. Si apre il blocco di controllo azione. È necessario stabilire la connessione SMTP nel blocco di progettazione se non lo si è ancora fatto. ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)

5. Immettere le informazioni di posta elettronica desiderate nel blocco **SMTP - Send Email** (SMTP - Invia messaggio di posta elettronica). ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)

6. Salvare il lavoro per attivare il flusso di lavoro.

## Dettagli tecnici

Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## Trigger di SMTP

SMTP non supporta trigger.

## Azioni di SMTP

SMTP supporta le azioni seguenti:


|Azione|Descrizione|
|--- | ---|
|[Invia messaggio di posta elettronica](connectors-create-api-smtp.md#send-email)|Questa operazione invia un messaggio di posta elettronica a uno o più destinatari.|

### Informazioni dettagliate sulle azioni

Ecco i dettagli delle azioni per questo connettore con le relative risposte:


### Invia messaggio di posta elettronica
Questa operazione invia un messaggio di posta elettronica a uno o più destinatari.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|To|To|Specificare gli indirizzi di posta elettronica separati da punto e virgola come recipient1@domain.com;destinatario2@dominio.com|
|CC|cc|Specificare gli indirizzi di posta elettronica separati da punto e virgola come recipient1@domain.com;destinatario2@dominio.com|
|Oggetto|Oggetto|Oggetto del messaggio di posta elettronica|
|Corpo|Corpo|Corpo del messaggio di posta elettronica|
|Da|Da|Indirizzo di posta elettronica del mittente come sender@domain.com|
|IsHtml|È HTML|Inviare il messaggio di posta elettronica in formato HTML (true/false)|
|Bcc|bcc|Specificare gli indirizzi di posta elettronica separati da punto e virgola come recipient1@domain.com;destinatario2@dominio.com|
|Importance|Importance|Importanza del messaggio di posta elettronica (Alta, Normale, Bassa)|
|ContentData|Dati sul contenuto degli allegati|Contenuto dei dati (con codifica base64 per i flussi e invariato per le stringhe)|
|ContentType|Tipo del contenuto degli allegati|Tipo di contenuto|
|ContentTransferEncoding|Codifica di trasferimento del contenuto per gli allegati|Codifica di trasferimento del contenuto (base64 o none)|
|FileName|Nome di file degli allegati|Nome file|
|ContentId|ID contenuto degli allegati|ID contenuto|

* indica che la proprietà è obbligatoria


## Risposte HTTP

Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP:

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto.|
|default|Operazione non riuscita.|

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->