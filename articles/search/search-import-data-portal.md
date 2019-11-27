---
title: Importare dati in un indice di ricerca usando portale di Azure
titleSuffix: Azure Cognitive Search
description: Informazioni su come usare la procedura guidata Importa dati nel portale di Azure per inserire i dati di Azure da Cosmos DB, archiviazione BLOB, archiviazione tabelle, database SQL e SQL Server in macchine virtuali di Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: df7d0fde05c974ef4cec739236a3ac0aebd63ecc
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534545"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Importazione guidata dati per Azure ricerca cognitiva

Il portale di Azure fornisce una procedura guidata **Importa dati** nel dashboard di Azure ricerca cognitiva per la creazione di prototipi e il caricamento di un indice. In questo articolo vengono illustrati i vantaggi e le limitazioni dell'utilizzo della procedura guidata, degli input e degli output e alcune informazioni sull'utilizzo. Per istruzioni pratiche nell'esecuzione della procedura guidata con i dati di esempio incorporati, vedere [creare un indice di Azure ricerca cognitiva usando la](search-get-started-portal.md) Guida introduttiva di portale di Azure.

Le operazioni eseguite dalla procedura guidata includono:

1: connettersi a un'origine dati di Azure supportata.

2-creare uno schema di indice, dedotto dai dati di origine del campionamento.

3-facoltativamente, aggiungere arricchimenti di intelligenza artificiale per estrarre o generare contenuto e struttura.

4-eseguire la procedura guidata per creare oggetti, importare dati, impostare una pianificazione e altre opzioni di configurazione.

La procedura guidata restituisce un numero di oggetti salvati nel servizio di ricerca, a cui è possibile accedere a a livello o altri strumenti.

## <a name="advantages-and-limitations"></a>Vantaggi e limitazioni

Prima di scrivere il codice, è possibile usare la procedura guidata per la creazione di prototipi e il test del modello di prova. La procedura guidata si connette a origini dati esterne, esegue il campionamento dei dati per creare un indice iniziale e quindi importa i dati come documenti JSON in un indice in ricerca cognitiva di Azure. 

Il campionamento è il processo mediante il quale viene dedotto uno schema dell'indice e presenta alcune limitazioni. Quando viene creata l'origine dati, la procedura guidata seleziona un campione di documenti per decidere quali colonne fanno parte dell'origine dati. Non tutti i file vengono letti, in quanto questa operazione potrebbe richiedere ore per origini dati di dimensioni molto grandi. Data la selezione di documenti, i metadati di origine, ad esempio il nome o il tipo di campo, vengono usati per creare una raccolta di campi in uno schema di indice. A seconda della complessità dei dati di origine, potrebbe essere necessario modificare lo schema iniziale per l'accuratezza oppure estenderlo per completezza. È possibile apportare le modifiche inline nella pagina definizione indice.

Complessivamente, i vantaggi derivanti dall'utilizzo della procedura guidata sono chiari: purché siano soddisfatti i requisiti, è possibile prototipare un indice Queryable entro pochi minuti. Alcune delle complessità dell'indicizzazione, ad esempio la fornitura di dati come documenti JSON, vengono gestite dalla procedura guidata.

Le limitazioni note sono riepilogate come segue:

+ La procedura guidata non supporta l'iterazione o il riutilizzo. Ogni passaggio della procedura guidata consente di creare una nuova configurazione di indice, competenze e indicizzatore. Solo le origini dati possono essere rese permanente e riutilizzate nella procedura guidata. Per modificare o perfezionare altri oggetti, è necessario usare le API REST o .NET SDK per recuperare e modificare le strutture.

+ Il contenuto di origine deve trovarsi in un'origine dati di Azure supportata.

+ Il campionamento è su un subset di dati di origine. Per le origini dati di grandi dimensioni, la procedura guidata può perdere i campi. Se il campionamento non è sufficiente, potrebbe essere necessario estendere lo schema o correggere i tipi di dati dedotti.

+ L'arricchimento di intelligenza artificiale, esposto nel portale, è limitato ad alcune competenze predefinite. 

+ Un [Archivio informazioni](knowledge-store-concept-intro.md), che può essere creato dalla procedura guidata, è limitato ad alcune proiezioni predefinite. Se si desidera salvare i documenti arricchiti creati dalla procedura guidata, il contenitore BLOB e le tabelle dispongono di nomi e strutture predefiniti.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Input origine dati

La procedura guidata **Importa dati** si connette a un'origine dati esterna usando la logica interna fornita dagli indicizzatori ricerca cognitiva di Azure, che sono disponibili per campionare l'origine, leggere i metadati, craccare i documenti per leggere il contenuto e la struttura e serializzare il contenuto come JSON per l'importazione successiva in Azure ricerca cognitiva.

È possibile importare solo da una singola tabella, da una vista di database o da una struttura di dati equivalente, tuttavia la struttura può includere sottostrutture gerarchiche o nidificate. Per ulteriori informazioni, vedere [come modellare tipi complessi](search-howto-complex-data-types.md).

È necessario creare questa singola tabella o vista prima di eseguire la procedura guidata e deve contenere contenuto. Per ovvie ragioni, non è consigliabile eseguire la procedura guidata di **importazione dei dati** in un'origine dati vuota.

|  Selezione | Description |
| ---------- | ----------- |
| **Origine dati esistente** |Se nel servizio di ricerca sono già definiti indicizzatori, potrebbe essere presente una definizione dell'origine dati che è possibile riutilizzare. In Azure ricerca cognitiva gli oggetti origine dati vengono utilizzati solo dagli indicizzatori. È possibile creare un oggetto origine dati a livello di codice o tramite la procedura guidata **Importa dati** e riutilizzarli in base alle esigenze.|
| **Esempi**| Azure ricerca cognitiva offre due origini dati di esempio predefinite che vengono usate nelle esercitazioni e nelle guide introduttive: un database SQL immobiliare e un database di hotel ospitato su Cosmos DB. Per una procedura dettagliata basata sull'esempio di Hotel, vedere l'articolo relativo alla [creazione di un indice nella portale di Azure](search-get-started-portal.md) Guida introduttiva. |
| [**Database SQL di Azure**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Il nome del servizio, le credenziali per un utente di database con autorizzazione di lettura e un nome di database possono essere specificati nella pagina o tramite una stringa di connessione ADO.NET. Per visualizzare o personalizzare le proprietà, scegliere l'opzione relativa alla stringa di connessione. <br/><br/>È necessario specificare nella pagina la tabella o la vista che fornisce il set di righe. Questa opzione viene visualizzata quando la connessione ha esito positivo, con un elenco a discesa che permette di effettuare una selezione.|
| **Macchine virtuali SQL Server in Azure** |Specificare un nome completo del servizio, un ID utente, una password e un database come stringa di connessione. Per usare questa origine dati, è necessario avere già installato un certificato nell'archivio locale che esegue la crittografia della connessione. Per istruzioni, vedere [connessione di una macchina virtuale SQL ad Azure ricerca cognitiva](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>È necessario specificare nella pagina la tabella o la vista che fornisce il set di righe. Questa opzione viene visualizzata quando la connessione ha esito positivo, con un elenco a discesa che permette di effettuare una selezione. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Tra i requisiti: l'account, il database e la raccolta. Tutti i documenti nella raccolta verranno inclusi nell'indice. È possibile definire una query per rendere flat o filtrare il set di righe oppure lasciare vuota la query. Una query non è obbligatoria in questa procedura guidata.|
| [**Archiviazione BLOB di Azure**](search-howto-indexing-azure-blob-storage.md) |Tra i requisiti: l'account di archiviazione e un contenitore. Facoltativamente, se i nomi dei BLOB seguono una convenzione di denominazione virtuale a scopo di raggruppamento, è possibile specificare la porzione directory virtuale del nome come una cartella nel contenitore. Per altre informazioni, vedere [Indicizzazione di documenti nell'archivio BLOB di Azure con Ricerca di Azure](search-howto-indexing-azure-blob-storage.md). |
| [**Archiviazione tabelle di Azure**](search-howto-indexing-azure-tables.md) |Tra i requisiti: l'account di archiviazione e un nome di tabella. Facoltativamente, è possibile specificare una query per recuperare un subset delle tabelle. Per altre informazioni, vedere [Indicizzazione nell'archivio tabelle di Azure con Ricerca di Azure](search-howto-indexing-azure-tables.md). |

## <a name="wizard-output"></a>Output della procedura guidata

Dietro le quinte, la procedura guidata consente di creare, configurare e richiamare gli oggetti seguenti. Dopo l'esecuzione della procedura guidata, è possibile trovarne l'output nelle pagine del portale. La pagina Panoramica del servizio include elenchi di indici, indicizzatori, origini dati e skillsets. Le definizioni degli indici possono essere visualizzate in formato JSON completo nel portale. Per altre definizioni, è possibile usare l' [API REST](https://docs.microsoft.com/rest/api/searchservice/) per ottenere oggetti specifici.

| Oggetto | Description | 
|--------|-------------|
| [Origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Rende permanente le informazioni di connessione ai dati di origine, incluse le credenziali. Un oggetto origine dati viene utilizzato esclusivamente con gli indicizzatori. | 
| [Index](https://docs.microsoft.com/rest/api/searchservice/create-index) | Struttura di dati fisici utilizzata per la ricerca full-text e altre query. | 
| [Competenze](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | Set completo di istruzioni per la manipolazione, la trasformazione e il data shaping del contenuto, inclusa l'analisi e l'estrazione di informazioni dai file di immagine. Fatta eccezione per le strutture molto semplici e limitate, include un riferimento a una risorsa Servizi cognitivi che fornisce l'arricchimento. Facoltativamente, potrebbe inoltre contenere una definizione di archivio informazioni.  | 
| [Indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Oggetto di configurazione che specifica un'origine dati, un indice di destinazione, un valore facoltativo di competenze, una pianificazione facoltativa e impostazioni di configurazione facoltative per la gestione degli errori e la codifica base-64. |


## <a name="how-to-start-the-wizard"></a>Come avviare la procedura guidata

La procedura guidata Importa dati viene avviata dalla barra dei comandi della pagina Panoramica del servizio.

1. Nel [portale di Azure](https://portal.azure.com) aprire la pagina per la ricerca dei servizi dal dashboard o [trovare il servizio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nell'elenco.

2. Nella pagina di panoramica del servizio nella parte superiore fare clic su **Importa dati**.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

È anche possibile avviare l' **importazione dei dati** da altri servizi di Azure, tra cui Azure Cosmos DB, il database SQL di Azure e l'archiviazione BLOB di Azure. Cercare **aggiungi ricerca cognitiva di Azure** nel riquadro di spostamento a sinistra della pagina Panoramica del servizio.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Come modificare o terminare uno schema di indice nella procedura guidata

La procedura guidata genera un indice incompleto, che verrà popolato con i documenti ottenuti dall'origine dati di input. Per un indice funzionale, assicurarsi di aver definito gli elementi seguenti.

1. L'elenco dei campi è completo? Aggiungere nuovi campi che campionano mancanti e rimuovere quelli che non aggiungono valore a un'esperienza di ricerca o che non verranno usati in un' [espressione di filtro](search-query-odata-filter.md) o un [profilo di Punteggio](index-add-scoring-profiles.md).

1. Il tipo di dati è appropriato per i dati in ingresso? Azure ricerca cognitiva supporta i [tipi di dati EDM (Entity Data Model)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types). Per i dati SQL di Azure è presente un [grafico di mapping](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) che definisce valori equivalenti. Per ulteriori informazioni, vedere [mapping dei campi e trasformazioni](search-indexer-field-mappings.md).

1. Si dispone di un campo che può fungere da *chiave*? Questo campo deve essere Edm. String e deve identificare in modo univoco un documento. Per i dati relazionali, è possibile eseguirne il mapping a una chiave primaria. Per i BLOB, potrebbe essere il `metadata-storage-path`. Se i valori dei campi includono spazi o trattini, è necessario impostare l'opzione **Chiavi di codifica Base 64** nel passaggio **Crea un indicizzatore**, in **Opzioni avanzate**, per evitare il controllo di convalida per questi caratteri.

1. Impostare gli attributi per determinare il modo in cui tale campo viene utilizzato in un indice. 

   Eseguire questo passaggio perché gli attributi determinano l'espressione fisica dei campi nell'indice. Se si desidera modificare gli attributi in un secondo momento, anche a livello di codice, è quasi sempre necessario eliminare e ricompilare l'indice. Gli attributi di base, ad esempio **ricercabili** e **recuperabili** , hanno un [effetto trascurabile sull'archiviazione](search-what-is-an-index.md#storage-implications). L'abilitazione dei filtri e l'uso dei suggerimenti per aumentare i requisiti di archiviazione. 
   
   + **Ricercabile** abilita la ricerca full-text. Ogni campo usato nelle query in formato libero o nelle espressioni di query deve contenere questo attributo. Per ogni campo contrassegnato come **Ricercabile** vengono creati indici invertiti.

   + **Recuperabile** restituisce il campo nei risultati della ricerca. Ogni campo che fornisce contenuto ai risultati della ricerca deve avere questo attributo. L'impostazione di questo campo non influisce significativamente sulle dimensioni dell'indice.

   + **Filtrabile** consente di fare riferimento al campo nelle espressioni di filtro. Ogni campo usato in un'espressione **$filter** deve avere questo attributo. Le espressioni di filtro sono per le corrispondenze esatte. Poiché le stringhe di testo rimangono inalterate, è necessario ulteriore spazio di archiviazione per ospitare il contenuto verbatim.

   + **Con facet** abilita il campo per l'esplorazione in base a facet. Solo i campi contrassegnati anche come **Filtrabile** possono essere contrassegnati come **Con facet**.

   + **Ordinabile** consente di usare il campo in un ordinamento. Ogni campo usato in un'espressione **$Orderby** deve avere questo attributo.

1. Sono necessarie [analisi lessicali](search-lucene-query-architecture.md#stage-2-lexical-analysis)? Per i campi EDM. String disponibili per la **ricerca**, è possibile impostare un **analizzatore** per l'indicizzazione e l'esecuzione di query ottimizzate per la lingua. 

   Il valore predefinito è *Standard - Lucene* ma è possibile scegliere *Inglese - Microsoft* se si vuole usare l'analizzatore Microsoft per l'elaborazione lessicale avanzata, ad esempio per la risoluzione di forme verbali o nominali irregolari. Solo gli analizzatori di lingua possono essere specificati nel portale. L'uso di un analizzatore personalizzato o di un analizzatore non di linguaggio come la parola chiave, il modello e così via, deve essere eseguito a livello di codice. Per ulteriori informazioni sugli analizzatori, vedere [aggiungere analizzatori di linguaggi](search-language-support.md).

1. Sono necessarie funzionalità di typeahead sotto forma di completamento automatico o di risultati consigliati? Selezionare la casella di controllo **Suggerisci** per abilitare i suggerimenti per le [query typeahead e il completamento automatico](index-add-suggesters.md) nei campi selezionati. I suggeritori aggiungono al numero di termini in formato token nell'indice e quindi utilizzano più spazio di archiviazione.


## <a name="next-steps"></a>Passaggi successivi

Il modo migliore per comprendere i vantaggi e le limitazioni della procedura guidata è eseguire un'istruzione alla volta. La Guida introduttiva seguente illustra ogni passaggio.

> [!div class="nextstepaction"]
> [Creare un indice di Azure ricerca cognitiva usando il portale di Azure](search-get-started-portal.md)