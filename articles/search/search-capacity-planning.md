---
title: Regolare la capacità per i carichi di lavoro di query e indici
titleSuffix: Azure Cognitive Search
description: Modificare le risorse del computer di replica e di partizione in ricerca cognitiva di Azure, in cui ogni risorsa viene addebitata in unità di ricerca fatturabili.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 92dcbfd360938724bb65b734d7c69ea61d7826b0
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533044"
---
# <a name="adjust-the-capacity-of-an-azure-cognitive-search-service"></a>Modificare la capacità di un servizio ricerca cognitiva di Azure

Prima di eseguire il [provisioning di un servizio di ricerca](search-create-service-portal.md) e il blocco in un piano tariffario specifico, è necessario attendere alcuni minuti per comprendere il funzionamento della capacità e come modificare le repliche e le partizioni per gestire la fluttuazione del carico di lavoro.

La capacità è una funzione del [livello scelto](search-sku-tier.md) (i livelli determinano le caratteristiche hardware) e la combinazione di replica e partizione necessaria per i carichi di lavoro proiettati. Una volta creato un servizio, è possibile aumentare o ridurre il numero di repliche o partizioni in modo indipendente. I costi verranno applicati a ogni risorsa fisica aggiuntiva, ma una volta completati i carichi di lavoro di grandi dimensioni, è possibile ridurre la scalabilità per abbassare la fattura. A seconda del livello e della dimensione della regolazione, l'aggiunta o la riduzione della capacità può richiedere da 15 minuti a diverse ore.

Quando si modifica l'allocazione delle repliche e delle partizioni, è consigliabile usare la portale di Azure. Il portale impone limiti per le combinazioni consentite che sono inferiori ai limiti massimi di un livello. Tuttavia, se è necessario un approccio di provisioning basato su script o basato su codice, l' [Azure PowerShell](search-manage-powershell.md) o l' [API REST di gestione](/rest/api/searchmanagement/services) sono soluzioni alternative.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Concetti: unità di ricerca, repliche, partizioni, partizioni

La capacità è espressa *in unità di ricerca* che possono essere allocate in combinazioni di *partizioni* e *repliche*, usando un meccanismo di *partizionamento orizzontale* sottostante per supportare configurazioni flessibili:

| Concetto  | Definizione|
|----------|-----------|
|*Unità di ricerca* | Un singolo incremento della capacità totale disponibile (36 unità). È anche l'unità di fatturazione per un servizio ricerca cognitiva di Azure. Per eseguire il servizio è necessario almeno un'unità.|
|*Replica* | Istanze del servizio di ricerca, utilizzate principalmente per il bilanciamento di carico delle operazioni di query. Ogni replica ospita una copia di un indice. Se si allocano tre repliche, saranno disponibili tre copie di un indice per la manutenzione delle richieste di query.|
|*Partizione* | Archiviazione fisica e I/O per le operazioni di lettura/scrittura, ad esempio per la ricompilazione o l'aggiornamento di un indice. Ogni partizione dispone di una sezione dell'indice totale. Se si allocano tre partizioni, l'indice è suddiviso in terze parti. |
|*Partizione* | Un blocco di un indice. Azure ricerca cognitiva divide ogni indice in partizioni per rendere più veloce il processo di aggiunta delle partizioni (spostando le partizioni in nuove unità di ricerca).|

Il diagramma seguente illustra la relazione tra le repliche, le partizioni, le partizioni e le unità di ricerca. Viene illustrato un esempio di come un singolo indice viene esteso in quattro unità di ricerca in un servizio con due repliche e due partizioni. Ognuna delle quattro unità di ricerca archivia solo la metà delle partizioni dell'indice. Le unità di ricerca nella colonna a sinistra archiviano la prima metà delle partizioni, che comprendono la prima partizione, mentre quelle nella colonna destra archiviano la seconda metà delle partizioni, che comprende la seconda partizione. Poiché sono presenti due repliche, sono presenti due copie di ogni partizione dell'indice. Le unità di ricerca nella parte superiore archiviano una copia, che comprende la prima replica, mentre quelle nella riga inferiore archiviano un'altra copia, che comprende la seconda replica.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Gli indici di ricerca sono partizionati tra le partizioni.":::

Il diagramma precedente è solo un esempio. Sono possibili molte combinazioni di partizioni e repliche, fino a un massimo di 36 unità di ricerca totali.

In ricerca cognitiva, la gestione delle partizioni è un dettaglio di implementazione e non configurabile, ma la conoscenza del partizionamento di un indice consente di comprendere le anomalie occasionali nei comportamenti di classificazione e completamento automatico:

+ Anomalie di classificazione: i punteggi di ricerca vengono calcolati a livello di partizione prima e quindi aggregati in un unico set di risultati. A seconda delle caratteristiche del contenuto della partizione, le corrispondenze di una partizione possono essere classificate in un livello superiore rispetto alle corrispondenze in un altro. Se si notano classificazioni non intuitive nei risultati della ricerca, è molto probabile che si verifichino gli effetti del partizionamento orizzontale, soprattutto se gli indici sono di dimensioni ridotte. È possibile evitare queste anomalie di rango scegliendo di [calcolare i punteggi a livello globale nell'intero indice](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), ma in questo modo si ridurrà una riduzione delle prestazioni.

+ Anomalie di completamento automatico: le query con completamento automatico, in cui vengono eseguite corrispondenze sui primi caratteri di un termine parzialmente immesso, accettano un parametro fuzzy che perdona le piccole deviazioni nell'ortografia. Per il completamento automatico, la corrispondenza fuzzy è vincolata a termini all'interno della partizione corrente. Se, ad esempio, una partizione contiene "Microsoft" e viene immesso un termine parziale di "micor", il motore di ricerca corrisponderà a "Microsoft" nella partizione, ma non in altre partizioni che contengono le parti rimanenti dell'indice.

## <a name="when-to-add-nodes"></a>Quando aggiungere nodi

Inizialmente, a un servizio viene allocato un livello minimo di risorse costituito da una partizione e una replica.

Un singolo servizio deve disporre di risorse sufficienti per gestire tutti i carichi di lavoro (indicizzazione e query). Nessuno dei due carichi di lavoro viene eseguito in background. È possibile pianificare l'indicizzazione per i periodi in cui le richieste di query sono naturalmente meno frequenti, ma il servizio non assegna priorità a un'attività rispetto a un'altra. Inoltre, una certa quantità di ridondanza smussa le prestazioni delle query quando i servizi o i nodi vengono aggiornati internamente.

Come regola generale, le applicazioni di ricerca tendono a avere più repliche rispetto alle partizioni, in particolare quando le operazioni del servizio sono distorte per i carichi di lavoro di query. La sezione relativa alla [disponibilità elevata](#HA) spiega perché.

> [!NOTE]
> L'aggiunta di più repliche o partizioni aumenta il costo di esecuzione del servizio e può introdurre variazioni minime nel modo in cui vengono ordinati i risultati. Assicurarsi di controllare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per comprendere le implicazioni di fatturazione dell'aggiunta di altri nodi. Il [grafico seguente](#chart) può essere utile per fare riferimento incrociato al numero di unità di ricerca necessarie per una configurazione specifica. Per ulteriori informazioni sul modo in cui le repliche aggiuntive influiscano sull'elaborazione delle query, vedere [ordinamento dei risultati](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Come allocare repliche e partizioni

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il servizio di ricerca.

1. In **Impostazioni** aprire la pagina **scala** per modificare le repliche e le partizioni. 

   Lo screenshot seguente mostra un servizio standard di cui è stato effettuato il provisioning con una replica e una partizione. La formula nella parte inferiore indica il numero di unità di ricerca utilizzate (1). Se il prezzo unitario è $100 (non un prezzo reale), il costo mensile per l'esecuzione di questo servizio sarà in media pari a $100.

   ![Pagina scala che mostra i valori correnti](media/search-capacity-planning/1-initial-values.png "Pagina scala che mostra i valori correnti")

1. Utilizzare il dispositivo di scorrimento per aumentare o diminuire il numero di partizioni. La formula nella parte inferiore indica il numero di unità di ricerca utilizzate.

   Questo esempio raddoppia la capacità, con due repliche e partizioni ciascuna. Si noti il numero di unità di ricerca; Ora è quattro perché la formula di fatturazione è rappresentata dalle repliche moltiplicate per le partizioni (2 x 2). Raddoppiare la capacità più di raddoppiare il costo di esecuzione del servizio. Se il costo unitario di ricerca è $100, la nuova fattura mensile sarà $400.

   Per i costi correnti per unità di ogni livello, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/).

   ![Aggiungere repliche e partizioni](media/search-capacity-planning/2-add-2-each.png "Aggiungere repliche e partizioni")

1. Selezionare **Save (Salva** ) per confermare le modifiche.

   ![Confermare le modifiche per la scalabilità e la fatturazione](media/search-capacity-planning/3-save-confirm.png "Confermare le modifiche per la scalabilità e la fatturazione")

   Per il completamento delle modifiche nella capacità sono necessarie diverse ore. Non è possibile annullare una volta che il processo è stato avviato e non è disponibile alcun monitoraggio in tempo reale per le modifiche della replica e della partizione. Tuttavia, il messaggio seguente rimane visibile mentre sono in corso le modifiche.

   ![Messaggio di stato nel portale](media/search-capacity-planning/4-updating.png "Messaggio di stato nel portale")

> [!NOTE]
> Una volta eseguito il provisioning di un servizio, non è possibile aggiornarlo a un livello superiore. È necessario creare un servizio di ricerca a un nuovo livello e ricaricare gli indici. Per informazioni sul provisioning del servizio, vedere [creare un servizio ricerca cognitiva di Azure nel portale](search-create-service-portal.md) .
>
> Inoltre, le partizioni e le repliche vengono gestite esclusivamente e internamente dal servizio. Non esiste un concetto di affinità processori o l'assegnazione di un carico di lavoro a un nodo specifico.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>combinazioni di partizioni e repliche

Il servizio Basic prevede esattamente una partizione e fino a tre repliche, per un massimo di tre unità di ricerca. Le repliche sono l'unica risorsa regolabile. Per la disponibilità elevata relativa alle query è necessario un minimo di due repliche.

Tutti i servizi di ricerca standard e ottimizzati per l'archiviazione possono presupporre le combinazioni di repliche e partizioni seguenti, soggette al limite di 36-SU. 

|   | **1 partizione** | **2 partizioni** | **3 partizioni** | **4 partizioni** | **6 partizioni** | **12 partizioni** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replica.** |1 unità di ricerca |2 unità di ricerca |3 unità di ricerca |4 unità di ricerca |6 unità di ricerca |12 unità di ricerca |
| **2 repliche** |2 unità di ricerca |4 unità di ricerca |6 unità di ricerca |8 unità di ricerca |12 unità di ricerca |24 unità di ricerca |
| **3 repliche** |3 unità di ricerca |6 unità di ricerca |9 unità di ricerca |12 unità di ricerca |18 unità di ricerca |36 unità di ricerca |
| **4 repliche** |4 unità di ricerca |8 unità di ricerca |12 unità di ricerca |16 unità di ricerca |24 unità di ricerca |N/D |
| **5 repliche** |5 unità di ricerca |10 unità di ricerca |15 unità di ricerca |20 unità di ricerca |30 unità di ricerca |N/D |
| **6 repliche** |6 unità di ricerca |12 unità di ricerca |18 unità di ricerca |24 unità di ricerca |36 unità di ricerca |N/D |
| **12 repliche** |12 unità di ricerca |24 unità di ricerca |36 unità di ricerca |N/D |N/D |N/D |

Le unità di ricerca, i prezzi e le capacità sono illustrati in dettaglio nel sito web di Azure. Per ulteriori informazioni, vedere la pagina relativa ai [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Il numero di repliche e partizioni divide equamente per 12 (in particolare 1, 2, 3, 4, 6, 12). Questo perché Azure ricerca cognitiva suddivide ogni indice in 12 partizioni in modo che sia possibile distribuirlo in parti uguali tra tutte le partizioni. Ad esempio, se il servizio ha tre partizioni e si crea un indice, ogni partizione conterrà quattro partizioni dell'indice. La modalità di partizionamento di un indice in Azure ricerca cognitiva è un dettaglio di implementazione, soggetta a modifiche nelle versioni future. Sebbene il numero attualmente sia 12, tale numero potrebbe non essere 12 in futuro.
>

<a id="HA"></a>

## <a name="high-availability"></a>Disponibilità elevata

Poiché è facile e relativamente veloce eseguire la scalabilità verticale, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi eseguire la scalabilità verticale come vengono compilati i volumi di query. I carichi di lavoro di query vengono eseguiti principalmente nelle repliche. Se è necessaria maggiore velocità effettiva o una disponibilità elevata, probabilmente saranno necessarie repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

* Due repliche per la disponibilità elevata di carichi di lavoro di sola lettura, vale a dire query.

* Tre o più repliche per la disponibilità elevata dei carichi di lavoro di lettura e scrittura, vale a dire query e indicizzazione man mano che singoli documenti vengono aggiunti, aggiornati o eliminati.

I contratti di servizio per Azure ricerca cognitiva sono destinati a operazioni di query e a aggiornamenti degli indici che includono l'aggiunta, l'aggiornamento o l'eliminazione di documenti.

Il livello Basic prevede una partizione e fino a tre repliche. Se si vuole avere la flessibilità necessaria per rispondere immediatamente alle fluttuazioni della richiesta di indicizzazione e velocità effettiva di query, prendere in considerazione uno dei piani Standard.  Se i requisiti di archiviazione aumentano molto più rapidamente rispetto alla velocità effettiva delle query, prendere in considerazione uno dei livelli ottimizzati per l'archiviazione.

## <a name="disaster-recovery"></a>Ripristino di emergenza

Attualmente, non esiste alcun meccanismo incorporato per il ripristino di emergenza. L'aggiunta di partizioni o repliche sarebbe una strategia errata per soddisfare gli obiettivi di ripristino di emergenza. Il metodo più comune consiste nell'aggiungere ridondanza a livello di servizio impostando un secondo servizio di ricerca in un'altra area. Come con la disponibilità durante la ricostruzione di un indice, la logica di reindirizzamento o di failover deve provenire dal codice.

## <a name="estimate-replicas"></a>Stimare le repliche

In un servizio di produzione è necessario allocare tre repliche per finalità del contratto di servizio. Se si verifica un rallentamento delle prestazioni delle query, è possibile aggiungere repliche in modo da portare online copie aggiuntive dell'indice per supportare carichi di lavoro di query di dimensioni maggiori e per bilanciare il carico delle richieste su più repliche.

Non sono disponibili linee guida sul numero di repliche necessarie per gestire i carichi di query. Le prestazioni delle query variano a seconda della complessità della query e dei carichi di lavoro in competizione. Sebbene l'aggiunta di repliche consenta certamente di migliorare le prestazioni, il risultato non è strettamente lineare. L'aggiunta di tre repliche, infatti, non garantisce una velocità effettiva triplicata.

Per informazioni aggiuntive sulla stima di query al secondo per la soluzione, vedere [scalabilità per le query di prestazioni](search-performance-optimization.md)e [monitoraggio](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Stima partizioni

Il [livello scelto](search-sku-tier.md) determina la velocità e le dimensioni della partizione e ogni livello viene ottimizzato in base a un set di caratteristiche che si adattano a diversi scenari. Se si sceglie un livello superiore, potrebbe essere necessario un minor numero di partizioni rispetto a S1. Una delle domande a cui è necessario rispondere attraverso test autoindirizzati consiste nel fatto che una partizione più grande e costosa produce prestazioni migliori rispetto a due partizioni meno economiche in un servizio di cui è stato effettuato il provisioning a un livello inferiore.

Le applicazioni di ricerca che richiedono l'aggiornamento dei dati quasi in tempo reale avranno bisogno in proporzione di più partizioni che repliche. L'aggiunta di partizioni distribuisce le operazioni di lettura/scrittura su un numero più ampio di risorse di calcolo. Offre inoltre più spazio su disco per l'archiviazione di documenti e indici aggiuntivi.

L'esecuzione di query su indici di dimensioni maggiori può richiedere tempi più lunghi. Di conseguenza, si noterà che ogni aumento incrementale delle partizioni richiede un aumento delle repliche proporzionale ma più limitato. La complessità e il volume delle query influiscono sulla rapidità di esecuzione delle stesse.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere un piano tariffario per Azure ricerca cognitiva](search-sku-tier.md)