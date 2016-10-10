<properties 
	pageTitle="Schema progettuale di DocumentDB: app di social media | Microsoft Azure" 
	description="Informazioni su uno schema progettuale per social network che sfrutta la flessibilità di archiviazione di DocumentDB e altri servizi di Azure." 
	keywords="app di social media"
	services="documentdb" 
	authors="ealsur" 
	manager="" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/27/2016" 
	ms.author="mimig"/>

# Uso di DocumentDB per i social media

Vivere in una società profondamente interconnessa porta, prima o poi, ad avere a che fare con i **social network**. I social network vengono usati per rimanere in contatto con amici, colleghi e familiari, ma anche per condividere passioni con persone con interessi simili.

Ingegneri e sviluppatori si sono probabilmente interrogati sulle modalità di archiviazione e interconnessione dei dati in queste reti. Altri potrebbero aver ricevuto l'incarico di creare o progettare un nuovo social network per uno specifico mercato di nicchia. La domanda più importante di tutte è: come vengono archiviati tutti questi dati?

Si supponga di voler creare un nuovo social network, in cui gli utenti possano pubblicare articoli con elementi multimediali correlati, ad esempio immagini, video o musica, e possano commentare i post e valutarli assegnando un punteggio. La pagina di destinazione principale del sito Web includerà un feed di post che gli utenti possono visualizzare e con cui possono interagire. Inizialmente l'esempio può non sembrare molto complesso. Si potrebbe approfondire l'argomento con feed utente personalizzati condizionati dalle relazioni, ma questo va oltre l'obiettivo di questo articolo.

Come e dove archiviare i dati?

Chi ha fatto esperienza di database SQL o conosce la [modellazione relazionale dei dati](https://en.wikipedia.org/wiki/Relational_model) potrebbe iniziare con il disegnare qualcosa di simile:

![Diagramma che illustra un modello relazionale relativo](./media/documentdb-social-media-apps/social-media-apps-sql.png)

Una struttura di dati perfettamente normalizzata, che però non supporta la scalabilità.

I database SQL sono certamente molto utili, ma come ogni modello, procedura e piattaforma software non sono perfetti per tutti gli scenari.

Perché SQL non rappresenta la scelta migliore in questo scenario? A causa della struttura usata, per visualizzare un singolo post in un sito Web o un'applicazione sarebbe necessario eseguire una query con addirittura otto join di tabella. Per un flusso di post caricati in modo dinamico e visualizzati sullo schermo questa struttura risulta quindi poco adatta.

Si potrebbe usare un'istanza di SQL di grandissime dimensioni, con capacità sufficiente per risolvere migliaia di query con un numero di join adatto a rendere disponibile il contenuto, ma esiste una soluzione più semplice.

## Approccio NoSQL

Esistono speciali database a grafo [eseguibili in Azure](http://neo4j.com/developer/guide-cloud-deployment/#_windows_azure), ma si tratta di una soluzione costosa che richiede manutenzione e l'uso di servizi IaaS (Infrastructure-as-a-Service), soprattutto macchine virtuali. Questo articolo intende proporre una soluzione a costo inferiore compatibile con la maggior parte degli scenari che può essere eseguita nel database NoSQL di Azure, ovvero [DocumentDB](https://azure.microsoft.com/services/documentdb/). Con un approccio [NoSQL](https://en.wikipedia.org/wiki/NoSQL), che prevede l'archiviazione dei dati in formato JSON e l'applicazione della [denormalizzazione](https://en.wikipedia.org/wiki/Denormalization), il post che prima risultava complesso può essere trasformato in un singolo [documento](https://en.wikipedia.org/wiki/Document-oriented_database):

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

E può essere ottenuto con una singola query, senza l'uso di join. Questa soluzione è molto più semplice e lineare e, a livello di budget, permette di ottenere risultati migliori con meno risorse.

Con Azure DocumentDB tutte le proprietà vengono indicizzate tramite l'[indicizzazione automatica](documentdb-indexing.md), che può anche essere [personalizzata](documentdb-indexing-policies.md). L'approccio senza schema consente di archiviare documenti con strutture varie e dinamiche. Se in futuro si volesse associare un elenco di categorie o hashtag ai post, DocumentDB gestirebbe i nuovi documenti con gli attributi aggiuntivi senza richiedere ulteriori interventi.

I commenti a un post possono essere considerati come altri post con una proprietà padre e questo semplifica il mapping degli oggetti.

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Tutte le interazioni social possono essere archiviate in un oggetto separato come contatori:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Per la creazione di feed è sufficiente creare documenti che possano contenere un elenco di ID post con un ordine di pertinenza specifico:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Si potrebbe avere un flusso delle notizie "più recenti", con i post ordinati per data di creazione, un flusso delle notizie "più interessanti", con i post più popolari delle ultime 24 ore, e perfino un flusso personalizzato per ogni utente, in base ai follower oppure agli interessi, ma rimarrebbe comunque un elenco di post. Dopo aver compilato gli elenchi, le prestazioni di lettura rimangono invariate. Dopo l'acquisizione di uno di questi elenchi, viene inviata una singola query a DocumentDB usando l'[operatore IN](documentdb-sql-query.md#where-clause) per ottenere più pagine di post alla volta.

I flussi di feed possono essere compilati usando i processi in background dei [servizi app di Azure](https://azure.microsoft.com/services/app-service/), ovvero [Processi Web](../app-service-web/web-sites-create-web-jobs.md). Dopo aver creato un post, l'elaborazione in background può essere attivata tramite le [code](../storage/storage-dotnet-how-to-use-queues.md) di [Archiviazione di Azure](https://azure.microsoft.com/services/storage/), mentre i Processi Web possono essere attivati tramite [Azure Webjobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md), implementando la propagazione dei post all'interno dei flussi in base a una logica personalizzata.

Con questa stessa tecnica è possibile elaborare punteggi e Mi piace relativi ai post in modo posticipato, per creare un ambiente coerente.

I follower sono più complessi. Il limite per la dimensione dei documenti in DocumentDB è di 512 Kb e ciò consente di memorizzare i follower come un documento con la struttura seguente:

    {
    	"id":"234d-sd23-rrf2-552d",
    	"followersOf": "dse4-qwe2-ert4-aad2",
    	"followers":[
    		"ewr5-232d-tyrg-iuo2",
    		"qejh-2345-sdf1-ytg5",
    		//...
    		"uie0-4tyg-3456-rwjh"
    	]
    }

Questa soluzione può funzionare per un utente con alcune migliaia di follower, ma qualora si unissero alcune celebrità, il limite massimo verrebbe raggiunto rapidamente.

Per risolvere il problema, è possibile adottare un approccio misto. Nel documento delle statistiche utenti è possibile archiviare il numero di follower:

    {
    	"id":"234d-sd23-rrf2-552d",
    	"user": "dse4-qwe2-ert4-aad2",
    	"followers":55230,
    	"totalPosts":452,
    	"totalPoints":11342
    }

Il grafico effettivo dei follower può essere archiviato nelle tabelle di archiviazione di Azure tramite un'[estensione](https://github.com/richorama/AzureStorageExtensions#azuregraphstore) che consente semplicemente l'archiviazione e il recupero dello schema "A-segue-B". In questo modo è possibile delegare il processo di recupero dell'elenco di follower esatto (quando è necessario) alle tabelle di archiviazione di Azure, mentre per una rapida ricerca sui numeri è possibile continuare a usare DocumentDB.

## Modello "a gradini" e duplicazione dei dati

Nel documento JSON che fa riferimento a un post sono presenti più occorrenze di un utente. Ciò significa che, data la denormalizzazione, le informazioni che rappresentano un utente potrebbero essere presenti in più posizioni.

Questo perché query più veloci comportano la duplicazione dei dati. Il problema legato a questo effetto collaterale è che, se i dati di un utente vengono modificati in qualche modo, è necessario trovare e aggiornare tutte le attività eseguite da tale utente. Questo è un aspetto che va risolto.

I database a grafo risolvono il problema in un modo particolare. In questo caso, invece, il problema viene risolto identificando gli attributi chiave di un utente che sono visualizzati nell'applicazione per ogni attività. Se nell'applicazione sono visibili soltanto il post con il nome e l'immagine dell'autore, perché archiviare tutti i dati dell'utente nell'attributo "createdBy"? Se per ogni commento viene visualizzata solo l'immagine dell'utente, il resto delle informazioni non serve. In questo caso può risultare utile il cosiddetto modello "a gradini".

Si prendano ad esempio le informazioni relative a un utente:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }
    
Esaminando queste informazioni è possibile distinguere rapidamente quelle più o meno critiche, creando così dei "gradini":

![Diagramma di un modello a gradini](./media/documentdb-social-media-apps/social-media-apps-ladder.png)

Il gradino più piccolo è detto UserChunk, l'informazione minima che identifica un utente, e viene usato per la duplicazione dei dati. Riducendo le dimensioni dei dati duplicati alle sole informazioni visualizzate, si riduce il rischio di aggiornamenti troppo estesi.

Il gradino intermedio è quello dell'utente e contiene i dati completi che verranno usati nella maggior parte delle query basate sulle prestazioni in DocumentDB. Si tratta del gradino più importante e più consultato. Include le informazioni rappresentate da un UserChunk.

Il gradino più grande è quello dell'utente esteso. Include tutte le informazioni critiche relative all'utente e altri dati che non è necessario leggere rapidamente o che vengono usati poco di frequente, ad esempio la procedura di accesso. Questi dati possono essere archiviati al di fuori di DocumentDB, nel database SQL di Azure o nelle tabelle di archiviazione di Azure.

Suddividere i dati dell'utente e archiviare le informazioni in posizioni diverse risulta utile perché lo spazio di archiviazione in DocumentDB [non è infinito](documentdb-limits.md) e perché, in termini di prestazioni, le query su documenti di grandi dimensioni hanno un costo maggiore. I documenti devono quindi essere leggeri e contenere le informazioni necessarie per eseguire query basate sulle prestazioni per un social network. È consigliabile archiviare le informazioni aggiuntive per altri scenari, come ad esempio le modifiche all'intero profilo, gli account di accesso e il data mining per l'analisi di utilizzo e le iniziative legate ai Big Data. Non importa se la raccolta dei dati per il data mining risulta lenta, perché è in esecuzione in un database SQL di Azure. Ciò che conta è offrire un'esperienza utente agile e veloce. Un utente archiviato in DocumentDB si presenta come segue:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

Un post invece si presenta come segue:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
        	"id":"dse4-qwe2-ert4-aad2",
    		"username":"johndoe"
        }
    }

Quando viene apportata una modifica che influisce su uno degli attributi del blocco, è facile trovare i documenti interessati usando query che puntano agli attributi indicizzati (SELECT * FROM posts p WHERE p.createdBy.id == "edited\_user\_id") e quindi aggiornare i blocchi.

## Casella di ricerca

Gli utenti generano molti contenuti e devono avere la possibilità di cercare e trovare anche contenuti non presenti direttamente nel proprio flusso di contenuti, perché non seguono gli autori o semplicemente perché si tratta di post vecchi di 6 mesi.

Grazie ad Azure DocumentDB è possibile implementare facilmente un motore di ricerca tramite [Ricerca di Azure](https://azure.microsoft.com/services/search/), in breve tempo e senza dover aggiungere altro codice oltre al processo di ricerca e all'interfaccia utente.

Perché è così semplice?

Ricerca di Azure implementa i cosiddetti [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx), ovvero processi in background che agganciano i repository di dati e aggiungono, aggiornano e rimuovono automaticamente gli oggetti contenuti negli indici. Supporta gli [indicizzatori di database SQL di Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), gli [indicizzatori di BLOB di Azure](../search/search-howto-indexing-azure-blob-storage.md) e, soprattutto, gli [indicizzatori di Azure DocumentDB](../documentdb/documentdb-search-indexer.md). Il passaggio delle informazioni da DocumentDB a Ricerca di Azure è semplice, perché entrambi archiviano le informazioni in formato JSON. È sufficiente [creare l'indice](../search/search-create-index-portal.md) ed eseguire il mapping degli attributi dei documenti da indicizzare. In pochi minuti, a seconda delle dimensioni dei dati, tutto il contenuto sarà disponibile per la ricerca, con la migliore soluzione di ricerca distribuita come servizio nell'infrastruttura cloud.

Per altre informazioni su Ricerca di Azure, vedere la [guida a Ricerca di Azure](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/) nel relativo post di blog.

## Conoscenza sottostante

Dopo aver archiviato tutti questi contenuti che continuano ad aumentare, come è possibile mettere tutto questo flusso di informazioni al servizio degli utenti?

È necessario usare le informazioni e imparare dai dati.

Ad esempio, è possibile usare l'[analisi dei sentimenti](https://en.wikipedia.org/wiki/Sentiment_analysis), includere indicazioni su contenuti consigliati in base alle preferenze dell'utente oppure un moderatore di contenuti automatizzato che garantisca la sicurezza per la famiglia di tutti i contenuti pubblicati dal social network.

Contrariamente a quanto si potrebbe pensare, non è necessario essere dei matematici per estrapolare questi modelli e informazioni da semplici file e database.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), incluso in [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), è un servizio cloud completamente gestito che consente di creare flussi di lavoro tramite algoritmi in una semplice interfaccia basata sul trascinamento. È possibile codificare algoritmi personalizzati in [R](https://en.wikipedia.org/wiki/R_(programming_language)) o usare alcune delle API già compilate e pronte per l'uso, come [Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator](https://www.microsoft.com/moderator) o [Recommendations](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Per ottenere uno di questi scenari di Machine Learning, è possibile usare [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) per inserire le informazioni provenienti da origini diverse e [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) per elaborare le informazioni e generare output che possono poi essere elaborati da Azure Machine Learning.

Un'altra opzione disponibile consiste nell'usare i [servizi cognitivi Microsoft](https://www.microsoft.com/cognitive-services) per analizzare i contenuti per gli utenti; non solo è possibile ottenerne una migliore comprensione (tramite l'analisi di ciò che gli utenti scrivono l'[API di analisi del testo](https://www.microsoft.com/cognitive-services/it-IT/text-analytics-api)), ma è anche possibile rilevare i contenuto indesiderati o per soli adulti e agire di conseguenza per mezzo dell'[API Visione artificiale](https://www.microsoft.com/cognitive-services/it-IT/computer-vision-api). I servizi cognitivi includono molte delle soluzioni pronte all'uso che non richiedono alcuna conoscenza pregressa di Machine Learning.

## Conclusioni

Questo articolo illustra come creare social network interamente in Azure, con servizi a costo contenuto e ottimi risultati, promuovendo l'uso di una distribuzione dei dati e di una soluzione di archiviazione a più livelli detta "a gradini".

![Diagramma di interazione tra servizi di Azure per il social networking](./media/documentdb-social-media-apps/social-media-apps-azure-solution.png)

Non esiste un metodo infallibile per questo tipo di scenario. È la sinergia creata dall'unione di servizi di alto livello che porta a grandi risultati. La velocità e la libertà di Azure DocumentDB che permettono di realizzare un'applicazione social ottimale, l'intelligenza di una soluzione di ricerca di alto livello come Ricerca di Azure, la flessibilità dei servizi app di Azure che permettono di ospitare non applicazioni indipendenti dal linguaggio ma potenti processi in background, l'espandibilità di Archiviazione di Azure e del database SQL di Azure che permettono di archiviare grandi quantità di dati e la potenza delle analisi di Azure Machine Learning che permette di creare conoscenza e informazioni per migliorare i processi e fornire i contenuti più appropriati agli utenti giusti.

## Passaggi successivi

Per altre informazioni sulla modellazione di dati, vedere [Modellazione dei dati in DocumentDB](documentdb-modeling-data.md). Per altri casi d'uso di DocumentDB, vedere [Casi di uso comuni di DocumentDB](documentdb-use-cases.md).

Per altre informazioni su DocumentDB, seguire il [percorso di apprendimento per DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/).

<!---HONumber=AcomDC_0928_2016-->