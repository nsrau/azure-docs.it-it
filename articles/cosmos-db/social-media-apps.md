---
title: 'Schema progettuale di Azure Cosmos DB: app di social media | Microsoft Docs'
description: Informazioni su uno schema progettuale per social network che sfrutta la flessibilità di archiviazione di Azure Cosmos DB e altri servizi di Azure.
keywords: app di social media
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: maquaran
ms.openlocfilehash: 6c2911ac65b95ea0a705944fdd8fb9288af28498
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165680"
---
# <a name="going-social-with-azure-cosmos-db"></a>Integrazione con i social con Azure Cosmos DB

Vivere in una società profondamente interconnessa porta, prima o poi, ad avere a che fare con i **social network**. I social network vengono usati per rimanere in contatto con amici, colleghi e familiari, ma anche per condividere passioni con persone con interessi simili.

I tecnici e gli sviluppatori si chiederanno probabilmente come vengono archiviati e interconnessi i dati in queste reti. Come sviluppatori, è anche possibile aver ricevuto richieste per creare o progettare un nuovo social network per un mercato di nicchia specifico. La domanda più importante di tutte è: come vengono archiviati tutti questi dati?

Si supponga di dover creare un nuovo social network, in cui gli utenti possano pubblicare articoli con elementi multimediali correlati, ad esempio immagini, video o musica. Gli utenti possono aggiungere commenti per i post e assegnare punti per le classificazioni. La pagina di destinazione principale del sito Web includerà un feed di post che gli utenti possono visualizzare e con cui possono interagire. Questo esempio potrebbe sembrare piuttosto semplice a prima vista. Si potrebbero approfondire, ad esempio, i feed dell'utente personalizzati influenzati dalle relazioni, ma questi argomenti esulano dagli obiettivi di questo articolo.

Come e dove archiviare i dati?

Si potrebbe avere esperienza di database SQL o conoscere i concetti della [modellazione relazionale dei dati](https://en.wikipedia.org/wiki/Relational_model). Per iniziare, si potrebbe disegnare un diagramma simile al seguente:

![Diagramma che illustra un modello relazionale relativo](./media/social-media-apps/social-media-apps-sql.png)

Una struttura di dati perfettamente normalizzata... che non supporta la scalabilità.

Niente da dire sui database SQL, naturalmente. Sono ottimi, ma come ogni modello, procedura e piattaforma software, non sono perfetti per ogni scenario.

Perché SQL non rappresenta la scelta migliore in questo scenario? Esaminiamo la struttura di un singolo post. Per visualizzare il post in un sito Web o un'applicazione, sarebbe necessario eseguire query e unire in join un gran numero di tabelle per mostrare un singolo post. L'ideale sarebbe invece realizzare un flusso di post che vengono caricati e visualizzati sullo schermo in modo dinamico.

Si potrebbe usare un'enorme istanza di SQL con capacità sufficienti per risolvere migliaia di query con numerosi join per rendere disponibile il contenuto. Ma perché mai scegliere questa opzione quando ne esiste una più semplice?

## <a name="the-nosql-road"></a>Approccio NoSQL

Questo articolo descrive la modellazione dei dati della piattaforma di social networking con il database NoSQL di Azure [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), in modo economicamente conveniente. Sono anche disponibili indicazioni su come usare altre funzionalità di Azure Cosmos DB, come l'[API Gremlin](../cosmos-db/graph-introduction.md). Con un approccio [NoSQL](https://en.wikipedia.org/wiki/NoSQL), che prevede l'archiviazione dei dati in formato JSON e l'applicazione della [denormalizzazione](https://en.wikipedia.org/wiki/Denormalization), il post che prima risultava complesso può essere trasformato in un singolo [documento](https://en.wikipedia.org/wiki/Document-oriented_database):

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

E può essere ottenuto con una singola query, senza join. Questa query è molto più semplice e lineare e, a livello di budget, permette di ottenere risultati migliori con meno risorse.

Azure Cosmos DB garantisce che tutte le proprietà vengano indicizzate tramite l'indicizzazione automatica. L'indicizzazione automatica può essere persino [personalizzata](index-policy.md). L'approccio senza schema consente di archiviare documenti con strutture diverse e dinamiche. Ad esempio, potrebbe risultare necessario associare un elenco di categorie o hashtag ai post in un secondo momento. Cosmos DB gestirà i nuovi documenti con gli attributi aggiuntivi senza richiedere ulteriori interventi.

I commenti per un post possono essere considerati come altri post con una proprietà padre. (Questo approccio semplifica il mapping degli oggetti.)

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

Per la creazione di feed è sufficiente creare documenti che possano contenere un elenco di ID di post con un ordine di pertinenza specifico:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

È possibile usare un flusso "più recenti" con i post ordinati per data di creazione. Oppure è possibile creare un flusso "più interessanti" con i post con il maggior numero di Mi piace nelle ultime 24 ore. Si potrebbe anche implementare un flusso personalizzato per ogni utente basato su logica, ad esempio follower e interessi. Rimarrebbe comunque un elenco di post. Il fulcro è come compilare questi elenchi, ma le prestazioni di lettura rimangono invariate. Dopo l'acquisizione di uno di questi elenchi, viene inviata una singola query a Cosmos DB usando l'[operatore IN](how-to-sql-query.md#WhereClause) per ottenere più pagine di post alla volta.

I flussi di feed possono essere creati usando i processi in background dei [servizi app di Azure](https://azure.microsoft.com/services/app-service/), ovvero [Processi Web](../app-service/web-sites-create-web-jobs.md). Dopo che un post è stato creato, l'elaborazione in background può essere attivata tramite le [code](https://azure.microsoft.com/services/storage/) di [Archiviazione di Azure](../storage/queues/storage-dotnet-how-to-use-queues.md), mentre i Processi Web possono essere attivati tramite [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), implementando la propagazione dei post all'interno dei flussi in base a una logica personalizzata.

Con questa stessa tecnica è possibile elaborare punteggi e Mi piace relativi ai post in modo posticipato, per creare un ambiente coerente.

I follower sono più complessi. Cosmos DB prevede un limite di dimensioni dei documenti e la lettura/scrittura di documenti di grandi dimensioni può influire sulla scalabilità dell'applicazione. È quindi consigliabile archiviare i follower come documento con questa struttura:

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

Questa struttura potrebbe funzionare per un utente con poche migliaia di follower. Nel caso di una celebrità, tuttavia, questo approccio porterebbe a un documento di dimensioni molto grandi con la possibilità di raggiungere il limite massimo.

Per risolvere il problema, è possibile adottare un approccio misto. Nel documento delle statistiche utenti è possibile archiviare il numero di follower:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

È possibile archiviare il grafo effettivo dei follower tramite l'[API Gremlin](../cosmos-db/graph-introduction.md) di Azure Cosmos DB, per creare [vertici](http://mathworld.wolfram.com/GraphVertex.html) per ogni utente e [archi](http://mathworld.wolfram.com/GraphEdge.html) che rappresentano le relazioni "A-segue-B". Con l'API Gremlin, è possibile ottenere i follower di un determinato utente e creare query più complesse per suggerire le persone in comune. Se al grafo si aggiungono le categorie di contenuto che piacciono o a cui sono interessati gli utenti, è possibile avviare esperienze composte che includono l'individuazione intelligente dei contenuti, il suggerimento dei contenuti che interessano ai follower o la ricerca di persone con cui l'utente potrebbe avere molto in comune.

Il documento Statistiche utente può ancora essere usato per creare schede nell'interfaccia utente o anteprime rapide del profilo.

## <a name="the-ladder-pattern-and-data-duplication"></a>Modello "a gradini" e duplicazione dei dati

Si è probabilmente notato che nel documento JSON che fa riferimento a un post sono presenti molte occorrenze di un utente. Questi duplicati significano che, data la denormalizzazione, le informazioni che descrivono un utente potrebbero essere presenti in più posizioni.

Questo perché query più veloci comportano la duplicazione dei dati. Il problema legato a questo effetto collaterale è che, se i dati di un utente vengono modificati in qualche modo, è necessario trovare e aggiornare tutte le attività eseguite da tale utente. Questo è un aspetto che va risolto.

Il problema viene risolto identificando gli attributi chiave di un utente che sono visualizzati nell'applicazione per ogni attività. Se nell'applicazione sono visibili soltanto il post con il nome e l'immagine dell'autore, perché archiviare tutti i dati dell'utente nell'attributo "createdBy"? Se per ogni commento viene visualizzata solo l'immagine dell'utente, il resto delle informazioni dell'utente in effetti non serve. In questo caso entra in gioco il cosiddetto modello "a gradini".

Si prendano ad esempio le informazioni relative a un utente:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Esaminando queste informazioni è possibile distinguere rapidamente quelle più o meno critiche, creando così dei "gradini":

![Diagramma di un modello a gradini](./media/social-media-apps/social-media-apps-ladder.png)

Il gradino più piccolo è detto UserChunk, l'informazione minima che identifica un utente, e viene usato per la duplicazione dei dati. Riducendo le dimensioni dei dati duplicati alle sole informazioni "visualizzate", si riduce il rischio di aggiornamenti troppo estesi.

Il gradino intermedio è quello dell'utente e contiene i dati completi che verranno usati nella maggior parte delle query dipendenti dalle prestazioni in Cosmos DB. Si tratta del gradino più importante e più consultato. Include le informazioni rappresentate da un UserChunk.

Il gradino più grande è quello dell'utente esteso. Include le informazioni cruciali degli utenti e altri dati che non è necessario leggere rapidamente o che hanno un eventuale utilizzo, come il processo di accesso. Questi dati possono essere archiviati al di fuori di Cosmos DB, nel database SQL di Azure o nelle tabelle di archiviazione di Azure.

Suddividere i dati dell'utente e archiviare le informazioni in posizioni diverse risulta utile perché, in termini di prestazioni, le query su documenti di grandi dimensioni hanno un costo maggiore. Mantenere snelli i documenti, con le informazioni essenziali per eseguire tutte le query dipendenti dalle prestazioni per il social network. Archiviare le altre informazioni aggiuntive per possibili scenari come modifiche del profilo completo, accessi, data mining per l'analisi di utilizzo e iniziative legate ai Big Data. Non è in effetti importante se la raccolta dati per il data mining risulta più lenta, perché è in esecuzione in un database SQL di Azure. Quello che conta è che gli utenti abbiano un'esperienza agile e veloce. Il codice per un utente archiviato in Cosmos DB si presenta come segue:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
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

Quando subentra una modifica che interessa un attributo di un blocco, è possibile trovare facilmente i documenti interessati. È sufficiente usare query che fanno riferimento agli attributi indicizzati, ad esempio `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`, quindi aggiornare i blocchi.

## <a name="the-search-box"></a>Casella di ricerca

Gli utenti generano molto contenuto e devono avere la possibilità di cercare e trovare anche contenuti non presenti direttamente nel proprio flusso di contenuti, perché non seguono gli autori o semplicemente perché si tratta di post vecchi di sei mesi.

Poiché si usa Azure Cosmos DB, è possibile implementare facilmente un motore di ricerca tramite [Ricerca di Azure](https://azure.microsoft.com/services/search/) in pochi minuti senza digitare codice, se non per il processo di ricerca e l'interfaccia utente.

Perché questo processo è così semplice?

Ricerca di Azure implementa i cosiddetti [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx), ovvero processi in background che agganciano i repository di dati e aggiungono, aggiornano e rimuovono automaticamente gli oggetti contenuti negli indici. Supportano gli [indicizzatori di database SQL di Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), gli [indicizzatori di BLOB di Azure](../search/search-howto-indexing-azure-blob-storage.md) e, soprattutto, gli [indicizzatori di Azure Cosmos DB](../search/search-howto-index-documentdb.md). La transizione delle informazioni da Cosmos DB a Ricerca di Azure è semplice. Entrambe le tecnologie archiviano le informazioni in formato JSON, pertanto è sufficiente [creare l'indice](../search/search-create-index-portal.md) ed eseguire il mapping degli attributi dai documenti da indicizzare. La procedura è terminata. A seconda delle dimensioni dei dati, tutto il contenuto sarà disponibile per la ricerca entro pochi minuti dalla soluzione di ricerca distribuita come servizio migliore nell'infrastruttura cloud.

Per altre informazioni su Ricerca di Azure, vedere la [guida a Ricerca di Azure](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)nel relativo post di blog.

## <a name="the-underlying-knowledge"></a>Conoscenza sottostante

Dopo aver archiviato tutti questi contenuti che continuano ad aumentare, come è possibile mettere tutto questo flusso di informazioni al servizio degli utenti?

È necessario usare le informazioni e imparare dai dati.

Ad esempio, è possibile usare l'[analisi del sentimento](https://en.wikipedia.org/wiki/Sentiment_analysis), includere indicazioni su contenuti consigliati in base alle preferenze dell'utente oppure un Content Moderator automatizzato che garantisca la sicurezza per la famiglia di tutti i contenuti pubblicati dal social network.

Contrariamente a quanto si potrebbe pensare, non è necessario essere dei matematici per estrapolare questi modelli e informazioni da semplici file e database.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), incluso in[ Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), è un servizio cloud completamente gestito che consente di creare flussi di lavoro tramite algoritmi in una semplice interfaccia basata sul trascinamento. È possibile codificare algoritmi personalizzati in [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) o usare alcune delle API già compilate e pronte per l'uso, come [Analisi del testo](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), Content Moderator o [Consigli](https://gallery.azure.ai/Solution/Recommendations-Solution).

Per realizzare uno di questi scenari di Machine Learning, è possibile usare [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) per inserire le informazioni provenienti da origini diverse. È anche possibile usare [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) per elaborare le informazioni e generare un output che può essere elaborato da Azure Machine Learning.

Un'altra opzione disponibile consiste nell'usare [Servizi cognitivi di Azure](https://www.microsoft.com/cognitive-services) per analizzare i contenuti degli utenti. Non solo è possibile comprenderli meglio (tramite l'analisi di ciò che gli utenti scrivono con l'[API Analisi del testo di Azure Machine Learning](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ma è anche possibile rilevare i contenuti indesiderati o per soli adulti e agire di conseguenza tramite l'[API Visione artificiale](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). I servizi cognitivi includono molte delle soluzioni pronte all'uso che non richiedono alcuna conoscenza pregressa di Machine Learning.

## <a name="a-planet-scale-social-experience"></a>Un'esperienza social su scala globale

C'è un ultimo, ma non meno importante, articolo da affrontare: la **scalabilità**. Quando si progetta un'architettura, ogni componente deve poter essere ridimensionato in modo autonomo. Potrebbe diventare necessario elaborare più dati o ottenere una copertura geografica più ampia. Per fortuna, entrambe le attività sono un'**esperienza chiavi in mano** con Cosmos DB.

Cosmos DB supporta il [partizionamento dinamico](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) per impostazione predefinita e crea automaticamente partizioni in base a una determinata **chiave di partizione**, definita come attributo nei documenti. La definizione della chiave di partizione corretta deve essere eseguita in fase di progettazione. Per altre informazioni, vedere l'articolo [Scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey).

Per un'esperienza social, è necessario allineare la strategia di partizionamento al modo in cui si eseguono query e scrittura. (Ad esempio, le letture all'interno della stessa partizione sono consigliabili ed è possibile evitare "hot spot" distribuendo le scritture su più partizioni.) Alcune opzioni sono: partizioni basate su una chiave temporale (giorno/mese/settimana), sulla categoria del contenuto, sull'area geografica o sull'utente. Tutto dipende da come verranno eseguite le query sui dati e visualizzati i dati nell'esperienza social.

Cosmos DB eseguirà le query (incluse le [aggregazioni](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) in tutte le partizioni in modo trasparente, senza dover quindi aggiungere logica con l'aumento dei dati.

Con il tempo, aumenterà il traffico e di conseguenza aumenterà il consumo di risorse (misurato in [UR](request-units.md) o unità richiesta). Le letture e le scritture saranno più frequenti man mano che cresce la base utenti. La base utenti inizierà a creare e a leggere più contenuto. Pertanto, la possibilità di **aumentare le unità richieste** è fondamentale. Aumentare le unità richieste è facile. È possibile farlo con pochi clic nel portale di Azure o [inviando i comandi tramite l'API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Aumento delle prestazioni e definizione di una chiave di partizione](./media/social-media-apps/social-media-apps-scaling.png)

Cosa accade se la piattaforma ha sempre più successo? Si supponga che gli utenti di un'area geografica, un paese o un continente diverso notino la piattaforma e inizino a usarla. È senz'altro una buona notizia,

ma presto ci si rende conto che l'esperienza con la piattaforma non è ottimale. Si tratta di utenti così lontani dall'area in cui si opera che la latenza è terribile. Ed è naturalmente un peccato perdere questi utenti. Ci vorrebbe un modo semplice per **estendere la portata globale** e questo modo esiste.

Cosmos DB consente di [replicare i dati a livello globale](../cosmos-db/tutorial-global-distribution-sql-api.md) e in modo trasparente con un paio di clic e di scegliere automaticamente tra le aree disponibili del [codice client](../cosmos-db/tutorial-global-distribution-sql-api.md). Questo processo significa anche che è possibile avere [più aree di failover](high-availability.md).

Quando si replicano i dati a livello globale, è necessario assicurarsi che i client possano sfruttarli. Se si usa un front-end Web o si accede alle API da client mobili, è possibile distribuire [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/) e clonare il Servizio app di Azure in tutte le aree desiderate, usando una configurazione delle prestazioni per supportare la copertura globale estesa. Quando i client accedono al front-end o alle API, verranno indirizzati al servizio app più vicino, che a sua volta si connetterà alla replica di Cosmos DB locale.

![Aggiunta della copertura globale alla piattaforma social](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Conclusioni

Questo articolo mette in luce alcune alternative per la creazione di social network interamente in Azure con servizi a costo contenuto. Viene promosso l'uso di una soluzione di archiviazione e distribuzione dei dati su più livelli detta "a gradini".

![Diagramma di interazione tra servizi di Azure per il social networking](./media/social-media-apps/social-media-apps-azure-solution.png)

La verità è che non esiste un'unica soluzione infallibile per questo tipo di scenari. È la sinergia creata dall'unione di servizi di alto livello che porta a grandi risultati: la velocità e la libertà di Azure Cosmos DB che rendono possibile la realizzazione di un'applicazione social ottimale, l'intelligenza di una soluzione di ricerca di alto livello come Ricerca di Azure, la flessibilità dei servizi app di Azure che consentono di ospitare non solo applicazioni indipendenti dal linguaggio, ma anche potenti processi in background, l'espandibilità di Archiviazione di Azure e del database SQL di Azure che permettono di archiviare grandi quantità di dati e la potenza delle analisi di Azure Machine Learning che consente di creare conoscenza e informazioni per migliorare i processi e fornire i contenuti più appropriati agli utenti giusti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui casi d'uso per Cosmos DB, vedere [Casi d'uso comuni di Cosmos DB](use-cases.md).
