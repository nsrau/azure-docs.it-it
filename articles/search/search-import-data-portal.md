<properties
	pageTitle="Importare dati in Ricerca di Azure tramite indicizzatori nel portale di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Usare la procedura guidata Importa dati di Ricerca di Azure nel portale di Azure per eseguire una ricerca per indicizzazione nell'archivio BLOB di Azure, nell'archivio tabelle, nel database SQL e in SQL Server in macchine virtuali di Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="jhubbard"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/29/2016"
	ms.author="heidist"/>

# Importare dati in Ricerca di Azure tramite il portale

Il portale di Azure include una procedura guidata **Importa dati** nel dashboard di Ricerca di Azure che permette di caricare i dati in un indice.

  ![Importa dati sulla barra dei comandi][1]

Internamente, la procedura guidata configurare e richiama un *indicizzatore*, automatizzando così diversi passaggi del processo di indicizzazione:

- Connettersi a un'origine dati esterna nella sottoscrizione di Azure corrente
- Generare automaticamente uno schema dell'indice in base alla struttura dei dati di origine
- Creare documenti in base a un set di righe recuperato dall'origine dati
- Caricare documenti nell'indice nel servizio di ricerca

È possibile provare questo flusso di lavoro con dati di esempio in DocumentDB. Per istruzioni, vedere [Introduzione a Ricerca di Azure nel portale](search-get-started-portal.md).

## Origini dati supportate dalla procedura guidata Importa dati

La procedura guidata Importa dati supporta le origini dati seguenti:

- Database SQL di Azure
- Dati relazionali di SQL Server in una macchina virtuale di Azure
- Azure DocumentDB
- Archivio BLOB di Azure (in anteprima)
- Archivio tabelle di Azure (in anteprima)

Un set di dati bidimensionale è un input obbligatorio. È possibile eseguire l'importazione solo da una singola tabella, di una vista di database o di una struttura dei dati equivalente. È necessario creare la struttura dei dati prima di eseguire la procedura guidata.

Si noti che alcuni degli indicizzatori sono ancora in anteprima, vale a dire che la definizione dell'indicizzatore è supportata dalla versione di anteprima dell'API. Per altre informazioni e collegamenti, vedere la [panoramica sugli indicizzatori](search-indexer-overview.md).

## Connettersi ai dati

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire il dashboard del servizio. Per visualizzare i servizi esistenti nella sottoscrizione corrente, è possibile fare clic su **Servizi di ricerca** nell'indice.

2. Fare clic su **Importa dati** nella barra dei comandi per aprire il pannello corrispondente.

3. Fare clic su **Definisci la connessione ai dati** per specificare una definizione dell'origine dati usata da un indicizzatore. Per le origini dati all'interno della sottoscrizione, la procedura guidata può in genere rilevare e leggere le informazioni di connessione, riducendo al minimo i requisiti di configurazione complessivi.

| | |
|--------|------------|
|**Origine dati esistente** | Se nel servizio di ricerca sono già definiti indicizzatori, è possibile selezionare una definizione esistente dell'origine dati per un'altra importazione.|
|**Database SQL di Azure** | Il nome del servizio, le credenziali per un utente di database con autorizzazione di lettura e un nome di database possono essere specificati nella pagina o tramite una stringa di connessione ADO.NET. Per visualizzare o personalizzare le proprietà, scegliere l'opzione relativa alla stringa di connessione. <br/><br/>È necessario specificare nella pagina la tabella o la vista che fornisce il set di righe. Questa opzione viene visualizzata quando la connessione ha esito positivo, con un elenco a discesa che permette di effettuare una selezione.|
|**Macchine virtuali SQL Server in Azure** | Specificare un nome completo del servizio, un ID utente, una password e un database come stringa di connessione. Per usare questa origine dati, è necessario avere già installato un certificato nell'archivio locale che esegue la crittografia della connessione. <br/><br/>È necessario specificare nella pagina la tabella o la vista che fornisce il set di righe. Questa opzione viene visualizzata quando la connessione ha esito positivo, con un elenco a discesa che permette di effettuare una selezione.
|**DocumentDB** |Tra i requisiti: l'account, il database e la raccolta. Tutti i documenti nella raccolta verranno inclusi nell'indice. È possibile definire una query per rendere flat o filtrare il set di righe oppure per rilevare i documenti modificati per operazioni successive di aggiornamento dei dati.|
|**Archivio BLOB di Azure** | Tra i requisiti: l'account di archiviazione e un contenitore. Facoltativamente, se i nomi dei BLOB seguono una convenzione di denominazione virtuale a scopo di raggruppamento, è possibile specificare la porzione directory virtuale del nome come una cartella nel contenitore. Per altre informazioni, vedere [Indicizzazione di documenti nell'archivio BLOB di Azure con Ricerca di Azure](search-howto-indexing-azure-blob-storage.md). |
|**Archivio tabelle di Azure** | Tra i requisiti: l'account di archiviazione e un nome di tabella. Facoltativamente, è possibile specificare una query per recuperare un subset delle tabelle. Per altre informazioni, vedere [Indicizzazione nell'archivio tabelle di Azure con Ricerca di Azure](search-howto-indexing-azure-tables.md). |

## Personalizzare l'indice di destinazione

In genere, un indice preliminare viene dedotto dal set di dati. È possibile aggiungere, modificare o eliminare campi per completare lo schema. È anche possibile impostare attributi a livello di campo per determinarne i comportamenti di ricerca successivi.

1. In **Personalizza indice di destinazione** specificare il nome e una **chiave** usati per identificare in modo univoco ogni documento. La chiave deve essere una stringa. Se i valori dei campi includono spazi o trattini, assicurarsi di impostare le opzioni avanzate in **Importare i dati** per evitare il controllo di convalida per questi caratteri.

2. Esaminare e modificare i campi rimanenti. Il nome del campo e il tipo sono in genere compilati automaticamente. È possibile modificare il tipo di dati.

3. Impostare gli attributi dell'indice per ogni campo:

 - Recuperabile restituisce il campo nei risultati della ricerca.
 - Filtrabile consente di fare riferimento al campo nelle espressioni di filtro.
 - Ordinabile consente di usare il campo in un ordinamento.
 - Con facet abilita il campo per l'esplorazione in base a facet.
 - Ricercabile abilita la ricerca full-text.
  
4. Fare clic sulla scheda **Analizzatore** se si vuole specificare un analizzatore del linguaggio a livello di campo. Al momento è possibile specificare solo gli analizzatori del linguaggio. Per usare un analizzatore personalizzato o un analizzatore non del linguaggio come Keyword, Pattern e così via, è necessario scrivere codice.

 - Fare clic su **Ricercabile** per designare la ricerca full-text sul campo e abilitare l'elenco a discesa Analizzatore.
 - Scegliere l'analizzatore da usare. Per informazioni dettagliate, vedere [Creare un indice per i documenti in più lingue](search-language-support.md).

5. Fare clic su **Strumento suggerimenti** per abilitare i suggerimenti di query con completamento automatico nei campi selezionati.


## Importare i dati

1. In **Importare i dati** specificare un nome per l'indicizzatore. È importante ricordare che il prodotto della procedura guidata Importa dati è un indicizzatore. Successivamente, per visualizzarlo o modificarlo sarà possibile selezionarlo dal portale anziché eseguire nuovamente la procedura guidata.

2. Specificare la pianificazione, che è basata sul fuso orario dell'area in cui è stato effettuato il provisioning del servizio.

3. Impostare le opzioni avanzate per specificare le soglie relative al proseguimento dell'indicizzazione in caso di eliminazione di un documento. È anche possibile specificare se i campi **chiave** possono contenere spazi e barre.

## Modificare un indicizzatore esistente

Nel dashboard del servizio fare doppio clic sul riquadro Indicizzatore per visualizzare un elenco di tutti gli indicizzatori creati per la sottoscrizione. Fare doppio clic su uno degli indicizzatori per eseguirlo, modificarlo o eliminarlo. È possibile sostituire l'indice con un altro esistente, modificare l'origine dati e impostare le opzioni per le soglie di errore durante l'indicizzazione.

## Modificare un indice esistente

In Ricerca di Azure, gli aggiornamenti strutturali in un indice richiedono la ricompilazione dell'indice, che prevede di eliminare e creare di nuovo l'indice e di ricaricare i dati. Gli aggiornamenti strutturali includono la modifica di un tipo di dati e la ridenominazione o eliminazione di un campo.

Le modifiche che non richiedono la ricompilazione includono l'aggiunta di un nuovo campo, la modifica dei profili di punteggio, la modifica degli strumenti suggerimenti o la modifica degli analizzatori del linguaggio. Per altre informazioni, vedere [Aggiornare l'indice](https://msdn.microsoft.com/library/azure/dn800964.aspx).

## Passaggio successivo

Per altre informazioni sugli indicizzatori, usare questi collegamenti:

- [Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Connessione di DocumentDB con Ricerca di Azure tramite indicizzatori](../documentdb/documentdb-search-indexer.md)
- [Indicizzazione di documenti nell'archivio BLOB di Azure con Ricerca di Azure](search-howto-indexing-azure-blob-storage.md)
- [Indicizzazione nell'archivio tabelle di Azure con Ricerca di Azure](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

<!---HONumber=AcomDC_0928_2016-->