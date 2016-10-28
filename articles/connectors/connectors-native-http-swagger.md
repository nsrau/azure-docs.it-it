
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

Con l'azione HTTP + Swagger è possibile creare un connettore di prima classe a qualsiasi endpoint REST tramite un [documento di Swagger](https://swagger.io). È anche possibile estendere un'app per la logica per chiamare qualsiasi endpoint REST con un'eccellente esperienza di progettazione delle app per la logica.

Per iniziare a usare un'azione HTTP + Swagger in un'app per la logica, vedere l'articolo su come [Creare una nuova app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usare HTTP + Swagger come trigger o azione

Il trigger e l'azione HTTP + Swagger funzionano come l'[azione HTTP](connectors-native-http.md) ma forniscono un'esperienza di progettazione migliore mostrando la forma dell'API e i risultati nella finestra di progettazione dai [metadati Swagger](https://swagger.io). In aggiunta, è possibile usare HTTP + Swagger come trigger. Se si vuole implementare un trigger di poll, è necessario seguire lo schema di polling descritto in [Creazione di un'API personalizzata da usare con le app per la logica](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[Altre informazioni sui trigger e le azioni nelle app per la logica.](connectors-overview.md)

Di seguito è riportato un esempio di come usare l'operazione HTTP + Swagger come azione in un flusso di lavoro in un'app per la logica.

1. Fare clic sul pulsante **Nuovo passaggio**.
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di ricerca azione digitare **swagger** per elencare l'azione HTTP + Swagger.

	![Selezionare l'azione HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)  

4. Digitare l'URL per un documento di Swagger:
	- Per funzionare dalla finestra di progettazione app per la logica, l'URL deve essere un endpoint HTTPS e avere CORS abilitato.
	- Se il documento di Swagger non soddisfa questo requisito, è possibile usare [Archiviazione di Azure con CORS abilitato](#hosting-swagger-from-storage) per archiviare il documento.
5. Fare clic su **Avanti** per leggere e visualizzare il documento di Swagger.
6. Aggiungere i parametri richiesti per la chiamata HTTP.

	![Completare l'azione HTTP](./media/connectors-native-http-swagger/using-action-2.png)  

1. Fare clic su **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare e pubblicare (attivare) l'app per la logica.

### Ospitare Swagger da Archiviazione di Azure

Si consiglia di fare riferimento a un documento di Swagger che non sia ospitato o non soddisfi i requisiti di sicurezza e origini multiple per la finestra di progettazione. Per risolvere questo problema, è possibile archiviare il documento di Swagger in Archiviazione di Azure e abilitare CORS a fare riferimento al documento.

Ecco i passaggi per creare, configurare e archiviare i documenti di Swagger in Archiviazione di Azure:

1. [Creare un account di Archiviazione di Azure con l'archiviazione BLOB di Azure](../storage/storage-create-storage-account.md) (a tale scopo, impostare le autorizzazioni su **Accesso pubblico**).
2. Abilitare CORS nel BLOB. È possibile usare [questo script di PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) per configurare tale impostazione automaticamente.
3. Caricare il file di Swagger nel BLOB. È possibile farlo dal [portale di Azure](https://portal.azure.com) o da uno strumento come [Azure Storage Explorer](http://storageexplorer.com/).
1. Rimandare un collegamento HTTPS al documento nell'archiviazione BLOB di Azure (il collegamento segue il formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`).



## Dettagli tecnici

Di seguito sono riportati i dettagli per i trigger e le azioni supportati da questo connettore HTTP + Swagger.

## Trigger HTTP + Swagger

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger.](connectors-overview.md) Il connettore HTTP + Swagger supporta un solo trigger.

|Trigger|Descrizione|
|---|---|
|HTTP + Swagger|Esegue una chiamata HTTP e restituisce il contenuto della risposta|

## Azioni HTTP + Swagger

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) Il connettore HTTP + Swagger supporta una sola azione possibile.

|Azione|Descrizione|
|---|---|
|HTTP + Swagger|Esegue una chiamata HTTP e restituisce il contenuto della risposta|

### Informazioni dettagliate sulle azioni

Il connettore HTTP + Swagger include una sola azione possibile. Di seguito sono riportate le informazioni su ognuna delle azioni, i relativi campi di input obbligatori e facoltativi e i corrispondenti dettagli di output associati al loro uso.

#### HTTP + Swagger

Eseguire una richiesta HTTP in uscita con l'assistenza dei metadati Swagger. L'asterisco (*) indica un campo obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Metodo*|statico|Verbo HTTP da usare.|
|URI*|Uri|URI per la richiesta HTTP.|
|Headers|headers|Un oggetto JSON delle intestazioni HTTP da includere.|
|Corpo|body|Il corpo della richiesta HTTP.|
|Autenticazione|autenticazione|Autenticazione da usare per la richiesta. [Per altre informazioni, vedere HTTP](./connectors-native-http.md#authentication).|

**Dettagli dell'output**

Risposta HTTP

|Nome proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Headers|object|Intestazioni della risposta|
|Corpo|object|Oggetto della risposta|
|Codice di stato|int|Stato codice HTTP|

### Risposte HTTP

Quando si eseguono chiamate a varie azioni, è possibile ottenere determinate risposte. Di seguito è riportata una tabella contenente le risposte e le descrizioni corrispondenti.

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Richiesta non valida|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto.|

---

## Passaggi successivi

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l'[elenco di API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->