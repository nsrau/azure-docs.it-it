<properties
pageTitle="Usare l'API del bus di servizio di Azure nelle app per la logica | Microsoft Azure"
description="Introduzione all'uso dell'API del bus di servizio di Azure (connettore) nelle app per la logica del Servizio app di Microsoft Azure"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="03/16/2016"
ms.author="deonhe"/>

# Introduzione all'API del bus di servizio di Azure

Connettersi al bus di servizio di Azure per inviare e ricevere messaggi. È possibile eseguire varie azioni, ad esempio inviare alla coda, inviare all'argomento, ricevere dalla coda, ricevere dalla sottoscrizione, e così via.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione dello schema 2014-12-01-preview, fare clic su [Bus di servizio di Azure](../app-service-logic/app-service-logic-connector-azureservicebus.md).

Con il bus di servizio di Azure è possibile:

* Compilare app per la logica  

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Informazioni su trigger e azioni

L'API del bus di servizio di Azure include trigger e può essere usata come azione. Tutte le API supportano dati nei formati JSON e XML.

 Nell'API del bus di servizio di Azure sono disponibili le azioni e/o i trigger seguenti:

### Azioni del bus di servizio di Azure
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|SendMessage|Invia un messaggio alla coda o all'argomento del bus di servizio di Azure.|
### Trigger del bus di servizio di Azure
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|GetMessageFromQueue|Recupera un nuovo messaggio dalla coda del bus di servizio di Azure.|
|GetMessageFromTopic|Recupera un nuovo messaggio dalla sottoscrizione dell'argomento del bus di servizio di Azure.|


## Creare una connessione al bus di servizio di Azure
Per usare l'API del bus di servizio di Azure, creare prima di tutto una **connessione**, quindi indicare i dettagli di queste proprietà:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|ConnectionString|Sì|Fornire una stringa di connessione per il bus di servizio di Azure|  

Seguire questi passaggi per creare una **connessione** bus di servizio che sarà possibile usare nelle app per la logica:

1. Selezionare **Ricorrenza**
2. Specificare una frequenza in **Frequency** e un intervallo in **Interval** ![Configurare il Bus di servizio][1] 
3. Selezionare **Add an action** ![Configurare il Bus di servizio][2]   
4. Immettere **Bus di servizio** nella casella di ricerca e attendere che la ricerca restituisca tutte le voci con Bus di servizio nel nome
5. Selezionare **Service Bus - Send message** ![Configurare il Bus di servizio][3]
7. Specificare un nome e una stringa di connessione in **Connection name** e **Connection string**, quindi fare clic su **Create connection**: ![Configurare il Bus di servizio][4]
7. Dopo aver creata la connessione, verrà visualizzata la finestra di dialogo **Invia messaggio**. Immettere tutte le informazioni necessarie per l'invio di un messaggio. ![Configurare il Bus di servizio][5]
8. Selezionare **Salva** nel menu in alto per salvare il lavoro.    

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento sulle API REST del bus di servizio di Azure
#### Questa documentazione è relativa alla versione 1.0


### Invia un messaggio alla coda o all'argomento del bus di servizio di Azure.
**```POST: /{entityName}/messages```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|message| |yes|body|nessuno|Messaggio del bus di servizio|
|entityName|string|yes|path|nessuno|Nome della coda o dell'argomento|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Recupera un nuovo messaggio dalla coda del bus di servizio di Azure.
**```GET: /{queueName}/messages/head```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|queueName|string|yes|path|nessuno|Nome della coda.|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Recupera un nuovo messaggio dalla sottoscrizione dell'argomento del bus di servizio di Azure.
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|topicName|string|yes|path|nessuno|Nome dell'argomento.|
|subscriptionName|string|yes|path|nessuno|Nome della sottoscrizione dell'argomento.|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



## Definizioni degli oggetti: 

 **ServiceBusMessage**: messaggio che include il contenuto e le proprietà

Proprietà obbligatorie per ServiceBusMessage:

ContentTransferEncoding

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ContentData|string|
|ContentType|string|
|ContentTransferEncoding|string|
|Proprietà|oggetto|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-servicebus/connectionconfig1.png
[2]: ./media/connectors-create-api-servicebus/connectionconfig2.png
[3]: ./media/connectors-create-api-servicebus/connectionconfig3.png
[4]: ./media/connectors-create-api-servicebus/connectionconfig4.png
[5]: ./media/connectors-create-api-servicebus/connectionconfig5.png
[6]: ./media/connectors-create-api-servicebus/connectionconfig6.png

<!---HONumber=AcomDC_0323_2016-->