<properties
	pageTitle="Introduzione a Ricerca di Azure in NodeJS | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Illustra la creazione di un'applicazione di ricerca in un servizio di ricerca cloud ospitato in Azure usando NodeJS come linguaggio di programmazione."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Introduzione a Ricerca di Azure in NodeJS

Informazioni su come compilare un'applicazione di ricerca NodeJS personalizzata che utilizza Ricerca di Azure per l’esperienza di ricerca. L'esercitazione utilizza l’[API REST del servizio Ricerca di Azure](https://msdn.microsoft.com/library/dn798935.aspx) per costruire gli oggetti e le operazioni utilizzati in questo esercizio.

Sono stati utilizzati [NodeJS](https://nodejs.org) e NPM, [Sublime Text 3](http://www.sublimetext.com/3) e Windows PowerShell in Windows 8.1 per sviluppare e testare il codice.

Per eseguire questo esempio, è necessario un servizio Ricerca di Azure, a cui è possibile iscriversi nel [portale di Azure](https://portal.azure.com).

> [AZURE.TIP]Scaricare il codice sorgente per questa esercitazione in [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).

## Informazioni sui dati

L’applicazione di esempio usa i dati dei [servizi geologici degli Stati Uniti (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) con il filtro dello stato del Rhode Island per ridurre la dimensione del set di dati. Tali dati saranno usati per compilare un'applicazione di ricerca che restituisce gli edifici di riferimento quali ospedali e scuole nonché caratteristiche geologiche come fiumi, laghi e vette.

In questa applicazione, il programma **DataIndexer** compila e carica l'indice utilizzando un costrutto [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando il set di dati filtrato dei servizi geologici degli Stati Uniti da un database SQL di Azure pubblico. Nel codice del programma vengono fornite credenziali e connessioni all'origine dati online. Non è necessaria ulteriore configurazione.

> [AZURE.NOTE]A questo set di dati è stato applicato un filtro per restare sotto il limite di 10.000 documenti del livello di prezzo gratuito. Se si usa il livello standard, questo limite non si applica. Per ulteriori informazioni sulla capacità per ogni livello di prezzo, vedere [Limiti e vincoli](search-limits-quotas-capacity.md).

## Creare il servizio

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nell'indice, fare clic su **Nuovo** > **Dati e archiviazione** > **Ricerca**.

     ![][1]

3. Configurare il nome del servizio, il livello di prezzo, il gruppo di risorse, la sottoscrizione e la posizione. Queste impostazioni sono necessarie e non possono essere modificate dopo il provisioning del servizio.

     ![][2]

	- Il **nome del servizio** deve essere univoco, in lettere minuscole, lungo massimo 15 caratteri e senza spazi. Questo nome diventa parte dell'endpoint del servizio Ricerca di Azure. Vedere [Regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx) per ulteriori informazioni sulle convenzioni di denominazione.

	- Il **livello di prezzo** determina capacità e fatturazione. Entrambi i livelli offrono le stesse funzionalità, ma a livelli diversi di risorse.

		- Il livello **gratuito** viene eseguito in cluster condivisi con altri sottoscrittori. Offre capacità sufficienti per provare le esercitazioni e scrivere codice per i modelli di prova, ma non è consigliato per le applicazioni in ambienti di produzione. La distribuzione di un servizio gratuito in genere richiede solo pochi minuti.
		- Il livello **standard** viene eseguito su risorse dedicate ed è altamente scalabile. Inizialmente viene eseguito il provisioning di un servizio standard con una replica e una partizione, ma è possibile regolare la capacità una volta creato il servizio. La distribuzione di un servizio standard richiede più tempo, in genere circa 15 minuti.

	- I **gruppi di risorse** sono contenitori per i servizi e le risorse usati per uno scopo comune. Ad esempio, se si intende creare un'applicazione di ricerca personalizzata basata su Ricerca di Azure, Siti Web di Azure, il servizio di archiviazione BLOB di Azure, è possibile creare un gruppo di risorse per raggruppare questi servizi nelle pagine di gestione del portale.

	- L’opzione **Sottoscrizione** consente di scegliere tra più sottoscrizioni, se si dispone di più di una sottoscrizione.

	- La **posizione** è l'area geografica del data center. Attualmente, tutte le risorse devono essere eseguite nello stesso data center. La distribuzione di risorse tra più data center non è supportata.

4. Fare clic su **Crea** per eseguire il provisioning del servizio.

Controllare le notifiche nell'indice. Quando il servizio è pronto per l'uso, viene visualizzato un avviso.

<a id="sub-2"></a>
## Individuare il nome del servizio e la chiave API del servizio Ricerca di Azure

Dopo aver creato il servizio, tornare al portale per ottenere l'URL o `api-key`. Per le connessioni al servizio Ricerca è necessario disporre sia dell'URL che di una `api-key` per l'autenticazione della chiamata.

1. Nell’indice, fare clic su **Home**, quindi fare clic sul servizio Ricerca per aprire il dashboard del servizio.

2. Nel dashboard del servizio, saranno riportate sezioni per informazioni essenziali, nonché l'icona della chiave per l'accesso alle chiavi di amministrazione.

  	![][3]

3. Copiare l'URL del servizio, una chiave di amministrazione e una chiave di query. Saranno necessari in seguito, quando verranno aggiunti al file config.js.

## Scaricare i file di esempio

Utilizzare uno degli approcci seguenti per scaricare l'esempio.

1. Andare a [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).
2. Fare clic su **Download ZIP**, salvare il file con estensione zip su disco e quindi estrarre tutti i file in esso contenuti.

Tutte le successive modifiche e le istruzioni di esecuzione verranno effettuate sui file in questa cartella.

In alternativa, se si dispone di GIT nell’istruzione del percorso, è possibile aprire una finestra di PowerShell e digitare `git clone https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git`

## Aggiornare config.js. con l'URL del servizio di ricerca e la chiave api

Utilizzando l'URL e la chiave API copiati in precedenza, specificare l'URL, la chiave di amministrazione e la chiave di query nel file di configurazione.

Le chiavi di amministrazione forniscono il controllo completo sulle operazioni del servizio, incluse creazione ed eliminazione di un indice e caricamento di documenti. Le chiavi di query, invece, sono per le operazioni di sola lettura, in genere utilizzate dalle applicazioni client che si connettono a Ricerca di Azure.

In questo esempio, è necessario includere la chiave di query per rafforzare la procedura consigliata di utilizzo della chiave di query nelle applicazioni client.

La seguente schermata mostra **config.js** aperto in un editor di testo con le voci pertinenti delimitate in modo che sia possibile sapere dove aggiornare il file con i valori validi per il servizio di ricerca.

![][5]


## Ospitare un ambiente di runtime per l'esempio

L'esempio richiede un server HTTP, che è possibile installare a livello globale tramite npm.

Eseguire i comandi seguenti da una finestra di PowerShell:

1. Passare alla cartella che contiene il file **package.json**.
2. Digitare `npm install`.
2. Digitare `npm install -g http-server`.

## Compilare l’indice ed eseguire l'applicazione.

1. Digitare `npm run indexDocuments`.
2. Digitare `npm run build`.
3. Digitare `npm run start_server`.
4. Inserire nel browser l'indirizzo `http://localhost:8080/index.html`

## Eseguire ricerche sui dati dei servizi geologici degli Stati Uniti

Il set di dati dei servizi geologici degli Stati Uniti include i dati relativi allo stato del Rhode Island. Se si fa clic su **Ricerca** su una casella di ricerca vuota, si otterranno le prime 50 voci, ossia l'impostazione predefinita.

L’immissione di un termine di ricerca fornirà al motore di ricerca un elemento con cui continuare. Provare a immettere un nome locale. "Roger Williams" è stato il primo governatore del Rhode Island. Numerosi parchi, edifici e scuole prendono il suo nome.

![][9]

È inoltre possibile tentare con uno dei termini seguenti:

- Pawtucket
- Pembroke
- goose +cape


## Passaggi successivi

Questa è la prima esercitazione di Ricerca di Azure basata su NodeJS e sul set di dati dei servizi geologici degli Stati Uniti. Nel corso del tempo, l’esercitazione sarà ampliata per illustrare le funzionalità di ricerca aggiuntive che potrebbero essere utili nelle soluzioni personalizzate.

Se si dispone già delle nozioni di base di Ricerca di Azure, è possibile usare questo esempio come base di prova per i suggerimenti di alternative (query di suggerimento per la digitazione e completamento automatico), filtri ed esplorazione basata su facet. È inoltre possibile migliorare la pagina dei risultati della ricerca aggiungendo conteggi e raggruppando i documenti in modo che gli utenti possano sfogliare i risultati.

Novità in Ricerca di Azure È consigliabile provare altre esercitazioni per acquisire consapevolezza di ciò che è possibile creare. Visitare la [pagina della documentazione](http://azure.microsoft.com/documentation/services/search/) per trovare ulteriori risorse. È inoltre possibile visualizzare i collegamenti nell'[elenco di video ed esercitazioni](search-video-demo-tutorial-list.md) per accedere a ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png

<!---HONumber=Nov15_HO3-->