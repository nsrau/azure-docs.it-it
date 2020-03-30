---
title: Importare dati in un indice di ricerca tramite il portale di AzureImport data into a search index using Azure portal
titleSuffix: Azure Cognitive Search
description: Informazioni su come usare la procedura guidata Importa dati nel portale di Azure per inserire i dati di Azure da Cosmos DB, archiviazione BLOB, archiviazione tabelle, database SQL e SQL Server in macchine virtuali di Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460693"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Procedura guidata Importare dati per Ricerca cognitiva di AzureImport data wizard for Azure Cognitive Search

Il portale di Azure offre una procedura guidata **Importa dati** nel dashboard di Ricerca cognitiva di Azure per la creazione di prototipi e il caricamento di un indice. In questo articolo vengono illustrati i vantaggi e le limitazioni dell'utilizzo della procedura guidata, gli input e gli output e alcune informazioni sull'utilizzo. Per indicazioni pratiche su come eseguire la procedura guidata usando dati di esempio incorporati, vedere Creare un indice di [Ricerca cognitiva](search-get-started-portal.md) di Azure usando la guida introduttiva del portale di Azure.For hands-on guidance in stepping through the wizard using built-in sample data, see the Create an Azure Cognitive Search index using the Azure portal quickstart.

Le operazioni eseguite da questa procedura guidata includono:Operations that this wizard performs include:

1 - Connettersi a un'origine dati di Azure supportata.1 - Connect to a supported Azure data source.

2 - Creare uno schema di indice, dedotto dai dati di origine di campionamento.

3 - Facoltativamente, aggiungere gli arricchimenti di IA per estrarre o generare contenuto e struttura.

4 - Eseguire la procedura guidata per creare oggetti, importare dati, impostare una pianificazione e altre opzioni di configurazione.

La procedura guidata restituisce una serie di oggetti che vengono salvati nel servizio di ricerca, a cui è possibile accedere a livello di programmazione o in altri strumenti.

## <a name="advantages-and-limitations"></a>Vantaggi e limitazioni

Prima di scrivere qualsiasi codice, è possibile utilizzare la procedura guidata per la creazione di prototipi e test proof-of-concept. The wizard connects to external data sources, samples the data to create an initial index, and then imports the data as JSON documents into an index on Azure Cognitive Search. 

Il campionamento è il processo mediante il quale viene dedotto uno schema di indice e presenta alcune limitazioni. Quando viene creata l'origine dati, la procedura guidata seleziona un campione di documenti per decidere quali colonne fanno parte dell'origine dati. Non tutti i file vengono letti, in quanto potrebbero essere necessarie ore per origini dati di grandi dimensioni. Data una selezione di documenti, i metadati di origine, ad esempio il nome o il tipo di campo, vengono utilizzati per creare una raccolta di campi in uno schema di indice. A seconda della complessità dei dati di origine, potrebbe essere necessario modificare lo schema iniziale per verificarne l'accuratezza o estenderlo per completezza. È possibile apportare le modifiche inline nella pagina di definizione dell'indice.

Nel complesso, i vantaggi dell'utilizzo della procedura guidata sono chiari: finché i requisiti sono soddisfatti, è possibile creare un prototipo di un indice interrogabile in pochi minuti. Alcune delle complessità dell'indicizzazione, ad esempio la fornitura di dati come documenti JSON, vengono gestite dalla procedura guidata.

Le limitazioni note sono riepilogate come segue:

+ La procedura guidata non supporta l'iterazione o il riutilizzo. Ogni passaggio attraverso la procedura guidata crea un nuovo indice, un set di competenze e la configurazione dell'indicizzatore. Solo le origini dati possono essere rese persistenti e riutilizzate all'interno della procedura guidata. Per modificare o perfezionare altri oggetti, è necessario usare le API REST o .NET SDK per recuperare e modificare le strutture.

+ Il contenuto di origine deve trovarsi in un'origine dati di Azure supportata.

+ Il campionamento è su un sottoinsieme di dati di origine. Per le origini dati di grandi dimensioni, è possibile che la procedura guidata per perdere i campi. Potrebbe essere necessario estendere lo schema o correggere i tipi di dati dedotti, se il campionamento non è sufficiente.

+ L'arricchimento dell'IA, così come esposto nel portale, è limitato a poche competenze incorporate. 

+ Un [archivio informazioni,](knowledge-store-concept-intro.md)che può essere creato dalla procedura guidata, è limitato ad alcune proiezioni predefinite. Se si desidera salvare i documenti arricchiti creati dalla procedura guidata, il contenitore BLOB e le tabelle vengono forniti con i nomi e la struttura predefiniti.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Input origine dati

**L'Importazione** guidata dati si connette a un'origine dati esterna usando la logica interna fornita dagli indicizzatori di Ricerca cognitiva di Azure, dotati di campionare l'origine, leggere i metadati, decifrare i documenti per leggere il contenuto e la struttura e serializzare il contenuto come JSON per la successiva importazione in Ricerca cognitiva di Azure.The Import data wizard connects to an external data source using the internal logic provided by Azure Cognitive Search indexers, which are equipped to sample the source, read metadata, crack documents to read content and structure, and serialize content as JSON for subsequent import to Azure Cognitive Search.

È possibile importare solo da una singola tabella, vista di database o struttura di dati equivalente, tuttavia la struttura può includere sottostrutture gerarchiche o nidificate. Per ulteriori informazioni, vedere [Come modellare tipi complessi](search-howto-complex-data-types.md).

È necessario creare questa singola tabella o vista prima di eseguire la procedura guidata e deve includere contenuto. Per ovvie cause, non ha senso eseguire **l'Importazione** guidata dati su un'origine dati vuota.

|  Selezione | Descrizione |
| ---------- | ----------- |
| **Origine dati esistente** |Se nel servizio di ricerca sono già definiti indicizzatori, è possibile che sia presente una definizione di origine dati esistente che è possibile riutilizzare. In Ricerca cognitiva di Azure gli oggetti origine dati vengono usati solo dagli indicizzatori. È possibile creare un oggetto origine dati a livello di codice o tramite **l'Importazione** guidata dati e riutilizzarli in base alle esigenze.|
| **Esempi**| Ricerca cognitiva di Azure offre due origini dati di esempio predefinite usate nelle esercitazioni e nelle guide rapide: un database SQL immobiliare e un database Hotels ospitato in Cosmos DB. Per una procedura utente in base all'esempio Hotels, vedere la guida introduttiva Creare un indice nel portale di [Azure.For](search-get-started-portal.md) a walk through based on the Hotels sample, see the Create an index in the Azure portal quickstart. |
| [**database SQL di Azure**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Il nome del servizio, le credenziali per un utente di database con autorizzazione di lettura e un nome di database possono essere specificati nella pagina o tramite una stringa di connessione ADO.NET. Per visualizzare o personalizzare le proprietà, scegliere l'opzione relativa alla stringa di connessione. <br/><br/>È necessario specificare nella pagina la tabella o la vista che fornisce il set di righe. Questa opzione viene visualizzata quando la connessione ha esito positivo, con un elenco a discesa che permette di effettuare una selezione.|
| **SQL Server on Azure VM** |Specificare un nome completo del servizio, un ID utente, una password e un database come stringa di connessione. Per usare questa origine dati, è necessario avere già installato un certificato nell'archivio locale che esegue la crittografia della connessione. Per istruzioni, vedere [Connessione di VM SQL a Ricerca cognitiva di Azure.For instructions,](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)see SQL VM connection to Azure Cognitive Search . <br/><br/>È necessario specificare nella pagina la tabella o la vista che fornisce il set di righe. Questa opzione viene visualizzata quando la connessione ha esito positivo, con un elenco a discesa che permette di effettuare una selezione. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Tra i requisiti: l'account, il database e la raccolta. Tutti i documenti nella raccolta verranno inclusi nell'indice. È possibile definire una query per appiattire o filtrare il set di righe oppure lasciare vuota la query. Non è necessaria una query in questa procedura guidata.|
| [**Archiviazione BLOB di AzureAzure Blob Storage**](search-howto-indexing-azure-blob-storage.md) |Tra i requisiti: l'account di archiviazione e un contenitore. Facoltativamente, se i nomi dei BLOB seguono una convenzione di denominazione virtuale a scopo di raggruppamento, è possibile specificare la porzione directory virtuale del nome come una cartella nel contenitore. Per altre informazioni, vedere [Indicizzazione di documenti nell'archivio BLOB di Azure con Ricerca di Azure](search-howto-indexing-azure-blob-storage.md). |
| [**Archiviazione tabelle di AzureAzure Table Storage**](search-howto-indexing-azure-tables.md) |Tra i requisiti: l'account di archiviazione e un nome di tabella. Facoltativamente, è possibile specificare una query per recuperare un subset delle tabelle. Per altre informazioni, vedere [Indicizzazione nell'archivio tabelle di Azure con Ricerca di Azure](search-howto-indexing-azure-tables.md). |

## <a name="wizard-output"></a>Uscita procedura guidata

Dietro le quinte, la procedura guidata crea, configura e richiama gli oggetti seguenti. Dopo l'esecuzione della procedura guidata, è possibile trovare il relativo output nelle pagine del portale. La pagina Panoramica del servizio include elenchi di indici, indicizzatori, origini dati e set di competenze. Le definizioni di indice possono essere visualizzate in JSON completo nel portale. Per altre definizioni, è possibile usare [l'API REST](https://docs.microsoft.com/rest/api/searchservice/) per ottenere oggetti specifici.

| Oggetto | Descrizione | 
|--------|-------------|
| [Origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Rende persistenti le informazioni di connessione ai dati di origine, incluse le credenziali. Un oggetto origine dati viene utilizzato esclusivamente con gli indicizzatori. | 
| [Indice](https://docs.microsoft.com/rest/api/searchservice/create-index) | Struttura dati fisica utilizzata per la ricerca full-text e altre query. | 
| [Set di competenze](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | Un set completo di istruzioni per la modifica, la trasformazione e la modellazione del contenuto, inclusa l'analisi e l'estrazione di informazioni dai file di immagine. Ad eccezione di strutture molto semplici e limitate, include un riferimento a una risorsa di Servizi cognitivi che fornisce l'arricchimento. Facoltativamente, potrebbe anche contenere una definizione del archivio informazioni.  | 
| [Indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Oggetto di configurazione che specifica un'origine dati, un indice di destinazione, un set di competenze facoltativo, una pianificazione facoltativa e impostazioni di configurazione facoltative per la gestione degli errori e la codifica in base 64. |


## <a name="how-to-start-the-wizard"></a>Come avviare la procedura guidata

L'Importazione guidata dati viene avviata dalla barra dei comandi nella pagina Panoramica del servizio.

1. Nel [portale di Azure](https://portal.azure.com) aprire la pagina per la ricerca dei servizi dal dashboard o [trovare il servizio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nell'elenco.

2. Nella pagina di panoramica del servizio nella parte superiore fare clic su **Importa dati**.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

È anche possibile avviare Importa dati da altri servizi di Azure, inclusi il database Cosmos di Azure, il database SQL di Azure e l'archiviazione BLOB di Azure.You can also launch **Import data** from other Azure services, including Azure Cosmos DB, Azure SQL Database, and Azure Blob storage. Cercare **Aggiungi Ricerca cognitiva** di Azure nel riquadro di spostamento a sinistra nella pagina di panoramica del servizio.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Come modificare o completare uno schema di indice nella procedura guidata

La procedura guidata genera un indice incompleto, che verrà popolato con i documenti ottenuti dall'origine dati di input. Per un indice funzionale, assicurarsi di aver definito gli elementi seguenti.

1. L'elenco dei campi è completo? Aggiungere nuovi campi persi dal campionamento e rimuovere gli elementi che non aggiungono valore a un'esperienza di ricerca o che non verranno utilizzati in [un'espressione](search-query-odata-filter.md) di filtro o in un profilo di [punteggio.](index-add-scoring-profiles.md)

1. Il tipo di dati è appropriato per i dati in ingresso? Ricerca cognitiva di Azure supporta i [tipi di dati EDM (Entity Data Model).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Per i dati SQL di Azure, è disponibile un grafico di [mapping](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) che definisce valori equivalenti. Per ulteriori informazioni, vedere [Mapping e trasformazioni](search-indexer-field-mappings.md)dei campi .

1. Avete un campo che può servire come *chiave*? Questo campo deve essere Edm.string e deve identificare in modo univoco un documento. Per i dati relazionali, è possibile che venga eseguito il mapping a una chiave primaria. Per i BLOB, potrebbe `metadata-storage-path`essere il file . Se i valori dei campi includono spazi o trattini, è necessario impostare l'opzione **Chiavi di codifica Base 64** nel passaggio **Crea un indicizzatore**, in **Opzioni avanzate**, per evitare il controllo di convalida per questi caratteri.

1. Impostare gli attributi per determinare la modalità di utilizzo di tale campo in un indice. 

   Con questo passaggio, gli attributi determinano l'espressione fisica dei campi nell'indice. Se si desidera modificare gli attributi in un secondo momento, anche a livello di codice, sarà quasi sempre necessario eliminare e ricompilare l'indice. Gli attributi di base come **Searchable** e **Retrievable** hanno un [impatto trascurabile sull'archiviazione.](search-what-is-an-index.md#index-size) L'abilitazione dei filtri e l'utilizzo dei suggerimenti aumentano i requisiti di archiviazione. 
   
   + **Ricercabile** consente la ricerca full-text. Ogni campo usato nelle query in formato libero o nelle espressioni di query deve contenere questo attributo. Per ogni campo contrassegnato come **Ricercabile** vengono creati indici invertiti.

   + **Retrievable** restituisce il campo nei risultati della ricerca. Ogni campo che fornisce contenuto ai risultati della ricerca deve avere questo attributo. L'impostazione di questo campo non influisce significativamente sulle dimensioni dell'indice.

   + **Filtrabile** consente di fare riferimento al campo nelle espressioni di filtro. Ogni campo usato in un'espressione **$filter** deve avere questo attributo. Le espressioni di filtro sono per le corrispondenze esatte. Poiché le stringhe di testo rimangono inalterate, è necessario ulteriore spazio di archiviazione per ospitare il contenuto verbatim.

   + **Facetable** abilita il campo per l'esplorazione in base a facet. Solo i campi contrassegnati anche come **Filtrabile** possono essere contrassegnati come **Con facet**.

   + **Ordinabile** consente di utilizzare il campo in un ordinamento. Ogni campo usato in un'espressione **$Orderby** deve avere questo attributo.

1. Avete bisogno di [analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis)? Per i campi Edm.string che sono **ricercabili**, è possibile impostare un **analizzatore** se si desidera l'indicizzazione e l'esecuzione di query con valori di lingua. 

   Il valore predefinito è *Standard - Lucene* ma è possibile scegliere *Inglese - Microsoft* se si vuole usare l'analizzatore Microsoft per l'elaborazione lessicale avanzata, ad esempio per la risoluzione di forme verbali o nominali irregolari. Solo gli analizzatori del linguaggio possono essere specificati nel portale. L'uso di un analizzatore personalizzato o di un analizzatore non in linguaggio come Keyword, Pattern e così via, deve essere eseguito a livello di codice. Per ulteriori informazioni sugli analizzatori, vedere [Aggiungere analizzatori di linguaggio](search-language-support.md).

1. Hai bisogno della funzionalità typeahead sotto forma di completamento automatico o risultati suggeriti? Selezionare la casella di controllo **Suggerimento** per abilitare i suggerimenti di [query typeahead e](index-add-suggesters.md) il completamento automatico nei campi selezionati. I suggerimenti aggiungono al numero di termini in formato token nell'indice e quindi utilizzano più spazio di archiviazione.


## <a name="next-steps"></a>Passaggi successivi

Il modo migliore per comprendere i vantaggi e le limitazioni della procedura guidata consiste nell'esaminarlo. La guida introduttiva seguente guida l'utente attraverso ogni passaggio.

> [!div class="nextstepaction"]
> [Creare un indice di Ricerca cognitiva di Azure usando il portale di AzureCreate an Azure Cognitive Search index using the Azure portal](search-get-started-portal.md)