<properties
	pageTitle="Connettore webhook dell'app per la logica | Microsoft Azure"
	description="Panoramica dell'azione e dei trigger webhook per l'esecuzione di azioni come Filter Array."
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
   ms.date="07/21/2016"
   ms.author="jehollan"/>

# Introduzione al connettore webhook

Con l'azione e il trigger webhook è possibile attivare, sospendere e riprendere flussi per:

- Eseguire l'attivazione da [Hub eventi di Azure immediatamente alla ricezione di un elemento](https://github.com/logicappsio/EventHubAPI)
- Attendere l'approvazione prima di continuare un flusso di lavoro

Le informazioni sulla creazione di un'API che supporti la sottoscrizione di un webhook sono disponibili [in questo articolo sulla creazione di connettori delle app per la logica](../app-service-logic/app-service-logic-create-api-app.md).

---

## Usare il trigger webhook

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](connectors-overview.md). Un trigger webhook è particolarmente utile poiché non fa affidamento sul polling per i nuovi elementi, come il [trigger di richiesta](./connectors-native-reqres.md) che l'app per la logica genererà nell'istante in cui si verifica un evento. Questa operazione viene eseguita registrando un *URL callback* a un servizio che consente di attivare l'app per la logica in base alle esigenze.

Ecco una sequenza di esempio di come configurare un trigger HTTP nella finestra di progettazione dell'app per la logica. Si presuppone che un'API sia già stata distribuita o che si stia accedendo all'API che segue [il modello di "subscribe" e "unsubscribe" del webhook usato nelle app per la logica](../app-service-logic/app-service-logic-create-api-app.md#webhook-triggers). La chiamata "subscribe" viene eseguita ogni volta che un'app per la logica viene salvata con un nuovo webhook o che passa da uno stato disabilitato ad abilitato. La chiamata "unsubscribe"viene eseguita ogni volta che un trigger webhook dell'app per la logica viene rimosso e salvato o passa dallo stato abilitato a disabilitato.

1. Aggiungere il trigger **HTTP Webhook** come primo passaggio in un'app per la logica
1. Specificare i parametri per le chiamate "subscribe" e "unsubscribe" del webhook
	- Questa operazione segue lo stesso modello del formato dell'[azione HTTP](./connectors-native-http.md)

	![Trigger HTTP](./media/connectors-native-webhook/using-trigger.png)

1. Aggiungere almeno un'azione
1. Fare clic sull'opzione di salvataggio per pubblicare l'app per la logica; in questo modo verrà chiamato l'endpoint "subscribe" con l'URL callback necessario per attivare questa app per la logica
1. Ogni volta che il servizio esegue un `HTTP POST` all'URL callback, verrà attivata l'app per la logica, inclusi i dati passati nella richiesta

## Usare l'azione webhook
	
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) Un'azione webhook è particolarmente utile poiché registra un *URL callback* con un servizio e attende che l'URL venga chiamato prima di riprendere le operazioni. ["Send Approval Email"](./connectors-create-api-office365-outlook.md) è un esempio di connettore che segue questo modello. È possibile estendere questo modello in qualsiasi servizio tramite l'azione webhook. Si presuppone che un'API sia già stata distribuita o che si stia accedendo all'API che segue [il modello di "subscribe" e "unsubscribe" del webhook usato nelle app per la logica](../app-service-logic/app-service-logic-create-api-app.md#webhook-actions). Ogni volta che un'app per la logica esegue l'azione webhook, viene eseguita la chiamata "subscribe". Ogni volta che un'esecuzione viene annullata in attesa di una risposta o prima che si verifichi il timeout dell'app per la logica, viene eseguita la chiamata "unsubscribe".

Per creare un'azione webhook:

1. Selezionare il pulsante **Nuovo passaggio**
1. Selezionare **Aggiungi un'azione**
1. Nella casella di ricerca azione digitare "webhook" per elencare l'azione **HTTP Webhook**

	![Selezionare l'azione di query](./media/connectors-native-webhook/using-action-1.png)

1. Specificare i parametri per le chiamate "subscribe" e "unsubscribe" del webhook
	- Questa operazione segue lo stesso modello del formato dell'[azione HTTP](./connectors-native-http.md)

	![Completare l'azione di query](./media/connectors-native-webhook/using-action-2.png)

	- In fase di runtime l'app per la logica chiamerà l'endpoint di "subscribe" quando ha raggiunto il passaggio

1. Fare clic su Salva nell'angolo in alto a sinistra della barra degli strumenti per salvare e pubblicare (attivare) l'app per la logica

---

## Dettagli tecnici

Di seguito sono riportati i dettagli per i trigger e le azioni supportate da questo webhook.

## Trigger webhook

Un trigger è un'operazione per avviare un flusso di lavoro. [Altre informazioni sui trigger.](connectors-overview.md) Questo connettore presenta 1 trigger.

|Azione|Descrizione|
|---|---|
|HTTP Webhook|Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per attivare l'app per la logica in base alle esigenze.|

### Dettagli del trigger

Il connettore webhook include 1 trigger possibile. Di seguito sono riportate le informazioni sull'azione, i relativi campi di input obbligatori e facoltativi e i corrispondenti dettagli di output associati al loro uso.

#### HTTP Webhook
Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per attivare l'app per la logica in base alle esigenze. L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Subscribe Method*|statico|Metodo HTTP da usare per la richiesta di sottoscrizione|
|Subscribe URI*|Uri|URI HTTP da usare per la richiesta di sottoscrizione|
|Unsubscribe Method*|statico|Metodo HTTP da usare per annullare la richiesta di sottoscrizione|
|Unsubscribe URI*|Uri|URI HTTP da usare per annullare la richiesta di sottoscrizione|
|Subscribe Body|body|Request body HTTP per la sottoscrizione|
|Subscribe Headers|headers|Intestazioni della richiesta HTTP per la sottoscrizione|
|Subscribe Authentication|authentication|Autenticazione HTTP da usare per la sottoscrizione. Vedere [Connettore HTTP](./connectors-native-http.md#authenication) per informazioni dettagliate|
|Unsubscribe Body|body|Request body HTTP per annullare la sottoscrizione|
|Unsubscribe Headers|headers|Intestazioni della richiesta HTTP per annullare la sottoscrizione|
|Unsubscribe Authentication|authentication|Autenticazione HTTP da usare per annullare la sottoscrizione. Vedere [Connettore HTTP](./connectors-native-http.md#authenication) per informazioni dettagliate|
<br>

**Dettagli dell'output**

Richiesta Webhook

|Nome proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Headers|object|Intestazioni della richiesta webhook|
|Corpo|object|Oggetto della richiesta webhook|
|Codice di stato|int|Codice di stato della richiesta webhook|

## Azioni webhook

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) Il connettore presenta 1 azione possibile.

|Azione|Descrizione|
|---|---|
|HTTP Webhook|Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per riprendere un passaggio del flusso di lavoro in base alle esigenze.|

### Informazioni dettagliate sulle azioni

Il connettore webhook include 1 azione possibile. Di seguito sono riportate le informazioni sull'azione, i relativi campi di input obbligatori e facoltativi e i corrispondenti dettagli di output associati al loro uso.

#### HTTP Webhook
Sottoscrivere un URL callback a un servizio in grado di chiamare l'URL per riprendere un passaggio del flusso di lavoro in base alle esigenze. L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Subscribe Method*|statico|Metodo HTTP da usare per la richiesta di sottoscrizione|
|Subscribe URI*|Uri|URI HTTP da usare per la richiesta di sottoscrizione|
|Unsubscribe Method*|statico|Metodo HTTP da usare per annullare la richiesta di sottoscrizione|
|Unsubscribe URI*|Uri|URI HTTP da usare per annullare la richiesta di sottoscrizione|
|Subscribe Body|body|Request body HTTP per la sottoscrizione|
|Subscribe Headers|headers|Intestazioni della richiesta HTTP per la sottoscrizione|
|Subscribe Authentication|authentication|Autenticazione HTTP da usare per la sottoscrizione. Vedere [Connettore HTTP](./connectors-native-http.md#authentication) per informazioni dettagliate|
|Unsubscribe Body|body|Request body HTTP per annullare la sottoscrizione|
|Unsubscribe Headers|headers|Intestazioni della richiesta HTTP per annullare la sottoscrizione|
|Unsubscribe Authentication|authentication|Autenticazione HTTP da usare per annullare la sottoscrizione. Vedere [Connettore HTTP](./connectors-native-http.md#authentication) per informazioni dettagliate|
<br>

**Dettagli dell'output**

Richiesta Webhook

|Nome proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Headers|object|Intestazioni della richiesta webhook|
|Corpo|object|Oggetto della richiesta webhook|
|Codice di stato|int|Codice di stato della richiesta webhook|

---

## Passaggi successivi

Di seguito sono riportate informazioni dettagliate su come procedere con le app per la logica e la nostra community.

## Creare un'app per la logica

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l'[elenco di API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->