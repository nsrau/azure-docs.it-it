---
title: Modellare e partizionare i dati in Azure Cosmos DB con un esempio reale
description: Informazioni su come modellare e partizionare un esempio reale usando l'API Core di Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: ef999d4b452f3f31942e1fb2ddb46efe760acff0
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342148"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Come modellare e partizionare i dati in Azure Cosmos DB usando un esempio reale
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo si basa su diversi concetti Azure Cosmos DB come la [modellazione dei dati](modeling-data.md), il [partizionamento](partitioning-overview.md)e la [velocità effettiva con provisioning](request-units.md) per illustrare come affrontare un esercizio di progettazione dei dati reale.

Se in genere si lavora con i database relazionali, saranno già state implementate abitudini e intuizioni su come progettare un modello di dati. A causa dei vincoli specifici, ma anche grazie ai punti di forza di Azure Cosmos DB, la maggior parte di queste procedure consigliate non produce il risultato auspicato e può condurre a soluzioni meno che ottimali. L'obiettivo di questo articolo è guidare l'utente attraverso il processo completo di un caso di utilizzo reale di modellazione in Azure Cosmos DB, dalla modellazione degli elementi alla condivisione del percorso delle entità e al partizionamento del contenitore.

## <a name="the-scenario"></a>Scenario

Per questo esercizio, si prenderà in considerazione il dominio di una piattaforma di blog in cui gli *utenti* possono scrivere dei *post* a cui possono anche *aggiungere Mi piace* e *commenti*.

> [!TIP]
> Sono evidenziate in *corsivo* alcune parole che identificano il tipo di elementi che il modello dovrà modificare.

Sono necessari altri requisiti oltre alle specifiche iniziali:

- Una pagina iniziale in cui sia visualizzato un feed dei post creati di recente
- La possibilità di recuperare tutti i post di un utente, tutti i commenti per un post e tutti i Mi piace per un post
- I post vengono restituiti con il nome utente dei rispettivi autori e il numero di commenti e Mi piace ricevuti
- Anche i commenti e i Mi piace vengono restituiti con il nome utente degli autori
- Quando visualizzati sotto forma di elenchi, i post devono presentare solo un riepilogo troncato del relativo contenuto

## <a name="identify-the-main-access-patterns"></a>Identificare i modelli di accesso principali

Per iniziare, si strutturerà la specifica iniziale identificando i modelli di accesso della soluzione. Quando si progetta un modello di dati per Azure Cosmos DB, è importante comprendere a quali richieste il modello dovrà rispondere per assicurarsi che le gestisca in modo efficiente.

Per rendere l'intero processo più semplice da seguire, le diverse richieste vengono classificate come comandi o query, prendendo in prestito la terminologia di [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation), in cui i comandi sono richieste di scrittura (vale a dire, finalità di aggiornamento del sistema) e le query sono richieste di sola lettura.

Ecco l'elenco di richieste che la piattaforma dovrà esporre:

- **[C1]** Creare/modificare un utente
- **[Q1]** Recuperare un utente
- **[C2]** Creare/modificare un post
- **[Q2]** Recuperare un post
- **[Q3]** Elencare i post di un utente in forma breve
- **[C3]** Creare un commento
- **[Q4]** Elenca i commenti di un post
- **[C4]** Aggiungere Mi piace a un post
- **[Q5]** Elencare i Mi piace ricevuti da un post
- **[Q6]** Elenca gli *x* post più recenti creati in forma breve (feed)

In questa fase, non sono stati considerati i dettagli relativi a ogni entità (utente, post e così via) che conterrà. Questo passaggio è in genere tra le prime da affrontare quando si progetta in un archivio relazionale, perché è necessario determinare il modo in cui tali entità verranno convertite in termini di tabelle, colonne, chiavi esterne e così via. È molto meno problematico per un database di documenti che non impone alcuno schema in fase di scrittura.

Il motivo principale per cui è importante identificare i modelli di accesso sin dall'inizio è che questo elenco di richieste diventerà il gruppo di test. Ogni volta che si esegue l'iterazione del modello di dati, si verificheranno le prestazioni e la scalabilità di ciascuna richiesta.

## <a name="v1-a-first-version"></a>V1: una prima versione

Si inizia con due contenitori: `users` e `posts`.

### <a name="users-container"></a>Contenitore utenti

In questo contenitore vengono archiviati solo gli elementi utente:

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

Il contenitore viene partizionato in base a `id`, cioè ogni partizione logica al suo interno conterrà un solo elemento.

### <a name="posts-container"></a>Contenitore post

Questo contenitore ospita post, commenti e Mi piace:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "title": "<post-title>",
    "content": "<post-content>",
    "creationDate": "<post-creation-date>"
}

{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "creationDate": "<like-creation-date>"
}
```

Il contenitore viene partizionato in base a `postId`, cioè ogni partizione logica al suo interno conterrà un solo post, tutti i commenti per tale post e tutti i Mi piace per tale post.

Come si può osservare, è stata introdotta una proprietà `type` negli elementi archiviati in questo contenitore per distinguere tra i tre tipi di entità in esso ospitati.

Si è anche scelto di fare riferimento ai dati correlati invece di incorporarli (per informazioni dettagliate su questi concetti, consultare [questa sezione](modeling-data.md)), per i seguenti motivi:

- non vi è alcun limite al numero di post che un utente può creare
- la lunghezza dei post può essere arbitraria
- non vi è alcun limite al numero di commenti e Mi piace che un post può ricevere
- si vuole avere la possibilità di aggiungere un commento o un Mi piace a un post senza dover aggiornare il post stesso

## <a name="how-well-does-our-model-perform"></a>Valutare le prestazioni del modello

A questo punto verranno valutate le prestazioni e la scalabilità della prima versione. Per ognuna delle richieste identificate in precedenza, vengono misurati la latenza e il numero di unità richiesta che utilizza. Questa misurazione viene eseguita in un set di dati fittizio contenente 100.000 utenti, ciascuno con un numero di post da 5 a 50, e con un massimo di 25 commenti e 100 Mi piace per ogni post.

### <a name="c1-createedit-a-user"></a>[C1] Creare/modificare un utente

Questa richiesta è semplice da implementare in quanto è sufficiente creare o aggiornare un elemento nel contenitore `users`. Le richieste si suddivideranno perfettamente in tutte le partizioni grazie alla chiave di partizione `id`.

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="Scrittura di un singolo elemento nel contenitore utenti" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 7 ms | 5,71 UR | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Recuperare un utente

Per recuperare un utente si legge l'elemento corrispondente dal contenitore `users`.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="Recupero di un singolo elemento dal contenitore utenti" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 2 ms | 1 UR | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Creare/modificare un post

Analogamente a **[C1]** , occorre solo scrivere nel contenitore `posts`.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Scrittura di un singolo elemento nel contenitore post" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 9 ms | 8,76 UR | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] Recuperare un post

Si inizia con il recupero del documento corrispondente dal contenitore `posts`. Dal momento che non è sufficiente, in base alla specifica è anche necessario aggregare il nome utente dell'autore del post e il numero di commenti e di Mi piace ricevuti da questo post, per cui occorre eseguire tre query SQL aggiuntive.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="Recupero di un post e aggregazione dei dati aggiuntivi" border="false":::

Ognuna delle query aggiuntive filtra la chiave di partizione del rispettivo contenitore, che è proprio ciò che si vuole ottenere per ottimizzare le prestazioni e la scalabilità. Tuttavia, alla fine sarà necessario eseguire quattro operazioni per restituire un singolo post, perciò si procederà a migliorarlo nella prossima iterazione.

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 9 ms | 19,54 UR | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Elencare i post di un utente in forma breve

In primo luogo, è necessario recuperare i post desiderati con una query SQL che recupera i post corrispondenti a tale utente specifico. Ma è anche necessario eseguire query aggiuntive per aggregare il nome utente dell'autore e i numeri di commenti e Mi piace.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="Recupero di tutti i post di un utente e aggregazione dei dati aggiuntivi" border="false":::

Questa implementazione presenta numerosi svantaggi:

- è necessario eseguire le query che aggregano i numeri di commenti e Mi piace per ogni post restituito dalla prima query
- la query principale non filtra la chiave di partizione del contenitore `posts`, causando un fan-out e un'analisi della partizione nell'intero contenitore.

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 130 ms | 619,41 UR | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Creare un commento

Per creare un commento basta scrivere l'elemento corrispondente nel contenitore `posts`.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Scrittura di un singolo elemento nel contenitore post" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 7 ms | 8,57 UR | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Elencare i commenti a un post

Per iniziare, si esegue una query che recupera tutti i commenti per tale post e, anche in questo caso, è necessario aggregare i nomi utente separatamente per ogni commento.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="Recupero di tutti i commenti a un post e aggregazione dei dati aggiuntivi" border="false":::

Anche se la query principale filtra la chiave di partizione del contenitore, aggregare separatamente i nomi utente penalizza le prestazioni complessive. Sarà possibile migliorarle in un secondo momento.

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 23 ms | 27,72 UR | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Aggiungere Mi piace a un post

Come per **[C3]** , basta scrivere l'elemento corrispondente nel contenitore `posts`.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Scrittura di un singolo elemento nel contenitore post" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 6 ms | 7,05 UR | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Elencare i Mi piace ricevuti da un post

Analogamente a **[Q4]** , si esegue una query per i Mi piace ricevuti da tale post, quindi si aggregano i relativi nomi utente.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="Recupero di tutti i Mi piace ricevuti da un post e aggregazione dei dati aggiuntivi" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 59 ms | 58,92 UR | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Elencare i x post più recenti in forma breve (feed)

Si recuperano i post più recenti eseguendo la query del contenitore `posts` per data di creazione decrescente, quindi si aggregano i nomi utente e il numero di commenti e Mi piace per ciascuno dei post.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="Recupero dei post più recenti e aggregazione dei dati aggiuntivi" border="false":::

Ancora una volta, la query iniziale non filtra sulla chiave di partizione del `posts` contenitore, che attiva un fan-out costoso. Questo è ancora peggio quando si fa riferimento a un set di risultati molto più ampio e si ordinano i risultati con una `ORDER BY` clausola, il che rende più costosa in termini di unità richiesta.

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 306 ms | 2063,54 UR | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Riflessioni sulle prestazioni della V1

Esaminando i problemi di prestazioni affrontati nella sezione precedente, è possibile identificare due classi principali dei problemi:

- alcune richieste richiedono l'esecuzione di più query per raccogliere tutti i dati che dovranno essere restituiti
- alcune query non filtrano la chiave di partizione dei contenitori di destinazione, determinando un fan-out che ostacola la scalabilità.

È possibile risolvere ognuno di questi problemi, a partire dal primo.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Introduzione alla denormalizzazione per ottimizzare le query di lettura

Il motivo per cui in alcuni casi è necessario eseguire richieste aggiuntive è che i risultati della richiesta iniziale non contengono tutti i dati che devono essere restituiti. Quando si usa un archivio dati non relazionali come Azure Cosmos DB, questo tipo di problema è comunemente risolvibile denormalizzando i dati nel set di dati.

In questo esempio, si modificano i post aggiungendo il nome utente del relativo autore, il numero di commenti e il numero di Mi piace:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Si modificano anche i commenti e i Mi piace aggiungendo il nome utente del relativo autore:

```json
{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "userUsername": "<comment-author-username>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "userUsername": "<liker-username>",
    "creationDate": "<like-creation-date>"
}
```

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizzazione del numero di commenti e Mi piace

L'obiettivo in questo caso è incrementare il `commentCount` o il `likeCount` nel post corrispondente ogni volta che si aggiunge un commento o un Mi piace. Dal momento che il contenitore `posts` è partizionato per `postId`, il nuovo elemento (commento o Mi piace) e il post corrispondente si trovano nella medesima partizione logica. Di conseguenza, è possibile usare una [stored procedure](stored-procedures-triggers-udfs.md) per eseguire tale operazione.

A questo punto, quando si crea un commento ( **[C3]** ), anziché aggiungere solo un nuovo elemento nel contenitore `posts` si chiama la stored procedure seguente in tale contenitore:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Questa stored procedure accetta l'ID del post e il corpo del nuovo commento come parametri, quindi:

- recupera il post
- incrementa il `commentCount`
- sostituisce il post
- aggiunge il nuovo commento

Poiché le stored procedure vengono eseguite come transazioni atomiche, il valore di `commentCount` e il numero effettivo di commenti resteranno sempre sincronizzati.

Ovviamente si chiama una stored procedure simile quando si aggiungono nuovi Mi piace per incrementare il `likeCount`.

### <a name="denormalizing-usernames"></a>Denormalizzazione dei nomi utente

I nomi utente richiedono un approccio diverso in quanto gli utenti non solo si trovano in partizioni diverse, ma anche in un contenitore diverso. Quando è necessario denormalizzare i dati nelle partizioni e i contenitori, è possibile usare il [feed di modifiche](change-feed.md) del contenitore di origine.

In questo esempio, si usa il feed delle modifiche del contenitore `users` per reagire ogni volta che gli utenti aggiornano i propri nomi utente. In questo caso, si propaga la modifica chiamando un'altra stored procedure sul contenitore `posts`:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="Denormalizzazione dei nomi utente nel contenitore post" border="false":::

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Questa stored procedure accetta l'ID dell'utente e il nuovo nome utente come parametri, quindi:

- recupera tutti gli elementi corrispondenti all'`userId` (che possono essere post, commenti, o Mi piace)
- per ognuno di questi elementi
  - sostituisce il `userUsername`
  - sostituisce l'elemento

> [!IMPORTANT]
> Questa operazione è dispendiosa perché richiede l'esecuzione di questa stored procedure in ogni partizione del contenitore `posts`. È molto probabile che la maggior parte degli utenti sceglierà un nome utente appropriato durante l'iscrizione e non lo modificherà mai, dunque questo aggiornamento verrà eseguito molto raramente.

## <a name="what-are-the-performance-gains-of-v2"></a>Quali sono i miglioramenti delle prestazioni offerti dalla V2?

### <a name="q2-retrieve-a-post"></a>[Q2] Recuperare un post

Ora che la denormalizzazione è stata applicata, è necessario solo recuperare un singolo elemento per gestire tale richiesta.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="Recupero di un singolo elemento dal contenitore post" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 2 ms | 1 UR | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Elencare i commenti a un post

Anche in questo caso, è possibile fare a meno delle richieste aggiuntive che recuperavano i nomi utente e terminare con una singola query che filtra la chiave di partizione.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="Recupero di tutti i commenti per un post" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 4 ms | 7,72 UR | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Elencare i Mi piace ricevuti da un post

La stessa esatta situazione di quando si elencano i Mi piace.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="Recupero di tutti i Mi piace per un post" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 4 ms | 8,92 UR | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: assicurarsi che tutte le richieste siano scalabili

Esaminando i miglioramenti delle prestazioni complessivi, ci sono ancora due richieste che non sono state ancora completamente ottimizzate: **[Q3]** e **[Q6]**. Si tratta di richieste che prevedono query che non filtrano la chiave di partizione dei contenitori di destinazione.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Elencare i post di un utente in forma breve

Questa richiesta trae già vantaggio dai miglioramenti introdotti nella V2, permettendo di evitare query aggiuntive.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="Diagramma che mostra la query per elencare i post di un utente in forma breve." border="false":::

Tuttavia, la query rimanente continua a non filtrare la chiave di partizione del contenitore `posts`.

È semplice gestire questa situazione:

1. Questa richiesta *deve* filtrare l'`userId` perché occorre recuperare tutti i post di un determinato utente
1. Non si ottiene il risultato previsto perché viene eseguita sul contenitore `posts`, che non è partizionato in base a `userId`
1. Ovviamente, il problema di prestazioni potrebbe essere risolto eseguendo questa richiesta su un contenitore che *sia* partizionato per `userId`
1. In questo caso, si tratta del contenitore `users` esistente.

Quindi, si introduce un secondo livello di denormalizzazione duplicando interi post nel contenitore `users`. In questo modo, si ottiene efficacemente una copia dei post, partizionati però lungo dimensioni diverse, rendendo così più efficiente il recupero in base al rispettivo `userId`.

Il contenitore `users` ora contiene due tipi di elementi:

```json
{
    "id": "<user-id>",
    "type": "user",
    "userId": "<user-id>",
    "username": "<username>"
}

{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Tenere presente quanto segue:

- è stato introdotto un campo `type` nell'elemento utente per distinguere gli utenti dai post
- è stato anche aggiunto un campo `userId` all'elemento utente, che è ridondante con il campo `id`, ma è richiesto in quanto il contenitore `users` è ora partizionato in base all'`userId` (e non all'`id`, come in precedenza)

Per conseguire tale denormalizzazione, si usa ancora una volta il feed di modifiche. Questa volta, si risponde al feed di modifiche del contenitore `posts` per inviare post nuovi o aggiornati al contenitore `users`. In più, dal momento che per elencare i post non è necessario restituirne il contenuto completo, è possibile troncarli nel processo.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="Denormalizzazione dei post nel contenitore utenti" border="false":::

A questo punto è possibile indirizzare la query al contenitore `users`, filtrando la chiave di partizione del contenitore.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="Recupero di tutti i post per un utente" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 4 ms | 6,46 UR | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Elencare i x post più recenti in forma breve (feed)

In questo caso è necessario affrontare una situazione analoga: anche dopo aver evitato le query aggiuntive, rese superflue dalla denormalizzazione introdotta nella V2, la query rimanente non filtra la chiave di partizione del contenitore:

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="Diagramma che mostra la query per elencare gli x post più recenti creati in forma breve." border="false":::

Seguendo lo stesso approccio, per ottimizzare le prestazioni e la scalabilità di questa richiesta, è necessario inviarla a una sola partizione. Ciò è plausibile perché occorre restituire solo un numero limitato di elementi; per poter popolare la home page della piattaforma di blog, è sufficiente ottenere i 100 post più recenti, senza la necessità di eseguire la paginazione attraverso l'intero set di dati.

Dunque, per ottimizzare quest'ultima richiesta, si introduce nella progettazione un terzo contenitore, interamente dedicato a rispondere a questa richiesta. Si esegue la denormalizzazione dei post nel nuovo contenitore `feed`:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Questo contenitore è suddiviso in partizioni per `type`, che sarà sempre `post` negli elementi di questo esempio. Questa operazione assicura che tutti gli elementi in questo contenitore si troveranno nella stessa partizione.

Per ottenere la denormalizzazione, è sufficiente utilizzare la pipeline del feed di modifiche introdotta in precedenza per inviare i post al nuovo contenitore. È importante ricordare che è necessario assicurarsi che vengano archiviati solo i 100 post più recenti. In caso contrario, il contenuto del contenitore può eccedere le dimensioni massime di una partizione. Per eseguire questa operazione si chiama un [post-trigger](stored-procedures-triggers-udfs.md#triggers) ogni volta che viene aggiunto un documento al contenitore:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="Denormalizzazione dei post nel contenitore feed" border="false":::

Di seguito è riportato il corpo del post-trigger che tronca la raccolta:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Il passaggio finale consiste nel reindirizzare la query al nuovo contenitore `feed`:

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="Recupero dei post più recenti" border="false":::

| **Latency** | **Addebito UR** | **Prestazioni** |
| --- | --- | --- |
| 9 ms | 16,97 UR | ✅ |

## <a name="conclusion"></a>Conclusione

Verranno ora esaminati i miglioramenti complessivi delle prestazioni e della scalabilità introdotti nelle diverse versioni progettate.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **C1** | 7 ms/5,71 UR | 7 ms/5,71 UR | 7 ms/5,71 UR |
| **Q1** | 2 ms/1 UR | 2 ms/1 UR | 2 ms/1 UR |
| **C2** | 9 ms/8,76 UR | 9 ms/8,76 UR | 9 ms/8,76 UR |
| **Q2** | 9 ms/ 19,54 UR | 2 ms/1 UR | 2 ms/1 UR |
| **Q3** | 130 ms/619,41 UR | 28 ms/201,54 UR | 4 ms/6,46 UR |
| **C3** | 7 ms/8,57 UR | 7 ms /15.27 RU | 7 ms /15.27 RU |
| **[Q4]** | 23 ms/27,72 UR | 4 ms/7,72 UR | 4 ms/7,72 UR |
| **C4** | 6 ms/7,05 UR | 7 ms/14,67 UR | 7 ms/14,67 UR |
| **[Q5]** | 59 ms/58,92 UR | 4 ms/8,92 UR | 4 ms/8,92 UR |
| **[Q6]** | 306 ms/2063,54 UR | 83 ms/532,33 UR | 9 ms/16,97 UR |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>È stato ottimizzato uno scenario con intensa attività di lettura

Si sarà notato che gli sforzi sono stati incentrati sul migliorare le prestazioni delle richieste di lettura (query) a scapito delle richieste di scrittura (comandi). In molti casi, le operazioni di scrittura ora attivano la successiva denormalizzazione tramite feed di modifiche, che ne aumenta i costi di calcolo e i tempi di materializzazione.

Ciò è giustificato dal fatto che una piattaforma di blog (come la maggior parte delle app social) è caratterizzata da un'intensa attività di lettura; questo significa che la quantità di richieste di lettura che deve gestire di solito supera la quantità di richieste di scrittura di diversi ordini di grandezza. È quindi opportuno aumentare il costo di esecuzione delle richieste di scrittura per ridurre quello delle richieste di lettura, migliorandone le prestazioni.

Se si osserva l'ottimizzazione più estrema eseguita, **[Q6]** è passato da oltre 2000 UR a sole 17 UR, grazie alla normalizzazione dei post a un costo di circa 10 UR per ogni elemento. Dal momento che l'attività principale consisterebbe nel gestire molte più richieste di feed piuttosto che nel creare o aggiornare i post, il costo della denormalizzazione è trascurabile, considerando il risparmio complessivo.

### <a name="denormalization-can-be-applied-incrementally"></a>La denormalizzazione può essere applicata in modo incrementale

I miglioramenti della scalabilità esplorati in questo articolo riguardano la denormalizzazione e la duplicazione dei dati nel set di dati. Si noti che non è necessario implementare queste ottimizzazioni sin dal primo momento. Le query che filtrano le chiavi di partizione offrono prestazioni migliori su larga scala, ma le query tra partizioni possono essere completamente accettabili se vengono chiamate raramente o rispetto a un set di dati limitato. Se si sta semplicemente creando un prototipo o lanciando un prodotto con una base di utenti ridotta e controllata, sarà possibile rimandare tali miglioramenti a un secondo momento. È importante dunque [monitorare](use-metrics.md) le prestazioni del modello in modo da decidere se e quando è il momento di introdurli.

Il feed di modifiche usato per distribuire gli aggiornamenti negli altri contenitori archivia tutti questi aggiornamenti in modo definitivo. Questo rende possibile richiedere tutti gli aggiornamenti dalla creazione del contenitore ed eseguire il bootstrap delle viste denormalizzate come un'operazione di recupero occasionale, anche se il sistema contiene già una grande quantità di dati.

## <a name="next-steps"></a>Passaggi successivi

Dopo questa introduzione pratica alla modellazione di dati e al partizionamento, consultare gli articoli seguenti per rivedere i concetti che sono stati trattati:

- [Usare database, contenitori ed elementi](account-databases-containers-items.md)
- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
- [Feed delle modifiche in Azure Cosmos DB](change-feed.md)
