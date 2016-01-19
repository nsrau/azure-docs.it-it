<properties
	pageTitle="Importare dati in Ricerca di Azure tramite il portale | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Come caricare dati in un indice di Ricerca di Azure tramite il portale"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importare dati in Ricerca di Azure tramite il portale
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Il dashboard di Ricerca di Azure nel portale di Azure include un comando **Importa dati** che facilita l'inserimento di dati in Ricerca di Azure. Il comando si basa sulla funzionalità predefinita Indicizzatori che effettua una ricerca per indicizzazione in un'origine dati esistente, creando e caricando documenti in base al set di righe trovato nell'origine dati.

Con la procedura guidata l'importazione dei dati si compone di 3 parti:

- Connessione all'origine dati
- Indice di destinazione in cui vengono caricati i dati (spesso la procedura può generarlo automaticamente)
- Pianificazione eseguita immediatamente o a intervalli regolari

Per usare un indicizzatore o il comando **Importa dati**, l'origine dati primaria deve essere una di quelle supportate: database SQL di Azure, database relazionali di SQL Server in una VM di Azure o Azure DocumentDB.

È possibile eseguire l'importazione solo da una singola tabella, vista o struttura dei dati equivalente. Potrebbe essere necessario creare prima questa struttura dei dati nell'origine dati dell'applicazione per ottenere i metadati e gli input dei dati corretti nell'indice di ricerca.

##Configurare l'importazione dei dati

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire il dashboard relativo al servizio Ricerca di Azure. Di seguito alcuni modi per trovare il dashboard.
	- Nell'indice, fare clic su **Home page**. La home page dispone di sezioni per ogni servizio incluso nella sottoscrizione. Fare clic sull'icona per aprire il dashboard del servizio.
	- Nell'indice fare clic su **Esplora tutto** > **Filtra per** > **Servizi di ricerca** per trovare il servizio di ricerca nell'elenco.

3. Nel dashboard del servizio verrà visualizzata una barra dei comandi nella parte superiore che include il comando **Importa dati**. Fare clic su **Importa dati** per aprire il pannello corrispondente.

4. Fare clic su **Definisci la connessione ai dati** per specificare una definizione dell'origine dati usata da un indicizzatore. Le opzioni includono:
	- 	L'origine dati esistente si riferisce a una definizione dell'origine dati creata in precedenza per un indicizzatore. Se nel servizio di ricerca sono già definiti indicizzatori, è possibile modificare una definizione dell'origine dati per un'altra importazione.
	- 	Azure SQL viene usato per specificare una connessione origine dati a un database SQL in Azure o un database SQL Server in una VM di Azure. 
	- 	DocumentDB viene usato per specificare una connessione all'origine dati per quel tipo di origine dati. 

   Per Azure SQL e DocumentDB il database deve essere disponibile all'interno della sottoscrizione. È possibile incollare una stringa di connessione, se la si conosce, o scegliere un'origine dati creata in precedenza da un utente con privilegi di scrittura per la sottoscrizione.

5. Fare clic su **Personalizza indice di destinazione** per completare l'indice predefinito.
	- Il campo **Chiave** è obbligatorio. Il campo selezionato per la chiave deve essere un campo di tipo stringa che contiene valori univoci.
	- Il nome del campo e il tipo sono in genere compilati automaticamente. È possibile modificare il tipo di dati.
	- Selezionare gli attributi per ogni campo:
		- Recuperabile restituisce il campo nei risultati della ricerca.
		- Filtrabile consente di fare riferimento al campo nelle espressioni di filtro.
		- Ordinabile consente di usare il campo in un ordinamento.
		- Con facet abilita il campo per l'esplorazione in base a facet.
		- Ricercabile abilita la ricerca full-text.
	- Fare clic sulla scheda **Analizzatore** se si vuole specificare un analizzatore del linguaggio a livello di campo. Per informazioni dettagliate, vedere [Creare un indice per i documenti in più lingue](search-language-support.md).
	- Scegliere lo **Strumento suggerimenti** per abilitare i suggerimenti per le query con completamento automatico.

6. Fare clic su **Importare i dati** per eseguire l'operazione di importazione con l'opzione Esegui adesso o configurare una pianificazione ricorrente.

L'operazione di importazione dei dati appena completata ha creato un indicizzatore in background. Ora è possibile modificare direttamente l'indicizzatore per cambiare le singole parti che lo compongono.
	
##Modificare un indicizzatore esistente

Nel dashboard del servizio fare doppio clic sul riquadro Indicizzatore per visualizzare un elenco di tutti gli indicizzatori creati per la sottoscrizione. Fare doppio clic su uno degli indicizzatori per eseguirlo, modificarlo o eliminarlo.

<!---HONumber=AcomDC_0114_2016-->