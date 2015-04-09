<properties 
	pageTitle="Visualizzare, modificare, creare e caricare documenti JSON tramite Esplora documenti di DocumentDB | Azure" 
	description="Informazioni su Esplora documenti di DocumentDB, uno strumento del portale di anteprima di Azure per visualizzare, modificare, creare e caricare documenti JSON con DocumentDB." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/13/2015" 
	ms.author="stbaro"/>

# Visualizzare, modificare, creare e caricare documenti JSON tramite Esplora documenti di DocumentDB #

Questo articolo fornisce una panoramica di Esplora documenti di [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), uno strumento del portale di anteprima di Azure che consente di visualizzare, modificare, creare e caricare documenti JSON con DocumentDB. 

Dopo aver completato questa esercitazione, si potrà rispondere alle domande seguenti:  

-	In che modo è possibile creare, visualizzare, modificare ed eliminare facilmente singoli documenti di DocumentDB tramite un Web browser?
-	In che modo è possibile visualizzare facilmente le proprietà di sistema di un documento di DocumentDB tramite un Web browser?
-	In che modo è possibile eseguire facilmente l'inserimento in blocco di documenti in DocumentDB tramite un Web browser?

##<a id="Launch"></a>Avviare ed esplorare Esplora documenti##

Esplora documenti può essere avviato da qualsiasi account, database e pannelli di raccolte di DocumentDB.  

1. Nella parte inferiore di ogni pannello è presente una sezione **Strumenti di sviluppo** che contiene la parte **Esplora documenti**.

	![Screenshot of the Document Explorer part](./media/documentdb-view-JSON-document-explorer/documentexplorerpart.png) 

2. Fare semplicemente clic sul riquadro per avviare Esplora documenti.

	<p>Gli elenchi a discesa**Database** e **Raccolta** vengono precompilati a seconda del contesto in cui si avvia Esplora documenti.  Ad esempio, se viene avviato da un pannello di database, viene prepopolato il database corrente.  Se viene avviato da un pannello della raccolta, viene prepopolata la raccolta corrente.

	![Screenshot of Document Explorer](./media/documentdb-view-JSON-document-explorer/documentexplorerinitial.png)

3. Esplora documenti carica i primi 100 documenti nella raccolta selezionata.  È possibile caricare altri documenti (in batch di 100) selezionando l'opzione **Carica altro** nella parte inferiore del pannello di Esplora documenti.  

4. Gli elenchi a discesa **Database** e **Raccolta** possono essere usati per modificare facilmente la raccolta da cui sono attualmente visualizzati, senza dover chiudere e riavviare Esplora documenti.  

5. Esplora documenti supporta anche l'applicazione di filtri al set di documenti attualmente caricato in base alla relativa proprietà ID.  È sufficiente digitare nella casella del filtro.

	![Screenshot of Document Explorer with filter highlighted](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png) 

	I risultati nell'elenco di Esplora documenti vengono filtrati in base ai criteri forniti.

	![Screenshot of Document Explorer with filtered results](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)


	> [AZURE.IMPORTANT] La funzionalità di filtro di Esplora documenti consente di filtrare solo dal set di documenti caricati ***attualmente*** e non esegue una query sulla raccolta attualmente selezionata.

6. Per aggiornare l'elenco dei documenti caricati da Esplora documenti, fare semplicemente clic sul comando **Aggiorna** nella parte superiore del pannello.

	![Screenshot of Document Explorer refresh command](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)


##<a id="Create"></a>Visualizzare, creare e modificare i documenti con Esplora documenti##

Esplora documenti consente di creare, modificare ed eliminare facilmente i documenti.  

- Per creare un documento, fare semplicemente clic sul comando **Crea documento**. Verrà fornito un frammento di codice JSON minimo.

	![Screenshot of Document Explorer create document experience](./media/documentdb-view-JSON-document-explorer/createdocument.png) 

- Digitare o incollare semplicemente il contenuto JSON del documento che si desidera creare e quindi fare clic sul comando **Salva** per eseguire il commit del documento.

	![Screenshot of Document Explorer save command](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

	> [AZURE.NOTE] Se non si specifica una proprietà "ID", Esplora documenti aggiunge automaticamente una proprietà ID e genera un GUID come valore ID. 

- Per modificare un documento esistente, è sufficiente selezionarlo in Esplora documenti, modificarlo nel modo che si preferisce, quindi fare clic sul comando **Salva**.

	![Screenshot of Document Explorer edit document functionality](./media/documentdb-view-JSON-document-explorer/editdocument.png)

- Se si modifica un documento e si decide di eliminare il set di modifiche corrente, fare semplicemente clic sul comando Annulla e confermare l'azione. Verrà ricaricato lo stato precedente del documento.

	![Screenshot of Document Explorer discard command](./media/documentdb-view-JSON-document-explorer/discardedit.png)

- Per eliminare un documento, selezionarlo, fare clic sul comando **Elimina** e quindi confermare l'eliminazione. Dopo la conferma, il documento viene immediatamente rimosso dall'elenco di Esplora documenti:

	![Screenshot of Document Explorer delete command](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

- Si noti che Esplora documenti verifica che un documento nuovo o modificato includa contenuto JSON valido.  È anche possibile passare il puntatore sopra la sezione non corretta per ottenere informazioni dettagliate sull'errore di convalida.

	![Screenshot of Document Explorer invalid JSON highlighting](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

- Esplora documenti impedisce anche di salvare un documento con contenuto JSON non valido.

	![Screenshot of Document Explorer invalid JSON save error](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

- Infine, Esplora documenti consente di visualizzare facilmente le proprietà di sistema del documento attualmente caricato facendo clic sul comando **Proprietà**.

	![Screenshot of Document Explorer document properties view](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

	> [AZURE.NOTE] La proprietà timestamp (_ts) viene rappresentata internamente come periodo, ma Esplora documenti consente di visualizzare il valore in un formato GMT leggibile.

##<a id="BulkAdd"></a>Aggiungere documenti in blocco con Esplora documenti##

Esplora documenti supporta l'inserimento in blocco di uno o più documenti JSON esistenti.  

1. Per avviare il processo di caricamento, fare clic sul comando **Aggiungi documento**.
	
	![Screenshot of Document Explorer bulk ingestion functionality](./media/documentdb-view-JSON-document-explorer/adddocument1.png)

2. Viene visualizzato un nuovo pannello.  Fare clic sul pulsante Sfoglia per aprire una finestra di Esplora file e selezionare uno o più documenti JSON da caricare.

	![Screenshot of Document Explorer bulk ingestion process](./media/documentdb-view-JSON-document-explorer/adddocument2.png)

	> [AZURE.NOTE] Esplora documenti supporta attualmente fino a 100 documenti JSON per singola operazione di caricamento.

3. Dopo aver completato la selezione, fare clic sul pulsante **Carica**.  I documenti vengono aggiunti automaticamente alla griglia di Esplora documenti e i risultati del caricamento vengono visualizzati con l'avanzare dell'operazione. Gli errori di importazione vengono segnalati per i singoli file.

	![Screenshot of Document Explorer bulk ingestion results](./media/documentdb-view-JSON-document-explorer/adddocument3.png)

4. Dopo aver completato l'operazione, è possibile selezionare un massimo di altri 100 documenti da caricare.

##<a name="NextSteps"></a>Passaggi successivi

Per altre informazioni su DocumentDB, vedere [qui](http://azure.com/docdb).

<!--HONumber=49-->