<properties
	pageTitle="Flusso di lavoro tipico per lo sviluppo di Ricerca di Azure | Microsoft Azure"
	description="Flusso di lavoro o guida di orientamento per la creazione di applicazioni prototipo e di produzione integrate con Ricerca di Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Flusso di lavoro tipico per lo sviluppo di Ricerca di Azure

In questo articolo viene riportata una guida di orientamento per includere Ricerca di Azure come componente che fornisce la funzionalità di ricerca nell’applicazione personalizzata. Sia che si stia sondando il terreno o si sia pronti per l’utilizzo, sono necessarie alcune indicazioni preliminari su come integrare Ricerca di Azure nel progetto di sviluppo personalizzato.

Nelle sezioni seguenti, è stato suddiviso un flusso di lavoro tipico come un prototipo iniziale che consentirà di valutare come Ricerca di Azure soddisfi i requisiti di ricerca dell'applicazione. Nella seconda parte di questo articolo vengono illustrate importanti decisioni di progettazione da tenere presenti per una più seria attività di sviluppo dell’applicazione.

Prima di iniziare la creazione del prototipo, si consiglia di incominciare con una delle esercitazioni introduttive o con questo [video di un’ora della presentazione degli approfondimenti](http://azure.microsoft.com/documentation/videos/tech-ed-europe-2014-azure-search-deep-dive/). Le esercitazioni introduttive sono disponibili per i seguenti linguaggi: [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md), [Node. js](search-get-started-nodejs.md).

##Sviluppo del prototipo

Il modo più rapido per sviluppare correttamente un prototipo in genere include i passaggi riportati in questa sezione. I passaggi includono il provisioning di un servizio, la definizione di uno schema per l'indice, il caricamento dell'indice con documenti e l’esecuzione di query sull'indice.

Per le applicazioni con dati volatili (ad esempio, se il caso comune include modifiche rapide al magazzino o al contenuto), il prototipo deve includere anche un componente per l'aggiornamento dei documenti.

   ![][1]

###Passaggio 1: eseguire il provisioning del servizio

Ricerca di Azure è un servizio online completamente gestito disponibile tramite una sottoscrizione di Azure. [Una volta eseguita l'iscrizione ad Azure](http://azure.microsoft.com/pricing/free-trial/), l’aggiunta del servizio di ricerca è un’operazione veloce. Per le istruzioni su come aggiungere un servizio di ricerca alla sottoscrizione, visitare la pagina [Creare un servizio di ricerca nel portale](search-create-service-portal.md).

Sono disponibili due livelli di prezzo per la selezione. Si consiglia di utilizzare il servizio condiviso (gratuito) per la creazione del prototipo, precisando che sarà necessario lavorare con un piccolo subset di dati. Il servizio condiviso è gratuito per gli abbonati esistenti (con abbonamenti di prova o regolari) e si installa velocemente, ma limita il numero di indici e documenti utilizzabili a 3 indici, fino a 10.000 documenti per ogni indice o 50 MB di spazio di archiviazione totale, rispettivamente.

###Passaggio 2: creare l'indice

Dopo aver creato il servizio, è possibile creare un indice, iniziando con la definizione dello schema.

Il modo più rapido e semplice per creare un indice è tramite il portale. Ciascun documento deve avere almeno una chiave univoca e un campo contenente dati disponibili per la ricerca. Per iniziare, vedere la pagina [Creare un indice nel portale](search-create-index-portal.md).

> [AZURE.NOTE]**Contenuto di un indice di Ricerca di Azure**
>
> Un *indice* contiene dati organizzati e persistenti che fungono da *corpo della ricerca* per tutte le operazioni di ricerca successive. Il corpo della ricerca viene archiviato nel cloud come parte della sottoscrizione del servizio di ricerca che consente di eseguire le operazioni di ricerca rapidamente e in modo coerente. Nella terminologia della ricerca, un elemento del corpo della ricerca viene denominato *documento* e la somma totale di tutti i documenti è la *raccolta documenti*.
>
>Uno *schema di indice* definisce tutti i campi all'interno di un documento per nome, tipo di dati e attributi che specificano se il campo è disponibile per la ricerca, per i filtri, per i facet e così via.
>
> Oltre alla struttura del documento, uno schema di indice specifica i profili punteggio che forniscono i criteri per incrementare un punteggio di ricerca e le impostazioni di configurazione che consentono il completamento automatico delle query (suggesters) e dei CORS per le richieste di query tra domini. **Per i prototipi, è consigliabile iniziare specificando semplicemente i campi in un documento**, quindi aggiungere altre funzionalità in modo incrementale (vedere più avanti il passaggio 5 per un elenco delle funzionalità da aggiungere).
>
> Per un esempio reale, prendere in considerazione un'applicazione e-commerce. L'indice di ricerca contiene tutti i prodotti o i servizi che sono disponibili per la ricerca nell'applicazione (tutto ciò che viene restituito nei risultati della ricerca). Sarà disponibile un documento per ogni SKU. Ogni documento include il nome del prodotto, il marchio, le dimensioni, il prezzo, i colori e anche i riferimenti a immagini o ad altri file di risorse che si desidera includere nei risultati di ricerca.

###Passaggio 3: caricare i documenti

Dopo avere salvato l'indice in Ricerca di Azure, il passaggio successivo consiste nel popolare l'indice con i documenti. In questo passaggio, i dati vengono caricati, analizzati, suddivisi in token e archiviati in strutture di dati (ad esempio indici invertiti) progettati per carichi di lavoro di ricerca.

I dati caricati in un indice devono essere conformi allo schema definito nel passaggio precedente. I dati del documento vengono rappresentati come un set di coppie chiave/valore per ogni campo, nel formato JSON. Se lo schema specifica un campo per l’ID (chiave), un campo per il nome, un campo per il numero e un campo per l’URL (che potrebbe accadere se le immagini esterne fanno parte dei risultati della ricerca), tutti i documenti passati nell'indice devono avere valori (o null) per ogni campo.

Esistono diversi modi per caricare i documenti ed attualmente tutti richiedono un'API. Per la maggior parte dei prototipi, questo passaggio potrebbe risultare il più lungo a causa di un requisito di codifica. Le opzioni sono descritte di seguito.

> [AZURE.NOTE]Si tenga presente che il servizio condiviso è limitato a 10.000 documenti per ogni indice. Assicurarsi di ridurre il set di dati in modo da restare entro i limiti. Per ulteriori informazioni, vedere [Limitazioni e vincoli](https://msdn.microsoft.com/library/dn798934.aspx).

####Come caricare i dati in un indice

Un approccio consiste nell'utilizzare un indicizzatore. Per Azure DocumentDB o per le origini dati relazionali SQL Server in Azure (in particolare Database SQL di Azure o SQL Server in una macchina virtuale di Azure), è possibile utilizzare gli [indicizzatori](https://msdn.microsoft.com/library/dn946891.aspx) per recuperare i documenti da un'origine dati supportata. Gli esempi di codice che utilizzano gli indicizzatori per il caricamento dei documenti sono disponibili in una qualsiasi delle seguenti esercitazioni introduttive: [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md), [Node. js](search-get-started-nodejs.md).

Una seconda opzione consiste nello scrivere un semplice programma che utilizza l'API REST o la libreria .NET che carica i documenti:

- [Aggiungere, aggiornare o eliminare documenti (API REST)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Classe DocumentOperationsExtensions](https://msdn.microsoft.com/library/microsoft.azure.search.documentoperationsextensions.aspx)

Una terza opzione adatta per i set di dati molto piccoli consiste nell’utilizzare [Fiddler](search-fiddler.md) o [Chrome Postman](search-chrome-postman.md) per caricare i documenti.

Una quarta opzione, probabilmente la più semplice, consiste nell’utilizzo del codice presente in [Esempio di API REST C# di Adventure Works](https://azuresearchadventureworksdemo.codeplex.com/) che carica i documenti da un database incorporato (con estensione mdf) nella soluzione o in [Esempio di API REST C# di Profili di punteggio](https://azuresearchscoringprofiles.codeplex.com/) che carica i dati dai file di dati JSON inclusi nella soluzione.

> [AZURE.TIP]È possibile modificare ed eseguire l’[esempio di profili di punteggio](https://azuresearchscoringprofiles.codeplex.com/), sostituendo il file di dati JSON e il file schema.json con dati validi per l'applicazione.

###Passaggio 4: eseguire query nei documenti

Una volta che i documenti vengono caricati nell'indice, è possibile scrivere la prima query.

Il modo più rapido per ottenere i risultati iniziali della ricerca dal servizio di ricerca consiste nell'utilizzare [Fiddler](search-fiddler.md) o [Chrome Postman](search-chrome-postman.md) per visualizzare una risposta, ma in uno scenario reale, è possibile scrivere il codice dell'interfaccia utente semplice per visualizzare i risultati in un formato leggibile.

Le API per le operazioni di ricerca includono:

- [Operazione di ricerca in documenti](https://msdn.microsoft.com/library/dn798927.aspx)
- [Classe SearchIndexClient](https://msdn.microsoft.com/library/microsoft.azure.search.searchindexclient.aspx)

Le query di Ricerca di Azure possono essere molto semplici. Includendo `search=*` nell'URI vengono restituiti i primi 50 elementi del corpo di ricerca, specificando `search=<some phrase>` verrà eseguita una ricerca full-text della frase, restituendo fino a 50 documenti, supponendo che esistano almeno 50 documenti contenenti una corrispondenza dell’input del termine.

50 documenti è il valore predefinito. È possibile modificare il numero di elementi restituiti tramite il parametro di query `$Count`. Questo parametro è descritto in [Eseguire ricerche nei documenti](https://msdn.microsoft.com/library/dn798927.aspx).

> [AZURE.TIP]L’elenco più completo di esempi di query è disponibile in [Eseguire ricerche nei documenti](https://msdn.microsoft.com/library/dn798927.aspx), ma è anche possibile esaminare il [riferimento della sintassi](https://msdn.microsoft.com/library/dn798920.aspx) per l'elenco degli operatori supportati.

###Passaggio 5: esplorare altre funzionalità

Ora che si dispone di un servizio e di un indice, è possibile provare le funzionalità per sviluppare ulteriormente l'esperienza di ricerca. Un breve elenco di funzionalità da esaminare è riportato più avanti.

Le **pagine di ricerca** spesso includono i conteggi dei documenti in un set di risultati o utilizzano l'impaginazione per suddividere i risultati in numeri più gestibili. Per informazioni dettagliate, vedere [Paginazione](search-pagination-page-layout.md).

**searchMode = all** è un parametro di query che consente di modificare la valutazione dell'operatore NOT in Ricerca di Azure. Per impostazione predefinita, le query che includono NOT (-) espandono anziché limitare i risultati. È possibile impostare questo parametro per modificare la modalità di valutazione dell'operatore. La relativa documentazione è disponibile in [Eseguire ricerche nei documenti](https://msdn.microsoft.com/library/dn798927.aspx) o [SearchMode (enumerazione)](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchmode.aspx).

I **profili di punteggio** vengono utilizzati per migliorare i punteggi di ricerca, consentendo agli elementi che soddisfano i criteri predefiniti di essere visualizzati in alto nei risultati della ricerca. Per eseguire questa funzionalità, vedere [Introduzione ai profili di punteggio](search-get-started-scoring-profiles.md).

I **filtri** vengono utilizzati per limitare i risultati della ricerca, fornendo criteri aggiuntivi per la selezione. Le espressioni di filtro vengono posizionate all'interno della query. Per informazioni dettagliate, vedere [Eseguire ricerche nei documenti](https://msdn.microsoft.com/library/dn798927.aspx).

L’**esplorazione in base al facet** viene utilizzata per i filtri autoindirizzati. Ricerca di Azure crea e restituisce la struttura e il codice esegue il rendering della struttura di esplorazione in base al facet in una pagina di risultati della ricerca. Per informazioni dettagliate, vedere [Esplorazione in base al facet](search-faceted-navigation.md).

I **suggesters** si riferiscono alle query di digitazione o di completamento automatico che restituiscono i termini di ricerca suggeriti mentre l’utente digita i primi caratteri di una frase di ricerca. Per ulteriori informazioni, vedere [Operazione Suggestions](https://msdn.microsoft.com/library/dn798936.aspx) o [Classe Suggesters](https://msdn.microsoft.com/library/microsoft.azure.search.models.suggester.aspx).

Gli **analizzatori di lingua** forniscono le regole linguistiche utilizzate durante l'analisi di testo. L'analizzatore di lingua predefinito per Ricerca di Azure è Lucene Inglese, ma è possibile utilizzare diversi o anche più analizzatori specificandoli nell’indice. Gli analizzatori Lucene sono disponibili in tutte le API. I processori di linguaggio naturale Microsoft sono disponibili solo in [API REST 2015-02-28-Preview](search-api-2015-02-28-preview.md). Per ulteriori informazioni, vedere [Supporto per le lingue](https://msdn.microsoft.com/library/dn879793.aspx).

###Passaggio 6: aggiornare indici e documenti

Alcune delle funzionalità da valutare potrebbero richiedere un aggiornamento dell'indice che spesso comporta la richiesta di aggiornamenti per i documenti.

Se è necessario aggiornare un indice o i documenti, ad esempio per aggiungere suggesters oppure specificare gli analizzatori di lingua nei campi aggiunti a tale scopo, vedere i collegamenti seguenti per le istruzioni:

- [Operazione Update Index (API REST)](https://msdn.microsoft.com/library/dn800964.aspx)
- [Operazione Update Indexer (API REST)](https://msdn.microsoft.com/library/dn946892.aspx)
- [Aggiungere, aggiornare o eliminare documenti (API REST)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Classe Index (libreria .NET)](https://msdn.microsoft.com/library/microsoft.azure.search.models.index.aspx)
- [Classe Documents (libreria .NET)](https://msdn.microsoft.com/library/microsoft.azure.search.models.document.aspx)

Dopo aver creato un prototipo che stabilisce la verifica concettuale, è possibile utilizzare quanto appreso nel prossimo livello per la progettazione di un progetto di sviluppo in grado di supportare i carichi di lavoro.

##Sviluppo di applicazioni

Nella fase successiva è necessario decidere quali API utilizzare, come gestire i documenti e caricare la frequenza e se includere le risorse esterne nei risultati della ricerca.

La progettazione della soluzione comunque richiede tutti i passaggi descritti per i prototipi, ma anziché stabilire la priorità della modalità più vantaggiosa, si considera quale approccio risulta essere più compatibile con la soluzione globale.

###Scegliere un'API

Ricerca di Azure offre due modelli di programmazione: la libreria .NET per il codice gestito e un'API REST per i linguaggi di programmazione, come Java, JavaScript o un altro linguaggio non indirizzato a Microsoft .NET Framework.

Attualmente nella libreria .NET ancora non è disponibile un piccolo subset di funzionalità, pertanto anche se si preferisce scrivere codice gestito, è necessario utilizzare l'API REST per ottenere le funzionalità desiderate. Le funzionalità disponibili solo nell'API REST sono:

- [Processori del linguaggio naturale Microsoft - Solo anteprima](../search-api-2015-02-28-preview/)
- [Funzionalità moreLikeThis - Solo anteprima](../search-api-2015-02-28-preview/)
- [API di gestione](https://msdn.microsoft.com/library/dn832684.aspx)

È possibile controllare periodicamente l’articolo [Novità](search-latest-updates.md) per monitorare le modifiche dello stato della funzionalità.

###Determinare i metodi di sincronizzazione dei dati: push o pull

I modelli push e pull fanno riferimento alla modalità di aggiornamento dei documenti nell'indice. Spesso, lo scenario indica quale sia il modello adatto.

Se si tratta di un’azienda di vendita online, molto probabilmente è necessario un modello push in modo da poter effettuare il push o la doppia scrittura di qualsiasi modifica apportata in magazzino per il database OLTP e l’indice di Ricerca di Azure. Quando viene esaurita una SKU specifica oppure una dimensione o un colore diventa non disponibile, è possibile che l'indice debba essere aggiornato nel minor tempo possibile per evitare la frustrazione dei clienti. Solo i modelli push possono fornire aggiornamenti in tempo quasi reale per l'indice di ricerca.

Non esiste alcun meccanismo specifico in Ricerca di Azure per l'implementazione di un modello push. Il codice dell'applicazione, a livello di dati, deve gestire l'operazione di aggiornamento dei documenti utilizzando l’[API REST](https://msdn.microsoft.com/library/dn798935.aspx) o la [libreria .NET](https://msdn.microsoft.com/library/dn951165.aspx) per aggiornare i documenti nella raccolta. Come dettaglio di implementazione, l’utilizzo di una SKU del prodotto per la chiave del documento può semplificare l’esecuzione di questa attività.

I modelli pull sono solitamente operazioni pianificate che recuperano dati da origini dati esterne. In Ricerca di Azure, un modello pull è disponibile tramite gli [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx), che a loro volta sono disponibili per origini dati specifiche: Azure DocumentDB o Database SQL di Azure e anche SQL Server in macchine virtuali di Azure.

###Caricamento di documenti in batch

È consigliabile aggiungere i documenti in batch per migliorare la velocità effettiva. È possibile creare batch contenenti fino a 1.000 documenti, supponendo che le dimensioni medie di un documento siano di ricerca 1 o 2 KB.

Per la richiesta POST c'è un codice di stato generale. I codici di stato sono HTTP 200 (Success) o HTTP 207 (Multi-Status) se esiste una combinazione di documenti elaborati correttamente o non elaborati. Oltre al codice di stato per la richiesta POST, la Ricerca di Azure prevede un campo di stato per ogni documento. In un caricamento in batch è necessario ottenere lo stato di ogni documento che indichi se l'inserimento è riuscito o no. Il campo di stato fornisce tali informazioni. Sarà impostato su false se non è stato possibile caricare il documento.

Con un carico pesante non è raro riscontrare alcuni errori di caricamento. Qualora ciò dovesse verificarsi, il codice di stato generale è 207, che indica un esito positivo parziale, mentre i documenti per i quali l'indicizzazione ha avuto esito negativo saranno contrassegnati dalla proprietà "status" impostata su false.

> [AZURE.NOTE]Quando il servizio riceve i documenti, questi vengono messi in coda per l'indicizzazione e potrebbero non essere immediatamente inclusi nei risultati della ricerca. Quando non è presente un carico pesante, i documenti vengono generalmente indicizzati entro pochi secondi.

Quando si aggiorna un indice è possibile combinare più azioni (inserimento, unione, eliminazione) nello stesso batch, eliminando la necessità di eseguire più round trip. Attualmente, la Ricerca di Azure non supporta gli aggiornamenti parziali (HTTP PATCH), perciò per aggiornare un indice sarà necessario inviare nuovamente la relativa definizione.

###Integrazione dei dati esterni nei risultati della ricerca

Ricerca di Azure usa le risorse di archiviazione interne per gli indici e i documenti usati nelle operazioni di ricerca. L'analisi dei testi e degli indici dipende dalla disponibilità di tutti i campi per la ricerca e degli attributi associati.

Tuttavia, non tutti i campi di un documento sono disponibili per la ricerca. Ad esempio, se l'applicazione è un catalogo online per musica o video, è consigliabile archiviare i file binari nel servizio BLOB di Azure o in risorse di archiviazione con altri formati. Gli stessi file binari non sono disponibili per la ricerca, quindi non è necessario salvarli in modo permanente nell'archiviazione della Ricerca di Azure. Benché sia opportuno archiviare file immagine, video e audio in altri servizi o percorsi, è consigliabile includere un campo che associ l'URL al percorso del file. In questo modo sarà possibile restituire i dati esterni come parte dei risultati di ricerca.

Per utilizzare dati esterni, è necessario definire un campo in un indice in cui è archiviato un puntatore URL al file di dati esterni. Se si esegue una richiesta [Lookup Documents](https://msdn.microsoft.com/library/dn798929.aspx) o si include il campo nei risultati della ricerca, viene visualizzato il file binario nel contesto di un documento.

###Pianificazione della capacità

Una delle caratteristiche più interessanti di Ricerca di Azure è costituita dalla semplicità con cui è possibile ridurre o aumentare le risorse in risposta alla domanda. Sebbene questa funzionalità non elimini la necessità di pianificazione della capacità, effettivamente riduce al minimo la maggior parte dei rischi. Non ci si deve impegnare con hardware aggiuntivo o hardware errato, per l'esecuzione dei carichi di lavoro di ricerca.

Come ultimo passaggio, esaminare i livelli di risorse esistenti per le repliche e le partizioni e stabilire se sono necessarie modifiche. È il modo più semplice per regolare la capacità nel [portale di gestione di Azure](https://ms.portal.azure.com/).

Si tenga presente che solo il livello di prezzo standard può essere aumentato o diminuito. Inoltre, a seconda del grado di regolazione, la distribuzione dei cluster aggiuntivi per il servizio può richiedere da alcuni minuti a diverse ore.

> [AZURE.NOTE]La capacità può essere modificata a livello di programmazione utilizzando l'API REST di gestione. Per ulteriori informazioni, vedere [API REST di gestione](https://msdn.microsoft.com/library/azure/dn832684.aspx).


<!--Image references-->
[1]: ./media/search-workflow/AzSearch-Workflow.png
 

<!---HONumber=July15_HO3-->