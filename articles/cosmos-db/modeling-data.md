---
title: Modellazione dei dati in Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Informazioni sulla modellazione dei dati nei database NoSQL e sulle differenze tra la modellazione dei dati nei database relazionali e nei database di documenti.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: 3cf075c2909b35bb08ca4cb24aaa3b99597f34c6
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203523"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modellazione dei dati in Azure Cosmos DB

Anche se i database privi di schema, come Azure Cosmos DB, è estremamente facile archiviare ed eseguire query sui dati strutturati e semi-strutturati, è consigliabile dedicare alcuni evoluzioni ora il modello di dati per ottenere il massimo del servizio in termini di prestazioni e scalabilità e più basso costo.

Come verranno archiviati i dati? In che modo l'applicazione recupererà i dati e ne eseguirà la query? È l'applicazione con intensa attività di lettura o scrittura-con intensa attività?

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

* Cos'è la modellazione dei dati e perché è importante?
* Come è diverso rispetto a un database relazionale di modellazione dei dati in Azure Cosmos DB?
* Come si esprimono le relazioni tra i dati in un database non relazionale?
* Quando si incorporano i dati e quando si creano i collegamenti ai dati?

## <a name="embedding-data"></a>Incorporamento dei dati

Quando si inizia a modellare i dati in Azure Cosmos DB cercare di considerare le entità come **self-contained elementi** rappresentate da documenti JSON.

Per il confronto, prima vediamo come è possibile modellare i dati in un database relazionale. L'esempio seguente mostra come una persona possa essere archiviata in un database relazionale.

![Database relazionale](./media/sql-api-modeling-data/relational-data-model.png)

Quando si lavora con i database relazionali, la strategia consiste nel normalizzare tutti i dati. Normalizzazione dei dati in genere prende un'entità, ad esempio una persona e suddividendola in componenti discreti. Nell'esempio precedente, una persona può avere più record di dettagli contatto, oltre a più record di indirizzo. I dettagli dei contatti possono essere ulteriormente suddivisi estraendo comuni campi, ad esempio un tipo. Lo stesso vale per indirizzo, ogni record può essere di tipo *casa* oppure *Business*.

Il presupposto principale quando si normalizzano i dati è **evitare di archiviare i dati ridondanti** in ogni record e fare invece riferimento ai dati. In questo esempio, per leggere una persona, con tutti i dettagli di contatto e indirizzi, è necessario usare join in modo efficace comporre nuovamente (o denormalizzazione) i dati in fase di esecuzione.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Per aggiornare una singola persona con i dettagli contatto e gli indirizzi, è necessario eseguire operazioni di scrittura in più tabelle.

Ora diamo un'occhiata a come si modellano gli stessi dati come entità autosufficiente in Azure Cosmos DB.

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
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

Usa l'approccio sopra è dispone **denormalizzati** il record della persona, da **incorporamento** tutte le informazioni correlate a questa persona, ad esempio i dettagli di contatto e gli indirizzi, in un *singolo JSON* documento.
Inoltre, dal momento che non esistono limiti imposti da uno schema fisso, abbiamo la flessibilità necessaria, ad esempio, per avere dettagli contatto di forme completamente diverse.

Il recupero di un record completo persona dal database è ora un **singola operazione di lettura** su un singolo contenitore e per un singolo elemento. L'aggiornamento di record di una persona, con i dettagli di contatto e gli indirizzi, è anche un **singola operazione di scrittura** rispetto a un singolo elemento.

Denormalizzando i dati, è possibile che l'applicazione debba eseguire meno query e aggiornamenti per completare le comuni operazioni.

### <a name="when-to-embed"></a>Quando eseguire l'incorporamento

In generale, usare i modelli di dati incorporati quando:

* Esistono **contenuti** le relazioni tra entità.
* Esistono relazioni **one-to-few** tra le entità.
* Esistono dati incorporati che **cambiano raramente**.
* Non ci sono dati incorporati che non aumenteranno **senza associazione**.
* Non ci sono dati incorporati che è **sottoposti a query frequentemente insieme**.

> [!NOTE]
> I modelli di dati denormalizzati garantiscono di solito prestazioni di **lettura** più elevate.

### <a name="when-not-to-embed"></a>Quando non eseguire l'incorporamento

Anche se la regola generale in Azure Cosmos DB è denormalizzare tutti gli elementi e incorporare tutti i dati in un singolo elemento, questo può causare situazioni che deve essere evitata.

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

Questo potrebbe essere l'aspetto di un'entità post con commenti incorporati, se si modellasse un tipico sistema di blog, o CMS. Il problema di questo esempio è che la matrice di commenti non è **limitata**, vale a dire che non esiste in pratica un limite al numero di commenti per i singoli post. Questo può diventare un problema perché le dimensioni dell'elemento è stato possibile raggiungere dimensioni all'infinito.

Con la possibilità di trasmettere i dati tramite la rete, nonché leggere e aggiornare l'elemento, in modo scalabile, di aumentare le dimensioni dell'elemento sarà interessata.

In questo caso, sarebbe meglio prendere in considerazione il seguente modello di dati.

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Questo modello include i tre commenti più recenti incorporati nel contenitore post, ovvero una matrice con un set fisso di attributi. Gli altri commenti sono raggruppati in batch di 100 commenti e archiviati come elementi separati. La dimensione scelta per il batch è 100 perché l'applicazione fittizia consente all'utente di caricare 100 commenti per volta.  

Un altro caso in cui incorporare i dati non sono una buona idea è quando i dati incorporati vengono usati spesso nei elementi e cambiano spesso.

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

Questo potrebbe essere il portafoglio azionario di una persona. Si è scelto di incorporare le informazioni sulle azioni in ogni documento del portafoglio. In un ambiente in cui i dati correlati cambieranno spesso, come in un'applicazione per le contrattazioni azionarie, l'incorporamento di dati che cambiano spesso obbligherà ad aggiornare continuamente ogni documento del portafoglio ogni volta che viene scambiata un'azione.

Il titolo *zaza* potrebbe essere scambiato diverse centinaia di volte in un solo giorno e migliaia di utenti potrebbero avere *zaza* nel portafoglio. Con un modello di dati come quello sopra è necessario aggiornare molte migliaia di documenti del portfolio più volte al giorno, con una scarsa efficienza della scalabilità del sistema.

## <a name="referencing-data"></a>Fare riferimento ai dati

Incorporamento di dati funziona senza problemi in molti casi, ma esistono scenari quando denormalizzazione dei dati comporterà ulteriori problemi che non vale la pena. Cosa si può fare dunque?

Le relazioni tra entità non devono essere necessariamente create in un database relazionale. In un database di documenti, è possibile avere informazioni in un documento che si riferisce ai dati in altri documenti. Non è consigliabile creare sistemi che sarebbero più appropriati per un database relazionale in Azure Cosmos DB o qualsiasi altro database di documenti, ma le relazioni semplici vanno bene e possono essere utili.

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
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

L'aspetto negativo di questo approccio diventa però immediatamente evidente se l'applicazione deve mostrare informazioni su ogni titolo disponibile quando si visualizza il portafoglio di una persona. In questo caso, sarebbe necessario accedere più volte al database per caricare le informazioni del documento di ogni titolo. Qui è stata presa la decisione di aumentare l'efficienza delle operazioni di scrittura, che vengono eseguite spesso durante la giornata, compromettendo però le operazioni di lettura che hanno un impatto potenzialmente minore sulle prestazioni di questo particolare sistema.

> [!NOTE]
> I modelli di dati normalizzati **possono richiedere più round trip** al server.

### <a name="what-about-foreign-keys"></a>Chiavi esterne

Poiché attualmente non esiste alcun vincolo, chiave esterna o altro, le relazioni esistenti tra i documenti sono di fatto "collegamenti deboli" e non verranno verificate dal database. Per essere certi che i dati a cui un documento fa riferimento esistano davvero, è eseguire questa operazione nell'applicazione oppure tramite trigger lato server o stored procedure in Azure Cosmos DB.

### <a name="when-to-reference"></a>Quando fare riferimento

In generale, usare i modelli di dati denormalizzati quando:

* Si rappresentano relazioni **uno a molti** .
* Si rappresentano relazioni **molti a molti** .
* I dati correlati **cambiano spesso**.
* È possibile che i dati a cui si fa riferimento **non siamo limitati**.

> [!NOTE]
> La normalizzazione offre di solito migliori prestazioni di **scrittura** .

### <a name="where-do-i-put-the-relationship"></a>Dove inserire le relazioni

La crescita della relazione aiuterà a determinare in quale documento archiviare il riferimento.

Il codice JSON seguente modella editori e libri.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

Se il numero di libri per editore è piccolo e ha una crescita limitata, può essere utile archiviare il riferimento ai libri nel documento dell'editore. Se invece il numero di libri per editore è illimitato, questo modello di dati darà origine a matrici modificabili e in costante crescita, come nel documento dell'editore di esempio precedente.

Cambiando un po' le cose, si ottiene un modello che rappresenta sempre gli stessi dati, ma che ora evita queste grandi raccolte modificabili.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

Nell'esempio precedente, abbiamo eliminato la raccolta illimitata nel documento dell'editore. Ora abbiamo solo un riferimento all'editore nel documento di ogni libro.

### <a name="how-do-i-model-manymany-relationships"></a>Come modellare le relazioni many:many?

In un database relazionale le relazioni *molti a molti* vengono spesso modellate con le tabelle join, che creano un join dei record delle altre tabelle.

![Unione di tabelle](./media/sql-api-modeling-data/join-table.png)

Si potrebbe essere tentati di replicare la stessa cosa con i documenti e di generare un modello di dati simile al seguente.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Funzionerebbe, ma, per caricare un autore con i suoi libri o un libro con il suo autore, sarebbero sempre necessarie almeno altre due query sul database; una query per creare un join del documento e quindi un'altra query per recuperare il documento effettivo di cui viene creato il join.

Se la tabella join si limita a incollare insieme due gruppi di dati, allora perché non eliminarla del tutto?
Tenere in considerazione quanto segue.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

A questo punto, se avessi un autore, è possibile sapere immediatamente quali libri ha scritto e viceversa se è stato caricato un documento dei libri, conosceremmo gli ID degli autori. Questo evita la query intermedia sulla tabella join riducendo il numero di round trip al server che l'applicazione deve eseguire.

## <a name="hybrid-data-models"></a>Modelli di dati ibridi

Come si è visto, sia l'incorporamento (o denormalizzazione) che il riferimento (o normalizzazione) ai dati presentano vantaggi e compromessi.

Ma non è sempre necessario scegliere uno dei due. È anche possibile mischiare un po' le cose.

In base ai modelli di utilizzo e ai carichi di lavoro specifici dell'applicazione, in certi casi può avere senso unire i dati incorporati e quelli a cui si fa riferimento per ottenere una logica dell'applicazione più semplice con meno round trip al server, mantenendo ugualmente un buon livello di prestazioni.

Si consideri il codice JSON seguente.

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

Qui abbiamo per lo più seguito il modello incorporato, in cui i dati delle altre entità vengono incorporati nel documento di primo livello, ma viene fatto riferimento agli altri dati.

Se si osserva il documento dei libri, si possono vedere alcuni campi interessanti quando si considera la matrice degli autori. È presente un' `id` campo che rappresenta il campo viene usato per fare riferimento a un documento dell'autore, la procedura standard in un modello normalizzato, ma poi sono presenti anche `name` e `thumbnailUrl`. È stato possibile hanno bloccato con `id` e chiuso l'applicazione per ottenere informazioni aggiuntive necessarie dal relativo documento dell'autore usando il "collegamento", tuttavia, poiché l'applicazione visualizza un'immagine di anteprima con ogni libro e il nome dell'autore visualizzata è possibile salvare un round trip al server per ogni libro in un elenco denormalizzando **alcuni** dati dall'autore.

Certo, se il nome dell'autore o se volesse aggiornare la foto è stato necessario accedere e aggiornare ogni libro vengono sempre pubblicati ma per la nostra applicazione, partendo dal presupposto che gli autori non cambino nome spesso, si tratta di una decisione di progettazione accettabile.  

Nell'esempio sono presenti valori di **aggregazioni precalcolate**, che consentono di evitare l'elaborazione di costose operazioni di lettura. Nell'esempio, alcuni dati incorporati nel documento dell'autore vengono calcolati in fase di esecuzione. Ogni volta che viene pubblicato un nuovo libro, viene creato un documento per il libro **e** il campo countOfBooks viene impostato su un valore calcolato in base al numero di documenti dei libri esistenti per un determinato autore. Questa ottimizzazione andrebbe bene nei sistemi che eseguono un'intensa attività di lettura, in cui si è disposti a effettuare calcoli nelle scritture per ottimizzare le letture.

Azure Cosmos DB supporta le transazioni in più documenti e consente quindi di usare **transazioni su più documenti**. Molti archivi NoSQL non possono eseguire le transazioni nei documenti e, a causa di questa limitazione, inducono a prendere decisioni di progettazione, ad esempio "incorporare sempre tutto". Con Azure Cosmos DB è possibile usare trigger lato server, o stored procedure, che inseriscono i libri e aggiornano gli autori in una sola transazione ACID. Ora non è **necessario** incorporare tutto in un unico documento solo per essere sicuri che i dati rimangano coerenti.

## <a name="distinguishing-between-different-document-types"></a>Applicare la distinzione tra diversi tipi di documento

In alcuni scenari, è possibile combinare tipi di documento diverso nella stessa raccolta. Ciò avviene in genere quando si desidera più documenti a cui si trovano nella stessa correlati [partizione](partitioning-overview.md). Ad esempio, è possibile inserire entrambi libri e recensioni nella stessa raccolta e di partizione da `bookId`. In tale situazione, in genere da aggiungere ai documenti con un campo che identifica il tipo per distinguerli.

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>Passaggi successivi

Il concetto principale espresso in questo articolo è che la modellazione dei dati in un ambiente senza schema è più importante che mai.

Come non esiste un solo modo per rappresentare i dati in una schermata, così non esiste un solo modo per modellare i dati. È necessario conoscere l'applicazione e come genererà, userà ed elaborerà i dati. Quindi, applicando alcune delle linee guida presentate qui, è possibile iniziare a creare un modello che risponda alle esigenze immediate dell'applicazione. Quando le applicazioni devono essere modificate, è possibile sfruttare la flessibilità di un database senza schema per accettare la modifica e far evolvere facilmente il modello di dati.

Per altre informazioni su Azure Cosmos DB, vedere la pagina della [documentazione](https://azure.microsoft.com/documentation/services/cosmos-db/) del servizio.

Per informazioni su come suddividere i dati in più partizioni, vedere [Partizionamento dei dati in Azure Cosmos DB](sql-api-partition-data.md).
