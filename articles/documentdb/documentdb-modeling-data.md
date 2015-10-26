<properties 
	pageTitle="Modellazione dei dati in Azure DocumentDB | Microsoft Azure" 
	description="Informazioni su come modellare i dati per un database di documenti NoSQL come Azure DocumentDB." 
	services="documentdb" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="mimig1" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2015" 
	ms.author="ryancraw"/>

#Modellazione dei dati in DocumentDB#
Anche se con i database privi di schema, come DocumentDB, è facilissimo accettare le modifiche apportate al modello di dati, è consigliabile valutare bene tutto ciò che riguarda i dati.

Come verranno archiviati i dati? In che modo l'applicazione recupererà i dati e ne eseguirà la query? L'applicazione esegue un'intensa attività di lettura o un'intensa attività di scrittura?

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

- Come si deve considerare un documento in un database di documenti?
- Cos'è la modellazione dei dati e perché è importante? 
- Come si distingue la modellazione dei dati in un database di documenti da quella in un database relazionale?
- Come si esprimono le relazioni tra i dati in un database non relazionale?
- Quando si incorporano i dati e quando si creano i collegamenti ai dati?

##Incorporamento dei dati##
Quando si inizia a modellare i dati in un archivio documenti, ad esempio DocumentDB, cercare di considerare le entità come **documenti autosufficienti** rappresentati in JSON.

Prima di proseguire, è meglio fare un passo indietro e pensare a come sia possibile modellare qualcosa in un database relazionale, un argomento con cui molti hanno già familiarità. L'esempio seguente mostra come una persona possa essere archiviata in un database relazionale.

![Database relazionale](./media/documentdb-modeling-data/relational-data-model.png)

Quando si lavora con i database relazionali, tutti hanno imparato ormai da anni a normalizzare, normalizzare, normalizzare.

Di solito nella normalizzazione dei dati si prende un'entità, ad esempio una persona, e la si suddivide in porzioni di dati distinte. Nell'esempio precedente, una persona può avere più record di dettagli contatto e più record di indirizzi. È possibile andare oltre e suddividere i dettagli contatto estraendo anche i campi comuni come tipo. Lo stesso per l'indirizzo: ogni record qui ha un tipo, ad esempio *Abitazione* o *Ufficio*

Il presupposto principale quando si normalizzano i dati è **evitare di archiviare i dati ridondanti** in ogni record e fare invece riferimento ai dati. In questo esempio, per leggere una persona, con tutti i dettagli contatto e gli indirizzi, è necessario usare i JOIN per aggregare in modo efficace i dati in fase di esecuzione.

	SELECT p.FirstName, p.LastName, a.City, cd.Detail
	FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Per aggiornare una singola persona con i dettagli contatto e gli indirizzi, è necessario eseguire operazioni di scrittura in più tabelle.

Ora si esaminerà come si modellano gli stessi dati come entità autosufficiente in un database di documenti.
		
	{
	    "id": "1",
	    "firstName": "Thomas",
	    "lastName": "Andersen",
	    "addresses": [
	        {            
	            "line1": "100 Some Street",
	            "line2": "Unit 1",
	            "city": "Seattle",
	            "state": "WA",
	            "zip": 98012
	        }
	    ],
	    "contactDetails": [
	        {"email: "thomas@andersen.com"},
	        {"phone": "+1 555 555-5555", "extension": 5555}
	    ] 
	}

Con questo approccio ora è stato **denormalizzato** il record della persona, in cui sono state **incorporate** tutte le informazioni relative a questa persona, ad esempio i dettagli contatto e gli indirizzi, in un singolo documento JSON. Inoltre, dal momento che non esistono limiti imposti da uno schema fisso, abbiamo la flessibilità necessaria, ad esempio, per avere dettagli contatto di forme completamente diverse.

Il recupero del record completo di una persona dal database richiede ora una sola operazione di lettura in una sola raccolta e per un solo documento. Anche l'aggiornamento del record di una persona, con i dettagli contatto e gli indirizzi, richiede una sola operazione di scrittura in un solo documento.

Denormalizzando i dati, è possibile che l'applicazione debba eseguire meno query e aggiornamenti per completare le comuni operazioni.

###Quando eseguire l'incorporamento

In generale, usare i modelli di dati incorporati quando:

- - Esistono relazioni **contains** tra le entità.
- - Esistono relazioni **one-to-few** tra le entità.
- - Esistono dati incorporati che **cambiano raramente**.
- - Esistono dati incorporati che non aumenteranno **senza limiti**.
- - Esistono dati incorporati che sono parte **integrante** dei dati in un documento.

> [AZURE.NOTE]I modelli di dati denormalizzati garantiscono di solito prestazioni di **lettura** più elevate.

###Quando non eseguire l'incorporamento

Anche se in linea generale in un database di documenti si denormalizza tutto e si incorporano tutti i dati in un solo documento, questo può creare situazioni che sarebbe meglio evitare.

Consideriamo questo frammento JSON.

	{
		"id": "1",
		"name": "What's new in the coolest Cloud",
		"summary": "A blog post by someone real famous",
		"comments": [
			{"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
			{"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
			…
			{"id": 100001, "author": "jane", "comment": "and on we go ..."},
			…
			{"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
			…
			{"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
		]
	}

Questo potrebbe essere l'aspetto di un'entità post con commenti incorporati, se si modellasse un tipico sistema di blog, o CMS. Il problema di questo esempio è che la matrice di commenti non è **limitata**, vale a dire che non esiste in pratica un limite al numero di commenti per i singoli post. Questo sarà un problema qualora le dimensioni del documento aumentassero considerevolmente.

> [AZURE.TIP]I documenti in DocumentDB hanno una dimensione massima. Per altre informazioni su questo argomento, fare riferimento a [Limiti di DocumentDB](documentdb-limits.md).

Quando le dimensioni del documento aumentano, diventa più difficile trasmettere i dati nella rete come anche leggere e aggiornare il documento.

In questo caso, sarebbe meglio prendere in considerazione il modello seguente.
		
	Post document:
	{
		"id": 1,
		"name": "What's new in the coolest Cloud",
		"summary": "A blog post by someone real famous",
		"recentComments": [
			{"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
			{"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
			{"id": 3, "author": "jane", "comment": "....."}
		]
	}

	Comment documents:
	{
		"postId": 1
		"comments": [
			{"id": 4, "author": "anon", "comment": "more goodness"},
			{"id": 5, "author": "bob", "comment": "tails from the field"},
			...
			{"id": 99, "author": "angry", "comment": "blah angry blah angry"}
		]
	},
	{
		"postId": 1
		"comments": [
			{"id": 100, "author": "anon", "comment": "yet more"},
			...
			{"id": 199, "author": "bored", "comment": "will this ever end?"}
		]
	}

Questo modello ha i tre commenti più recenti incorporati nel post, che questa volta è una matrice con un limite fisso. Gli altri commenti sono raggruppati in batch di 100 commenti e archiviati in documenti separati. La dimensione scelta per il batch è 100 perché l'applicazione fittizia consente all'utente di caricare 100 commenti per volta.

Un altro caso in cui non è consigliabile ricorrere all'incorporamento dei dati è quando i dati incorporati vengono usati spesso nei documenti e cambiano spesso.

Consideriamo questo frammento JSON.

	{
	    "id": "1",
	    "firstName": "Thomas",
	    "lastName": "Andersen",
	    "holdings": [
	        {
	            "numberHeld": 100,
	            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
	        },
	        {
	            "numberHeld": 50,
	            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
	        }
	    ]
	}

Questo potrebbe essere il portafoglio azionario di una persona. Abbiamo scelto di incorporare le informazioni sulle azioni in ogni documento del portafoglio. In un ambiente in cui i dati correlati cambieranno spesso, come in un'applicazione per le contrattazioni azionarie, l'incorporamento di dati che cambiano spesso obbligherà ad aggiornare continuamente ogni documento del portafoglio ogni volta che viene scambiata un'azione.

Il titolo *zaza* potrebbe essere scambiato diverse centinaia di volte in un solo giorno e migliaia di utenti potrebbero avere *zaza* nel portafoglio. Con un modello di dati come quello sopra sarebbe necessario aggiornare diverse migliaia di documenti del portfolio più volte al giorno e la scalabilità del sistema non sarebbe efficiente.

##<a id="Refer"></a>Dati di riferimento##

L'incorporamento dei dati quindi funziona senza problemi in molti casi, ma è evidente che in altri scenari la denormalizzazione dei dati è più che altro causa di problemi. Cosa si può fare dunque?

Le relazioni tra entità non devono essere necessariamente create in un database relazionale. In un database di documenti è possibile tenere in un documento informazioni che sono effettivamente correlate ai dati in altri documenti. Questo non è assolutamente un consiglio a creare sistemi che sarebbero più appropriati per un database relazionale in DocumentDB o in qualsiasi altro database di documenti, tuttavia le relazioni semplici vanno bene e possono essere molto utili.

Nel codice JSON seguente abbiamo scelto di usare l'esempio del portafoglio di azioni di prima, ma questa volta facciamo riferimento all'elemento titolo nel portafoglio invece di incorporarlo. In questo modo, anche se l'elemento titolo cambia più volte nel corso della giornata, il solo documento da aggiornare è il documento del titolo.

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }
	
    Stock documents:
    {
        "id": 1,
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": 2,
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }
    

L'aspetto negativo di questo approccio diventa però immediatamente evidente se l'applicazione deve mostrare informazioni su ogni titolo disponibile quando si visualizza il portafoglio di una persona. In questo caso, sarebbe necessario accedere più volte al database per caricare le informazioni del documento di ogni titolo. Qui è stata presa la decisione di aumentare l'efficienza delle operazioni di scrittura, che vengono eseguite spesso durante la giornata, compromettendo però le operazioni di lettura che hanno un impatto potenzialmente minore sulle prestazioni di questo particolare sistema.

> [AZURE.NOTE]I modelli di dati normalizzati **possono richiedere più round trip** al server.

### Chiavi esterne
Poiché attualmente non esiste alcun vincolo, chiave esterna o altro, le relazioni esistenti tra i documenti sono di fatto "collegamenti deboli" e non verranno verificate dal database. Per essere certi che i dati a cui un documento fa riferimento esistano davvero, è necessario operare nell'applicazione oppure tramite trigger lato server o stored procedure in DocumentDB.

###Quando fare riferimento
In generale, usare i modelli di dati denormalizzati quando:

- Si rappresentano relazioni **uno a molti**.
- Si rappresentano relazioni **molti a molti**.
- I dati correlati **cambiano spesso**.
- È possibile che i dati a cui si fa riferimento **non siamo limitati**.

> [AZURE.NOTE]La normalizzazione offre di solito migliori prestazioni di **scrittura**.

###Dove inserire le relazioni
La crescita della relazione aiuterà a determinare in quale documento archiviare il riferimento.

Il codice JSON seguente modella editori e libri.

	Publisher document:
	{
	    "id": "mspress",
	    "name": "Microsoft Press",
	    "books": [ 1, 2, 3, ..., 100, ..., 1000]
	}

	Book documents:
	{"id": 1, "name": "DocumentDB 101" }
	{"id": 2, "name": "DocumentDB for RDBMS Users" }
	{"id": 3, "name": "Taking over the world one JSON doc at a time" }
	...
	{"id": 100, "name": "Learn about Azure DocumentDB" }
	...
	{"id": 1000, "name": "Deep Dive in to DocumentDB" }

Se il numero di libri per editore è piccolo e ha una crescita limitata, può essere utile archiviare il riferimento ai libri nel documento dell'editore. Se invece il numero di libri per editore è illimitato, questo modello di dati darà origine a matrici modificabili e in costante crescita, come nel documento dell'editore di esempio precedente.

Cambiando un po' le cose, si ottiene un modello che rappresenta sempre gli stessi dati, ma che ora evita queste grandi raccolte modificabili.

	Publisher document: 
	{
	    "id": "mspress",
	    "name": "Microsoft Press"
	}
	
	Book documents: 
	{"id": 1,"name": "DocumentDB 101", "pub-id": "mspress"}
	{"id": 2,"name": "DocumentDB for RDBMS Users", "pub-id": "mspress"}
	{"id": 3,"name": "Taking over the world one JSON doc at a time"}
	...
	{"id": 100,"name": "Learn about Azure DocumentDB", "pub-id": "mspress"}
	...
	{"id": 1000,"name": "Deep Dive in to DocumentDB", "pub-id": "mspress"}

Nell'esempio precedente, abbiamo eliminato la raccolta illimitata nel documento dell'editore. Ora abbiamo un solo riferimento all'editore nel documento di ogni libro.

###Come modellare le relazioni many:many?
In un database relazionale le relazioni *molti a molti* vengono spesso modellate con le tabelle join, che creano un join dei record delle altre tabelle.

![Unione di tabelle](./media/documentdb-modeling-data/join-table.png)

Si potrebbe essere tentati di replicare la stessa cosa con i documenti e di generare un modello di dati simile al seguente.

	Author documents: 
	{"id": 1, "name": "Thomas Andersen" }
	{"id": 2, "name": "William Wakefield" }
	
	Book documents:
	{"id": 1, "name": "DocumentDB 101" }
	{"id": 2, "name": "DocumentDB for RDBMS Users" }
	{"id": 3, "name": "Taking over the world one JSON doc at a time" }
	{"id": 4, "name": "Learn about Azure DocumentDB" }
	{"id": 5, "name": "Deep Dive in to DocumentDB" }
	
	Joining documents: 
	{"authorId": 1, "bookId": 1 }
	{"authorId": 2, "bookId": 1 }
	{"authorId": 1, "bookId": 2 }
	{"authorId": 1, "bookId": 3 }

Funzionerebbe, ma, per caricare un autore con i suoi libri o un libro con il suo autore, sarebbero sempre necessarie almeno altre due query sul database; una query per creare un join del documento e quindi un'altra query per recuperare il documento effettivo di cui viene creato il join.

Se la tabella join si limita a incollare insieme due gruppi di dati, allora perché non eliminarla del tutto? Tenere in considerazione quanto segue.

	Author documents:
	{"id": 1, "name": "Thomas Andersen", "books": [1, 2, 3]}
	{"id": 2, "name": "William Wakefield", "books": [1, 4]}
	
	Book documents: 
	{"id": 1, "name": "DocumentDB 101", "authors": [1, 2]}
	{"id": 2, "name": "DocumentDB for RDBMS Users", "authors": [1]}
	{"id": 3, "name": "Learn about Azure DocumentDB", "authors": [1]}
	{"id": 4, "name": "Deep Dive in to DocumentDB", "authors": [2]}

Se avessimo un autore, sapremmo immediatamente quali libri ha scritto e, al contrario, se avessimo caricato un documento relativo ai libri, conosceremmo gli ID degli autori. Questo evita la query intermedia sulla tabella join riducendo il numero di round trip al server che l'applicazione deve eseguire.

##<a id="WrapUp"></a>Modelli di dati ibridi##
Come si è visto, sia l'incorporamento (o denormalizzazione) che il riferimento (o normalizzazione) ai dati presentano vantaggi e compromessi.

Ma non è sempre necessario scegliere uno dei due. È anche possibile mischiare un po' le cose.

In base ai modelli di utilizzo e ai carichi di lavoro specifici dell'applicazione, in certi casi può avere senso unire i dati incorporati e quelli a cui si fa riferimento per ottenere una logica dell'applicazione più semplice con meno round trip al server, mantenendo ugualmente un buon livello di prestazioni.

Si consideri il codice JSON seguente.

	Author documents: 
	{
	    "id": 1,
	    "firstName": "Thomas",
	    "lastName": "Andersen",		
	    "countOfBooks": 3,
	 	"books": [1, 2, 3],
		"images": [
			{"thumbnail": "http://....png"}
			{"profile": "http://....png"}
			{"large": "http://....png"}
		]
	},
	{
	    "id": 2,
	    "firstName": "William",
	    "lastName": "Wakefield",
	    "countOfBooks": 1,
		"books": [1, 4, 5],
		"images": [
			{"thumbnail": "http://....png"}
		]
	}
	
	Book documents:
	{
		"id": 1,
		"name": "DocumentDB 101",
		"authors": [
			{"id": 1, "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
			{"id": 2, "name": "William Wakefield", "thumbnailUrl": "http://....png"}
		]
	},
	{
		"id": 2,
		"name": "DocumentDB for RDBMS Users",
		"authors": [
			{"id": 1, "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
		]
	}

Qui abbiamo per lo più seguito il modello incorporato, in cui i dati delle altre entità vengono incorporati nel documento di primo livello, ma viene fatto riferimento agli altri dati.

Se si osserva il documento dei libri, si possono vedere alcuni campi interessanti quando si considera la matrice degli autori. È presente un campo *id* che viene usato per fare riferimento al documento di un autore, che è una procedura standard in un modello normalizzato, ma poi sono presenti anche *name* e *thumbnailUrl*. Avremmo potuto limitarci a *id* e lasciare che l'applicazione recuperasse le altre informazioni necessarie dal relativo documento dell'autore usando il "collegamento", ma, dal momento che l'applicazione visualizza il nome e un'immagine di anteprima dell'autore con ogni libro visualizzato, è possibile evitare un round trip al server per ogni libro di un elenco denormalizzando **alcuni** dati dall'autore.

Ovviamente, se l'autore cambiasse nome o se volesse aggiornare la foto, sarebbe necessario eseguire un aggiornamento per ogni libro pubblicato, ma per la nostra applicazione, che si basa sul presupposto che gli autori non cambino nome molto spesso, questa è una decisione di progettazione accettabile.

Nell'esempio ci sono valori di **aggregati precalcolati** per evitare la costosa elaborazione di un'operazione di lettura. Nell'esempio, alcuni dati incorporati nel documento dell'autore vengono calcolati in fase di esecuzione. Ogni volta che viene pubblicato un nuovo libro, viene creato un documento per il libro **e** il campo countOfBooks viene impostato su un valore calcolato in base al numero di documenti dei libri esistenti per un determinato autore. Questa ottimizzazione andrebbe bene nei sistemi che eseguono un'intensa attività di lettura, in cui si è disposti a effettuare calcoli nelle scritture per ottimizzare le letture.

DocumentDB supporta le transazioni in più documenti e pertanto consente di usare **transazioni con documenti multipli**. Molti archivi NoSQL non possono eseguire le transazioni nei documenti e, a causa di questa limitazione, inducono a prendere decisioni di progettazione, ad esempio "incorporare sempre tutto". Con DocumentDB, è possibile usare trigger lato server, o stored procedure, che inseriscono i libri e aggiornano gli autori in una sola transazionale ACID. Ora non è **necessario** incorporare tutto in un documento solo per essere sicuri che i dati rimangano coerenti.

##<a name="NextSteps"></a>Passaggi successivi

Il concetto principale espresso in questo articolo è che la modellazione dei dati in un ambiente senza schema è più importante che mai.

Come non esiste un solo modo per rappresentare i dati in una schermata, così non esiste un solo modo per modellare i dati. È necessario conoscere l'applicazione e come genererà, userà ed elaborerà i dati. Quindi, applicando alcune delle linee guida presentate qui, è possibile iniziare a creare un modello che risponda alle esigenze immediate dell'applicazione. Quando le applicazioni devono essere modificate, è possibile sfruttare la flessibilità di un database senza schema per accettare la modifica e far evolvere facilmente il modello di dati.

Per altre informazioni su Azure DocumentDB, fare riferimento alla pagina della [documentazione](../../services/documentdb/) del servizio.

Per informazioni sull'ottimizzazione degli indici in Azure DocumentDB, fare riferimento all'articolo sui [criteri di indicizzazione](documentdb-indexing-policies.md).

Per sapere come condividere i dati in più partizioni, fare riferimento a [Partizionamento dei dati in DocumentDB](documentdb-partition-data.md).

E infine, per informazioni aggiuntive sulla modellazione e la condivisione di dati per le applicazioni multi-tenant, consultare [Scalabilità di un'applicazione multi-tenant con Azure DocumentDB](http://blogs.msdn.com/b/documentdb/archive/2014/12/03/scaling-a-multi-tenant-application-with-azure-documentdb.aspx).
 

<!---HONumber=Oct15_HO3-->