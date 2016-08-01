<properties
	pageTitle="Aggiungere l'azione HTTP alle app per la logica | Microsoft Azure"
	description="Panoramica dell'azione HTTP con proprietà"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# Introduzione all'azione HTTP

Con l'azione HTTP è possibile estendere i flussi di lavoro per l'organizzazione e comunicare con qualsiasi endpoint su HTTP.

- Creare flussi di lavoro con app per la logica che si attivano (trigger) quando un sito Web che si gestisce si blocca.
- Comunicare con qualsiasi endpoint su HTTP per estendere i flussi di lavoro ad altri servizi.

Per informazioni su come iniziare a usare un'azione HTTP in un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usare un trigger HTTP

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](connectors-overview.md).

Ecco una sequenza di esempio di come configurare un trigger HTTP nella finestra di progettazione dell'app per la logica.

1. Aggiungere il trigger HTTP all'app per la logica
1. Inserire i parametri per l'endpoint HTTP di cui si desidera eseguire il polling
1. Modificare l'intervallo di ricorrenza che stabilisce la frequenza del polling
1. L'app per la logica ora si attiva con il contenuto restituito durante ogni controllo.

![Trigger HTTP](./media/connectors-native-http/using-trigger.png)

### Come funziona il trigger HTTP

Il trigger HTTP eseguirà una chiamata a un endpoint HTTP a intervalli ricorrenti. Per impostazione predefinita, il codice di risposta HTTP < 300 genererà un'esecuzione dell'app per la logica. È possibile aggiungere una condizione nella visualizzazione codice che verrà valutata dopo la chiamata HTTP per determinare se l'app per la logica deve essere eseguita. Di seguito è riportato un esempio di un trigger HTTP che verrà attivato ogni volta che il codice di stato restituito è maggiore o uguale a `400`.

```javascript
"Http": 
{
	"conditions": [
		{
			"expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
		}
	],
	"inputs": {
		"method": "GET",
		"uri": "https://blogs.msdn.microsoft.com/logicapps/",
		"headers": {
			"accept-language": "en"
		}
	},
	"recurrence": {
		"frequency": "Second",
		"interval": 15
	},
	"type": "Http"
}
```

I dettagli completi sui parametri del trigger HTTP [sono disponibili in MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## Usare un'azione HTTP
	
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md)

1. Selezionare il pulsante **Nuovo passaggio**
1. Selezionare **Add an action** (Aggiungi azione)
1. Nella casella di ricerca azione digitare "HTTP" per elencare l'azione HTTP

	![Selezionare l'azione HTTP](./media/connectors-native-http/using-action-1.png)

1. Aggiungere i parametri richiesti per la chiamata HTTP

	![Completare l'azione HTTP](./media/connectors-native-http/using-action-2.png)

1. Fare clic su Salva nell'angolo in alto a sinistra della barra degli strumenti per salvare e pubblicare (attivare) l'app per la logica

---

## Dettagli tecnici

Di seguito sono riportati i dettagli per i trigger e le azioni supportate da questo connettore.

## Trigger HTTP

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](connectors-overview.md). Il connettore HTTP presenta 1 trigger.

|Trigger|Descrizione|
|---|---|
|HTTP|Esegue una chiamata HTTP e restituisce il contenuto della risposta|

## Azioni HTTP

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) Il connettore HTTP presenta 1 azione possibile.

|Azione|Descrizione|
|---|---|
|HTTP|Esegue una chiamata HTTP e restituisce il contenuto della risposta|

### Informazioni dettagliate sulle azioni

Il connettore HTTP include 1 azione possibile. Di seguito sono riportate le informazioni su ognuna delle azioni, i relativi campi di input obbligatori e facoltativi e i corrispondenti dettagli di output associati al loro uso.

#### Richiesta HTTP
Eseguire una richiesta HTTP in uscita. L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Metodo*|statico|Verbo HTTP da usare|
|URI*|Uri|URI per la richiesta HTTP|
|Headers|headers|Un oggetto JSON delle intestazioni HTTP da includere|
|Corpo|body|Il corpo della richiesta HTTP|
<br>

**Dettagli dell'output**

Risposta HTTP

|Nome proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Headers|oggetto|Intestazioni della risposta|
|Corpo|oggetto|Oggetto della risposta|
|Codice di stato|int|Stato codice HTTP|

### Risposte HTTP

Quando si eseguono chiamate a varie azioni, è possibile ottenere determinate risposte. Di seguito è riportata una tabella contenente le risposte e le descrizioni corrispondenti.

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|

---

## Passaggi successivi

Di seguito sono riportate informazioni dettagliate su come procedere con le app per la logica e la nostra community.

## Creare un'app per la logica

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando il nostro [elenco di API](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->