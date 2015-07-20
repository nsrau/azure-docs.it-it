<properties 
	pageTitle="Creazione di un'applicazione di prototipo per la Ricerca di Azure" 
	description="Creare il primo prototipo di applicazione per iniziare la Ricerca di Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Creazione di un'applicazione di prototipo per la Ricerca di Azure

Gli sviluppatori che valutano Ricerca di Azure quasi sempre iniziano con un'applicazione di test preliminare che illustra il valore di aggiunta di Ricerca di Azure a un'applicazione personalizzata. In questo articolo sono precisati alcuni concetti fondamentali per velocizzare il processo di creazione di prototipi.
 
- Un progetto di visual studio c# che include file data.json e schema.json. Con dati di esempio è possibile compilare ed eseguire la soluzione e confermare che il corretto funzionamento del sistema prima di scrivere una riga di codice immediatamente. 

	Se possibile, provare a sostituirli autonomamente, separatamente dello schema e i file di dati con i dati dell'azienda. Quindi, quando si esegue l'applicazione, l'indice che viene creato è basato sullo schema e documenti facendo riferimento ai dati file, saranno immediatamente disponibili per utilizzare nella Ricerca di Azure.

- Indicazioni su come strutturare i dati aggiornati per la Ricerca di Azure. È necessario un set di dati per ogni indice. La Ricerca di Azure utilizza JSON per la serializzazione di dati.

- Infine, sono indicate alcune funzionalità aggiuntive che diventeranno il prototipo a livello successivo, inserendo funzionalità di ricerca specifiche come spostamento collaborazione, assegnazione dei punteggi profili per i risultati dell'ottimizzazione digitazione o query di completamento automatico e funzionalità correlate alla pagina di ricerca non è disponibile nella ricerca full-text rigorosamente alternativo.

Al termine, sarà necessaria un'applicazione di prototipo, utilizzando i dati per dimostrare le affidabili funzionalità di ricerca tramite la Ricerca di Azure.

## Preparare i dati

Vengono effettuate le operazioni di ricerca rispetto ad un indice di ricerca in Ricerca di Azure. Un indice dispone di più parti, inclusi il punteggio profili e suggesters, ma la maggior parte di un indice è costituito da campi dati utilizzati nelle operazioni di ricerca.

Il set di dati che costituisce i campi deve essere un file di dati flat (in JSON), dove ogni campo nel set di dati esegue il mapping a un campo nell'indice che si sta tentando di compilare equivalente. Vengono aggiunte altre parti dell'indice, ad esempio un profilo di assegnazione dei punteggi o opzioni CORS, in modo indipendente, direttamente nello schema.

Le Origini dei dati che utilizzano JSON in modalità nativa, ad esempio l’archiviazione tabelle di Azure, forniranno dati che è più facile utilizzare rispetto ai dati relazionali. Sarà necessaria una singola visualizzazione o una tabella che fornisce tutti i campi in un set di righe.

Per caricare i file di dati e dello schema utilizzando il codice nell'applicazione esemplificativa del prototipo, è possibile sovrascrivere i file di schema e i dati predefiniti incorporati nella soluzione.

> [AZURE.NOTE]È possibile caricare più file di dati, ad esempio per caricare i dati in batch, ma la struttura dei dati deve essere uguale per ogni file di dati caricato nello stesso indice. Non è possibile aggiungere indici. Ogni indice funziona come una raccolta di ricerca autonomo.

## Testare la soluzione prototipo nel sistema

1. [Creare un servizio di Ricerca di Azure nel Portale di Azure](search-create-service-portal.md).

    È possibile aggiungere una versione condivisa (gratuita) del servizio per qualsiasi sottoscrizione Azure esistente. Il servizio condiviso viene spesso utilizzato per prototipi. Tenere presente che il servizio condiviso fornisce 50 MB di dati o 10.000 documenti in totale, rispettivamente. Inoltre, i documenti e dati possano essere distribuiti in un massimo di 3 indici.

    Esecuzione dell'applicazione esemplificativa del prototipo, con i file di dati di esempio incorporata, verrà creata in un indice detto "musicstoreindex", che contiene 246 documenti, in KB 278 nel servizio di Ricerca di Azure. Più avanti in questa procedura dettagliata, l’indice sarà sostituito da un nuovo indice utilizzando i dati di Adventure Works che utilizzerà fino a # # documenti e # # MB.

2. [Scaricare la soluzione del generatore di prototipo](http://go.microsoft.com/fwlink/p/?LinkId=536479). Questa è una soluzione di Visual Studio 2013 in C# che consente di creare un'applicazione console utilizzata per creare, caricare e un indice di query. Se non si dispone di Visual Studio, è possibile ottenere la versione gratuita di [Visual Studio 2013 Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx).

3. Modificare il file app. config per aggiungere le impostazioni di configurazione del servizio di ricerca e la chiave dell'api di destinazione.

	È possibile ottenere l'URL e la chiave api amministratore da [dashboard del servizio nel portale di](search-create-service-portal.md). Per l'URL, digitare il percorso completo del nome del servizio, incluso il prefisso https e `search.windows.net` dominio.

4. Compilare la soluzione per assicurarsi che non siano presenti errori di compilazione. Potrebbe essere necessario aggiornare i pacchetti per risolvere errori di compilazione. Fare clic con il pulsante destro del mouse su **Gestisci pacchetti NuGet** nella soluzione in Esplora soluzioni.

5. Eseguire la soluzione utilizzando i file di schema e i dati incorporati. Questo passaggio è facoltativo, ma il funzionamento della soluzione è confermato prima di investire qualsiasi momento in cui aggiungere i propri dati. La console restituisce i messaggi seguenti:

	- Indice eliminato (si verifica solo se esiste un indice di nome "musicstoreindex")
	- Indice creato (viene creato un nuovo indice denominato "musicstoreindex" nel servizio)
	- Registrazione dei documenti (un messaggio per ogni file JSON)
	- Attesa di 5 secondi (consente l'indicizzazione completare prima di rilasciare la prima query)
	- Risultati della ricerca per termine di ricerca migliore con alcun aumento (viene eseguita una query semplice, dimostrando che i dati vengono caricati nell'indice)

6. Arrestare l'applicazione ed eliminare l'indice per fare spazio per quelle in uso.

    > [AZURE.TIP]È possibile utilizzare [il portale](https://portal.azure.com) per eliminare l'indice. Fare clic sul nome dell’indice per aprire il pannello Indice e utilizzare il **eliminare** comando.

## Sostituire i file di schema e i dati JSON con i dati

Nella soluzione prototipo, c'è uno schema file e tre dati file: data1.json, data2.json e data3.json. Il file di schema (schema.json) deve essere sostituito con uno schema che descrive i dati.

Per default, questi file si trovano nella cartella della soluzione:

![][1]

Se è possibile ottenere i dati in file JSON, è possibile sovrascrivere i file esistenti con i dati e quindi eseguire l'applicazione per creare e caricare un indice. La distribuzione dei dati su più file consente di dimostrare un'operazione di caricamento del batch.

Altri approcci per il caricamento di dati includono l'utilizzo di un indicizzatore (richiede un'origine dati di Azure DocumentDB o un'origine dati di Database SQL Azure). I codice di esempio che dimostrano ulteriori modalità di caricamento dei dati sono reperibili in [Ricerca di Azure Video ed esercitazioni elenco](https://msdn.microsoft.com/library/azure/dn818681.aspx) su MSDN.

### Modificare la query

La query predefinita incorporata in soluzione riferimenti a campi e strutture dai file di dati incorporati. Una volta che si sarà sovrascritto file di dati per utilizzare i dati, è possibile impostare come commento il codice della query o modificare in modo da utilizzare campi dallo schema. Per ulteriori informazioni sulla sintassi di query di ricerca, vedere [ricerca documenti](https://msdn.microsoft.com/library/azure/dn798927.aspx).

### Compilare ed eseguire l'applicazione

Premere **F5** per eseguire lo script. Come prima, verranno visualizzati i messaggi che indicano che l'indice è stato creati, caricato e queryable.

È ora disponibile un indice operativo che può essere utilizzato come base per un'ulteriore analisi.

A questo punto, è possibile passare a [Fiddler](search-fiddler.md) o [Chrome Postman](search-chrome-postman.md) per eseguire query, modificare le applicazioni di esempio per utilizzare il servizio di ricerca e l'indice o aggiungere un codice personalizzato che fornisca una visualizzazione dei dati.

## Aggiungere un profilo di assegnazione dei punteggi

È consigliabile sperimentare [punteggio profili](search-get-started-scoring-profiles.md) tramite il portale o tramite codice. L’assegnazione dei punteggi profili aumenta l'importanza di un termine di ricerca trovato in un campo specifico. Ad esempio, se il termine di ricerca è un nome di città, si troveranno documenti con *Seattle* nel campo città più in alto nei risultati di documenti con *Seattle* in un campo descrittivo.

Aggiunta di un punteggio delle modifiche al profilo dell'indice: occorre ricreare e ricaricare l'indice ogni volta che si modifica lo schema. Per questo motivo, considerare l'aggiunta di assegnazione dei punteggi ai segmenti di codice di profilo per il prototipo o modificare l'assegnazione dei punteggi esempi di profilo per utilizzare il codice di indicizzazione.

Vedere [aggiungere un profilo di assegnazione dei punteggi](https://msdn.microsoft.com/library/dn798928.aspx) per la documentazione di riferimento in profili di punteggio.

## Aggiungere un suggester

Il suggester si riferisce alla funzionalità di ricerca più comuni che prevede un elenco di possibili termini di ricerca basato su input di testo da parte dell'utente ("wea" richiede la digitazione di un elenco di termini di ricerca di completamento automatico per "meteo", "canale meteo", "sotterraneo meteo" e così via).

Per aggiungere suggester, aggiungere una sezione per lo schema di indice che specifica quali campi vengono utilizzati per generare query per il completamento automatico o typeahead. Campi che contengono i nomi o stringhe più brevi con valori non ricorrenti tendono a utilizzare al meglio. Vedere [Create Index](https://msdn.microsoft.com/library/dn798928.aspx) per ulteriori informazioni.

## Provare un analizzatore del linguaggio

Gli analizzatori di linguaggio forniscono regole linguistiche per distinguere tra parole essenziali e non essenziali, form principale e anche sinonimi. Per impostazione predefinita, la Ricerca di Azure utilizza l'analizzatore del linguaggio Lucene per l'inglese. È possibile specificare diversi analizzatori come un attributo dell'indice in campi specifici, che consente di compilare gli schemi ed i documenti che includono campi utilizzando analizzatori diversi (ad esempio, un'applicazione multilingua possibile combinare campi francese e inglese affiancate nello stesso documento). Vedere [supporto linguistico](https://msdn.microsoft.com/library/dn879793.aspx) per ulteriori dettagli.

## Passaggi successivi

Nelle sezioni precedenti è stato modificato l'indice per aggiungere ulteriori funzionalità per il prototipo. A questo punto, le modifiche di indice sono quasi complete (non sono stati trattati abilitazione della condivisione CORS, che è l'unica modifica allo schema rimanente che è possibile apportare).

Ulteriormente la creazione di prototipi potenzialmente porterà in due direzioni diverse: un'attenzione verso l'esterno per l'interfaccia utente, ad esempio aggiungendo una navigazione sfaccettate o altri filtri che consentono di restringere la ricerca o ulteriori approfondimenti sugli aspetti di sincronizzazione dei dati della soluzione. Molte soluzioni includono dati volatili. Per molti sviluppatori, la sincronizzazione degli aggiornamenti di dati tra sistemi di database transazionali ed un indice di Ricerca di Azure è la priorità successiva della verifica dei comportamenti di ricerca di base.

Per ulteriori informazioni, visitare:

- [Flussi di lavoro tipici per i progetti di sviluppo tramite la Ricerca di Azure](search-workflow.md)

- [Personalizzazione dell'indicizzatore di Ricerca di Azure](search-indexers-customization.md)

- [Navigazione sfaccettata nella ricerca di Azure](search-faceted-navigation.md)

- [Risultati della ricerca di paging nella Ricerca di Azure](search-pagination-page-layout.md)


<!--Image references-->
[1]: ./media/search-build-prototype/azsearch-datafiles.png
 

<!---HONumber=July15_HO2-->