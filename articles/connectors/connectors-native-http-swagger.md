<properties
	pageTitle="Aggiungere l'azione HTTP + Swagger alle app per la logica | Microsoft Azure"
	description="Panoramica dell'azione e delle operazioni di HTTP + Swagger"
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

# Introduzione all'azione HTTP + Swagger

Con l'azione HTTP + Swagger è possibile creare un connettore di prima classe a qualsiasi endpoint REST tramite un [documento di Swagger](https://swagger.io).

- Estendere un'app per la logica per chiamare qualsiasi endpoint REST con un'esperienza di progettazione eccellente

Per informazioni su come iniziare a usare un'azione HTTP + Swagger in un'app per la logica, vedere l'articolo su come [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usare HTTP + Swagger come trigger o azione

Il trigger e azione HTTP + Swagger funziona come l'[azione HTTP](connectors-native-http.md) ma fornisce un'esperienza di progettazione migliore mostrando la forma dell'API e i risultati nella finestra di progettazione dai [metadati swagger](https://swagger.io). Inoltre è possibile usare HTTP + Swagger come trigger e, se si vuole implementare un trigger di poll, è necessario seguire lo schema di polling [descritto in questo documento](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).
	
[Altre informazioni sui trigger e le azioni nelle app per la logica.](connectors-overview.md)

Di seguito è riportato un esempio di impiego dell'operazione HTTP + Swagger come azione in un flusso di lavoro.

1. Selezionare il pulsante **Nuovo passaggio**
1. Selezionare **Aggiungi un'azione**
1. Nella casella di ricerca azione digitare "swagger" per elencare l'azione HTTP + Swagger

	![Selezionare l'azione HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)

1. Immettere l'URL per un documento di Swagger
	- L'URL deve essere un endpoint HTTPS e avere CORS abilitato per l'uso nella finestra di progettazione. Se il documento di Swagger non soddisfa questa condizione, è possibile usare [Archiviazione di Azure con CORS abilitato](#hosting-swagger-from-storage) per archiviare il documento.
1. Fare clic su Avanti per leggere e visualizzare il documento di Swagger
1. Aggiungere i parametri richiesti per la chiamata HTTP

	![Completare l'azione HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Fare clic su Salva nell'angolo in alto a sinistra della barra degli strumenti per salvare e pubblicare (attivare) l'app per la logica

### Hosting di Swagger dall'Archiviazione

Si consiglia di fare riferimento a un documento di Swagger che non sia ospitato o non soddisfi i requisiti di sicurezza e origini multiple necessari per l'uso nella finestra di progettazione. In alternativa, è possibile archiviare il documento di Swagger in Archiviazione di Azure e abilitare CORS a fare riferimento al documento. Ecco i passaggi per creare, configurare e archiviazione Swagger in Archiviazione di Azure:

1. [Creare un account di archiviazione di Azure con l'archiviazione BLOB](../storage/storage-create-storage-account.md) e impostare le autorizzazioni su "Accesso pubblico"
1. Abilitare CORS nel BLOB
	- È possibile usare [questo script di PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) per configurare tale impostazione automaticamente.
1. Caricare il file di Swagger nel BLOB di Azure
	- È possibile farlo dal [portale di Azure](https://portal.azure.com) o da uno strumento come [Esplora archivi di Azure](http://storageexplorer.com/)
1. Rimandare un collegamento HTTPS al documento nel BLOB di Azure (seguire il formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`)

---

## Dettagli tecnici

Di seguito sono riportati i dettagli per i trigger e le azioni supportate da questo connettore.

## Trigger HTTP + Swagger

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger.](connectors-overview.md) Il connettore HTTP + Swagger supporta 1 trigger.

|Trigger|Descrizione|
|---|---|
|HTTP + Swagger|Esegue una chiamata HTTP e restituisce il contenuto della risposta|

## Azioni HTTP + Swagger

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) Il connettore HTTP + Swagger supporta 1 azione possibile.

|Azione|Descrizione|
|---|---|
|HTTP + Swagger|Esegue una chiamata HTTP e restituisce il contenuto della risposta|

### Informazioni dettagliate sulle azioni

Il connettore HTTP + Swagger include 1 azione possibile. Di seguito sono riportate le informazioni su ognuna delle azioni, i relativi campi di input obbligatori e facoltativi e i corrispondenti dettagli di output associati al loro uso.

#### HTTP + Swagger

Eseguire una richiesta HTTP in uscita con l'assistenza dei metadati Swagger. L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Metodo*|statico|Verbo HTTP da usare|
|URI*|Uri|URI per la richiesta HTTP|
|Headers|headers|Un oggetto JSON delle intestazioni HTTP da includere|
|Corpo|body|Il corpo della richiesta HTTP|
|Autenticazione|authentication|Autenticazione da usare per la richiesta: [per informazioni dettagliate, vedere HTTP](./connectors-native-http.md#authentication)|
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

---

## Passaggi successivi

Di seguito sono riportate informazioni dettagliate su come procedere con le app per la logica e la nostra community.

## Creare un'app per la logica

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l'[elenco di API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->