<properties
	pageTitle="Usare le azioni di richiesta e risposta | Microsoft Azure"
	description="Failover del trigger e dell'azione di richiesta e risposta in un'app per la logica di Azure"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introduzione ai componenti di richiesta e risposta

Con i componenti di richiesta e risposta in un'app per la logica è possibile rispondere in tempo reale a eventi come:

- Rispondere a una richiesta HTTP con i dati di un database locale tramite un'app per la logica.
- Attivare un'app per la logica da un evento webhook esterno.
- Chiamare un'app per la logica con un'azione di richiesta e risposta dall'interno di un'altra app per la logica

Per informazioni su come iniziare a usare le azioni di richiesta e risposta in un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usare il trigger di richiesta HTTP

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](connectors-overview.md).

Ecco una sequenza di esempio di come configurare una richiesta HTTP nella finestra di progettazione dell'app per la logica.

1. Aggiungere il trigger **Request - When an HTTP request is received** (Richiesta - Quando viene ricevuta una richiesta HTTP) all'app per la logica
	- È possibile facoltativamente fornire uno schema JSON (usando uno strumento come [jsonschema.net](http://jsonschema.net)) per il corpo della richiesta. In questo modo la finestra di progettazione potrà generare i token per le proprietà nella richiesta HTTP.
1. Aggiungere un'altra azione in modo da poter salvare l'app per la logica
1. Dopo il salvataggio, è possibile ottenere l'URL della richiesta HTTP dalla scheda di richiesta
1. Un HTTP POST (è possibile usare uno strumento come [Postman](https://www.getpostman.com/)) all'URL attiverà l'app per la logica

>[AZURE.NOTE] Se non viene definita un'azione di risposta, una risposta `202 ACCEPTED` verrà restituita immediatamente al chiamante. È possibile usare l'azione di risposta per personalizzare una risposta.

![Trigger di risposta](./media/connectors-native-reqres/using-trigger.png)

## Usare l'azione di risposta HTTP
	
L'azione di risposta HTTP è valida solo quando viene usata in un flusso di lavoro attivato da una richiesta HTTP. Se non viene definita un'azione di risposta, una risposta `202 ACCEPTED` verrà restituita immediatamente al chiamante. È possibile aggiungere un'azione di risposta in qualsiasi passaggio nel flusso di lavoro. L'app per la logica manterrà aperta la richiesta in ingresso per una risposta solo per 1 minuto. Dopo 1 minuto, se non è stata inviata alcuna risposta dal flusso di lavoro (e nella definizione è presente un'azione di risposta), verrà restituito un `504 GATEWAY TIMEOUT` al chiamante. Ecco come aggiungere un'azione di risposta HTTP:

1. Selezionare il pulsante **Nuovo passaggio**
1. Selezionare **Add an action** (Aggiungi azione)
1. Nella casella di ricerca azione digitare "Response" (Risposta) per elencare l'azione di risposta

	![Selezionare l'azione di risposta](./media/connectors-native-reqres/using-action-1.png)

1. Aggiungere i parametri richiesti per il messaggio di risposta HTTP

	![Completare l'azione di risposta](./media/connectors-native-reqres/using-action-2.png)

1. Fare clic su Salva nell'angolo in alto a sinistra della barra degli strumenti per salvare e pubblicare (attivare) l'app per la logica

---

## Dettagli tecnici

Di seguito sono riportati i dettagli per i trigger e le azioni supportate da questo connettore.

## Trigger di richiesta

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](connectors-overview.md). È disponibile un solo trigger di richiesta.

|Trigger|Descrizione|
|---|---|
|Richiesta|Quando viene ricevuta una richiesta HTTP|

## Azione di risposta

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) Esiste una sola azione di risposta che può essere usata solo quando è accompagnata da un trigger di richiesta.

|Azione|Descrizione|
|---|---|
|Response|Restituisce una risposta alla richiesta HTTP correlata|

### Informazioni dettagliate sulle azioni

Di seguito sono riportate le informazioni su ognuna delle azioni, i relativi campi di input obbligatori e facoltativi e i corrispondenti dettagli di output associati al loro uso.

#### Trigger di richiesta
Trigger da una richiesta HTTP in ingresso L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Schema JSON|schema|Lo schema JSON del corpo della richiesta HTTP|
<br>

**Dettagli dell'output**

Richiesta

|Nome proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Headers|oggetto|Intestazioni della richiesta|
|Corpo|oggetto|Oggetto della richiesta|

#### Azione di risposta

Risposta HTTP L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Codice di stato*|statusCode|Il codice di stato HTTP|
|Headers|headers|Un oggetto JSON delle intestazioni HTTP da includere|
|Corpo|body|Il corpo della risposta|

## Passaggi successivi

Di seguito sono riportate informazioni dettagliate su come procedere con le app per la logica e la nostra community.

## Creare un'app per la logica

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando il nostro [elenco di API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->