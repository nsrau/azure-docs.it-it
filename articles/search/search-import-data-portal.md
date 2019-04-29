---
title: Importare i dati nell'indice di ricerca usando il portale di Azure - Ricerca di Azure
description: Informazioni su come usare la procedura guidata Importa dati nel portale di Azure per inserire i dati di Azure da Cosmos DB, archiviazione BLOB, archiviazione tabelle, database SQL e SQL Server in macchine virtuali di Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: fcb1e4f32608a1c83b653984dfa066da38e7c451
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871100"
---
# <a name="import-data-wizard-for-azure-search"></a>Procedura guidata Importa dati per Ricerca di Azure

Il portale di Azure include una procedura guidata **Importa dati** nel dashboard di Ricerca di Azure che permette di caricare i dati in un indice. In background, la procedura guidata configura e richiama un'*origine dati*, un *indice* e un *indicizzatore*, automatizzando così diversi passaggi del processo di indicizzazione: 

* Si connette a un'origine dati esterna nella stessa sottoscrizione di Azure.
* Facoltativamente, integra il riconoscimento ottico dei caratteri o l'elaborazione del linguaggio naturale per l'estrazione di testo da dati non strutturati.
* Genera un indice basato su campionamento dei dati e metadati dell'origine dati esterna.
* Esegue una ricerca per indicizzazione nell'origine dati per individuare contenuto ricercabile, serializzando e caricando documenti JSON nell'indice.

La procedura guidata non può connettersi a un indice predefinito o eseguire un indicizzatore esistente, ma all'interno della procedura guidata si può configurare un nuovo indice o indicizzatore per supportare la struttura e i comportamenti richiesti.

Non si ha familiarità con Ricerca di Azure? Vedere [Avvio rapido: Usare gli strumenti predefiniti del portale per importazione, indicizzazione e query in Ricerca di Azure](search-get-started-portal.md) per provare a eseguire l'importazione e l'indicizzazione con **Importa dati** e il set di dati di esempio realestate incorporato.

## <a name="start-importing-data"></a>Avviare l'importazione di dati

Questa sezione illustra come avviare la procedura guidata e offre una panoramica generale di ogni passaggio.

1. Nel [portale di Azure](https://portal.azure.com) aprire la pagina per la ricerca dei servizi dal dashboard o [trovare il servizio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nell'elenco.

2. Nella pagina di panoramica del servizio nella parte superiore fare clic su **Importa dati**.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

   > [!NOTE]
   > È possibile avviare **Importa dati** da altri servizi di Azure, inclusi Azure Cosmos DB, database SQL di Azure e Archiviazione Blob di Azure. Cercare **Aggiungi Ricerca di Azure** nel riquadro di spostamento a sinistra nella pagina di panoramica del servizio.

3. La procedura guidata si apre con il passaggio **Definisci la connessione ai dati**, in cui si può scegliere un'origine dati esterna da usare per l'operazione di importazione. Esistono diversi aspetti da conoscere su questo passaggio, pertanto assicurarsi di leggere la sezione [Input per l'origine dati](#data-source-inputs) per altri dettagli.

   ![Procedura guidata Importa dati nel portale](./media/search-import-data-portal/import-data-wizard-startup.png "Procedura guidata Importa dati per Ricerca di Azure")

4. Il passaggio successivo è **Aggiungi ricerca cognitiva**, nel caso in cui si vogliano includere riconoscimento ottico dei caratteri (OCR) di testo nei file di immagine o analisi del testo su dati non strutturati. Per questa attività vengono usati gli algoritmi di intelligenza artificiale di Servizi cognitivi. Questo passaggio è composto da due parti:
  
   Prima di tutto, [collegare una risorsa Servizi cognitivi](cognitive-search-attach-cognitive-services.md) a un set di competenze di Ricerca di Azure.
  
   In secondo luogo, scegliere quali arricchimenti di intelligenza artificiale includere nel set di competenze. Per una dimostrazione dettagliata, vedere questo [argomento di avvio rapido](cognitive-search-quickstart-blob.md).

   Se si vogliono solo importare dati, ignorare questo passaggio e passare direttamente alla definizione dell'indice.

5. Il passaggio successivo è **Personalizza indice di destinazione**, in cui è possibile accettare o modificare lo schema dell'indice presentato nella procedura guidata. La procedura guidata deduce i campi e i tipi di dati campionando i dati e leggendo i metadati dall'origine dati esterna.

   Per ogni campo [controllare gli attributi dell'indice](#index-definition) per abilitare comportamenti specifici. Se non si seleziona alcun attributo, l'indice non sarà utilizzabile. 

6. Il passaggio successivo è **Crea un indicizzatore**, che è un prodotto di questa procedura guidata. Un indicizzatore è un crawler che estrae metadati e dati ricercabili da un'origine dati esterna di Azure. Selezionando l'origine dati e collegando set di competenze (facoltativo) e un indice, si è configurato un indicizzatore completando i vari passaggi della procedura guidata.

   Assegnare un nome all'indicizzatore e fare clic su **Invia** per avviare il processo di importazione. 

È possibile monitorare l'indicizzazione nel portale facendo clic sull'indicizzatore nell'elenco **Indicizzatori**. Man mano che vengono caricati documenti, il loro conteggio aumenta per l'indice definito. La pagina del portale può impiegare alcuni minuti per rilevare gli aggiornamenti più recenti.

Non appena è stato caricato il primo documento, l'indice è pronto per l'esecuzione di query. È possibile usare [Esplora ricerche](search-explorer.md) per questa attività.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Input per l'origine dati

La procedura guidata **Importa dati** crea un oggetto origine dati permanente che specifica le informazioni di connessione a un'origine dati esterna. L'oggetto origine dati viene usato esclusivamente con gli [indicizzatori](search-indexer-overview.md) e può essere creato per le origini dati seguenti: 

* [SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Archiviazione tabelle di Azure](cognitive-search-concept-intro.md) (non supportato per le pipeline di [ricerca cognitiva](search-howto-indexing-azure-tables.md))

Un set di dati bidimensionale è un input obbligatorio. È possibile eseguire l'importazione solo da una singola tabella, di una vista di database o di una struttura dei dati equivalente. 

È consigliabile creare questa struttura di dati prima di eseguire la procedura guidata e deve contenere il contenuto. Non eseguire la **importare dati** procedura guidata in un'origine dati vuota.

|  Selezione | Descrizione |
| ---------- | ----------- |
| **Origine dati esistente** |Se nel servizio di ricerca sono già definiti indicizzatori, è possibile selezionare una definizione esistente dell'origine dati per un'altra importazione. In Ricerca di Azure, gli oggetti origine dati vengono usati solo dagli indicizzatori. È possibile creare un oggetto origine dati a livello di codice o tramite la procedura guidata **Importa dati**.|
| **Esempi**| Ricerca di Azure ospita un database SQL di Azure pubblico gratuito che è possibile usare per apprendere il funzionamento delle richieste di importazione e query in Ricerca di Azure. Vedere [Avvio rapido: Usare gli strumenti predefiniti del portale per importazione, indicizzazione e query in Ricerca di Azure](search-get-started-portal.md) per una procedura dettagliata. |
| **Database SQL di Azure** |Il nome del servizio, le credenziali per un utente di database con autorizzazione di lettura e un nome di database possono essere specificati nella pagina o tramite una stringa di connessione ADO.NET. Per visualizzare o personalizzare le proprietà, scegliere l'opzione relativa alla stringa di connessione. <br/><br/>È necessario specificare nella pagina la tabella o la vista che fornisce il set di righe. Questa opzione viene visualizzata quando la connessione ha esito positivo, con un elenco a discesa che permette di effettuare una selezione. |
| **Macchine virtuali SQL Server in Azure** |Specificare un nome completo del servizio, un ID utente, una password e un database come stringa di connessione. Per usare questa origine dati, è necessario avere già installato un certificato nell'archivio locale che esegue la crittografia della connessione. Per istruzioni, vedere [Connessione di una macchina virtuale SQL a Ricerca di Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>È necessario specificare nella pagina la tabella o la vista che fornisce il set di righe. Questa opzione viene visualizzata quando la connessione ha esito positivo, con un elenco a discesa che permette di effettuare una selezione. |
| **Cosmos DB** |Tra i requisiti: l'account, il database e la raccolta. Tutti i documenti nella raccolta verranno inclusi nell'indice. È possibile definire una query per rendere flat o filtrare il set di righe o lasciare vuota la query. In questa procedura guidata non è necessaria una query.|
| **Archiviazione BLOB di Azure** |Tra i requisiti: l'account di archiviazione e un contenitore. Facoltativamente, se i nomi dei BLOB seguono una convenzione di denominazione virtuale a scopo di raggruppamento, è possibile specificare la porzione directory virtuale del nome come una cartella nel contenitore. Per altre informazioni, vedere [Indicizzazione di documenti nell'archivio BLOB di Azure con Ricerca di Azure](search-howto-indexing-azure-blob-storage.md). |
| **Archiviazione tabelle di Azure** |Tra i requisiti: l'account di archiviazione e un nome di tabella. Facoltativamente, è possibile specificare una query per recuperare un subset delle tabelle. Per altre informazioni, vedere [Indicizzazione nell'archivio tabelle di Azure con Ricerca di Azure](search-howto-indexing-azure-tables.md). |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Attributi dell'indice

La procedura guidata **Importa dati** genera un indice, che verrà popolato con i documenti ottenuti dall'origine dati di input. 

Per un indice funzionale, assicurarsi di aver definito gli elementi seguenti.

1. Un campo deve essere contrassegnato come **Chiave** e verrà usato per identificare in modo univoco ogni documento. La **Chiave** deve essere *Edm.string*. 

   Se i valori dei campi includono spazi o trattini, è necessario impostare l'opzione **Chiavi di codifica Base 64** nel passaggio **Crea un indicizzatore**, in **Opzioni avanzate**, per evitare il controllo di convalida per questi caratteri.

1. Impostare gli attributi dell'indice per ogni campo. Se non si seleziona alcun attributo, l'indice è essenzialmente vuoto, fatta eccezione per il campo obbligatorio della chiave. Come minimo, scegliere uno o più degli attributi seguenti per ogni campo.
   
   + **Recuperabile** restituisce il campo nei risultati della ricerca. Ogni campo che fornisce contenuto ai risultati della ricerca deve avere questo attributo. L'impostazione di questo campo non influisce significativamente sulle dimensioni dell'indice.
   + **Filtrabile** consente di fare riferimento al campo nelle espressioni di filtro. Ogni campo usato in un'espressione **$filter** deve avere questo attributo. Le espressioni di filtro sono per le corrispondenze esatte. Poiché le stringhe di testo rimangono inalterate, è necessario ulteriore spazio di archiviazione per ospitare il contenuto verbatim.
   + **Ricercabile** abilita la ricerca full-text. Ogni campo usato nelle query in formato libero o nelle espressioni di query deve contenere questo attributo. Per ogni campo contrassegnato come **Ricercabile** vengono creati indici invertiti.

1. Facoltativamente, impostare questi attributi in base alle esigenze:

   + **Ordinabile** consente di usare il campo in un ordinamento. Ogni campo usato in un'espressione **$Orderby** deve avere questo attributo.
   + **Con facet** abilita il campo per l'esplorazione in base a facet. Solo i campi contrassegnati anche come **Filtrabile** possono essere contrassegnati come **Con facet**.

1. Impostare un **Analizzatore** se si vogliono indicizzazione e query migliorati per la lingua. Il valore predefinito è *Standard - Lucene* ma è possibile scegliere *Inglese - Microsoft* se si vuole usare l'analizzatore Microsoft per l'elaborazione lessicale avanzata, ad esempio per la risoluzione di forme verbali o nominali irregolari.

   + Selezionare **Ricercabile** per abilitare l'elenco **Analizzatore**.
   + Scegliere un analizzatore dall'elenco. 
   
   Al momento è possibile specificare solo gli analizzatori del linguaggio. Per usare un analizzatore personalizzato o un analizzatore non del linguaggio come Keyword, Pattern e così via, è necessario scrivere codice. Per altre informazioni sugli analizzatori, vedere [Creare un indice per i documenti in più lingue](search-language-support.md).

1. Selezionare la casella di controllo **Strumento suggerimenti** per abilitare i suggerimenti di query con completamento automatico nei campi selezionati.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli indicizzatori, usare questi collegamenti:

* [Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indicizzazione di Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indicizzazione di documenti nell'archivio BLOB di Azure con Ricerca di Azure](search-howto-indexing-azure-blob-storage.md)
* [Indicizzazione nell'archivio tabelle di Azure con Ricerca di Azure](search-howto-indexing-azure-tables.md)