---
title: Regolare la capacità dei carichi di lavoro di query e indici
titleSuffix: Azure Cognitive Search
description: Modificare le risorse del computer di partizione e replica in Ricerca cognitiva di Azure, in cui ogni risorsa ha un prezzo in unità di ricerca fatturabili.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 270ff3c3e8e4cffbb1f4b1987ee497530d0c0982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546252"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Regolare la capacità in Ricerca cognitiva di AzureAdjust capacity in Azure Cognitive Search

Prima di eseguire il provisioning di [un servizio](search-create-service-portal.md) di ricerca e il blocco in un piano tariffario specifico, è necessario alcuni minuti per comprendere il ruolo delle repliche e delle partizioni in un servizio e come è possibile modificare un servizio per soddisfare picchi e cali nella richiesta di risorse.

La capacità è una funzione del [livello scelto](search-sku-tier.md) (i livelli determinano le caratteristiche hardware) e della combinazione di replica e partizione necessaria per i carichi di lavoro previsti. A seconda del livello e delle dimensioni della regolazione, l'aggiunta o la riduzione della capacità può richiedere da 15 minuti a diverse ore. 

Quando si modifica l'allocazione di repliche e partizioni, è consigliabile usare il portale di Azure.When modifying the allocation of replicas and partitions, we recommend using the Azure portal. Il portale impone limiti alle combinazioni consentite che rimangono al di sotto dei limiti massimi di un livello. Tuttavia, se è necessario un approccio di provisioning basato su script o codice, [Azure PowerShell](search-manage-powershell.md) o l'API REST di [gestione](https://docs.microsoft.com/rest/api/searchmanagement/services) sono soluzioni alternative.

## <a name="terminology-replicas-and-partitions"></a>Terminologia: repliche e partizioni

|||
|-|-|
|*Partizioni* | Offre l'archiviazione degli indici e l'I/O per le operazioni di lettura e scrittura, ad esempio durante la compilazione o l'aggiornamento di un indice. Ogni partizione ha una quota dell'indice totale. Se si allocano tre partizioni, l'indice viene suddiviso in terzi. |
|*Repliche* | Istanze del servizio di ricerca, utilizzate principalmente per il bilanciamento di carico delle operazioni di query. Ogni replica è una copia di un indice. Se si allocano tre repliche, saranno disponibili tre copie di un indice per la gestione delle richieste di query.|

## <a name="when-to-add-nodes"></a>Quando aggiungere nodi

Inizialmente, a un servizio viene allocato un livello minimo di risorse costituito da una partizione e una replica. 

Un singolo servizio deve disporre di risorse sufficienti per gestire tutti i carichi di lavoro (indicizzazione e query). Nessuno dei due carichi di lavoro viene eseguito in background. È possibile pianificare l'indicizzazione per gli orari in cui le richieste di query sono naturalmente meno frequenti, ma il servizio non assocerà in altro modo la priorità di un'attività rispetto a un'altra. Inoltre, una certa quantità di ridondanza uniforma le prestazioni delle query quando i servizi o i nodi vengono aggiornati internamente.

Come regola generale, le applicazioni di ricerca tendono ad avere bisogno di più repliche rispetto alle partizioni, in particolare quando le operazioni del servizio sono orientate verso carichi di lavoro di query. La sezione relativa alla [disponibilità elevata](#HA) spiega perché.

> [!NOTE]
> L'aggiunta di più repliche o partizioni aumenta il costo di esecuzione del servizio e può introdurre lievi variazioni nella modalità di ordinaggio dei risultati. Assicurati di controllare il [calcolatore](https://azure.microsoft.com/pricing/calculator/) dei prezzi per comprendere le implicazioni di fatturazione dell'aggiunta di altri nodi. Il [grafico seguente](#chart) consente di fare un riferimento incrociato al numero di unità di ricerca necessarie per una configurazione specifica. Per ulteriori informazioni sull'impatto di repliche aggiuntive sull'elaborazione delle query, vedere [Ordinamento dei risultati.](search-pagination-page-layout.md#ordering-results)

## <a name="how-to-allocate-replicas-and-partitions"></a>Come allocare repliche e partizioni

1. Accedere al [portale](https://portal.azure.com/) di Azure e selezionare il servizio di ricerca.

1. In **Impostazioni**aprire la pagina **Scala** per modificare le repliche e le partizioni. 

   La schermata seguente mostra un servizio standard di cui è stato eseguito il provisioning con una replica e una partizione. La formula in basso indica il numero di unità di ricerca utilizzate (1). Se il prezzo unitario era di 100 dollari (non un prezzo reale), il costo mensile di esecuzione di questo servizio sarebbe di 100 dollari in media.

   ![Pagina Scala con i valori correnti](media/search-capacity-planning/1-initial-values.png "Pagina Scala con i valori correnti")

1. Utilizzare il dispositivo di scorrimento per aumentare o ridurre il numero di partizioni. La formula in basso indica il numero di unità di ricerca utilizzate.

   Questo esempio raddoppia la capacità, con due repliche e partizioni ciascuna. Si noti il numero di unità di ricerca; ora è quattro perché la formula di fatturazione è repliche moltiplicate per partizioni (2 x 2). Raddoppiare la capacità più che raddoppiare il costo di esecuzione del servizio. Se il costo unitario di ricerca era di 100 dollari, la nuova fattura mensile sarebbe ora di 400 dollari.

   Per i costi correnti per unità di ogni livello, visitare la [pagina Prezzi](https://azure.microsoft.com/pricing/details/search/).

   ![Aggiungere repliche e partizioni](media/search-capacity-planning/2-add-2-each.png "Aggiungere repliche e partizioni")

1. Fare clic su **Salva** per confermare le modifiche.

   ![Confermare le modifiche alla scalabilità e alla fatturazione](media/search-capacity-planning/3-save-confirm.png "Confermare le modifiche alla scalabilità e alla fatturazione")

   Il completamento delle modifiche della capacità richiede diverse ore. Non è possibile annullare una volta avviato il processo e non è presente alcun monitoraggio in tempo reale per le regolazioni di replica e partizione. Tuttavia, il messaggio seguente rimane visibile mentre sono in corso le modifiche.

   ![Messaggio di stato nel portale](media/search-capacity-planning/4-updating.png "Messaggio di stato nel portale")

> [!NOTE]
> Dopo il provisioning di un servizio, non può essere aggiornato a un livello superiore. È necessario creare un servizio di ricerca a un nuovo livello e ricaricare gli indici. Per informazioni sul provisioning dei servizi, vedere [Creare un servizio Ricerca cognitiva di Azure nel portale.](search-create-service-portal.md)
>
> Inoltre, le partizioni e le repliche vengono gestite in modo esclusivo e interno dal servizio. Non esiste alcun concetto di affinità del processore o assegnazione di un carico di lavoro a un nodo specifico.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>combinazioni di partizioni e repliche

Il servizio Basic prevede esattamente una partizione e fino a tre repliche, per un massimo di tre unità di ricerca. Le repliche sono l'unica risorsa regolabile. Per la disponibilità elevata relativa alle query è necessario un minimo di due repliche.

Tutti i servizi di ricerca Standard and Storage Optimized possono assumere le seguenti combinazioni di repliche e partizioni, in base al limite di 36 SU. 

|   | **1 partizione** | **2 partizioni** | **3 partizioni** | **4 partizioni** | **6 partizioni** | **12 partizioni** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replica.** |1 unità di ricerca |2 unità di ricerca |3 unità di ricerca |4 unità di ricerca |6 unità di ricerca |12 unità di ricerca |
| **2 repliche** |2 unità di ricerca |4 unità di ricerca |6 unità di ricerca |8 unità di ricerca |12 unità di ricerca |24 unità di ricerca |
| **3 repliche** |3 unità di ricerca |6 unità di ricerca |9 unità di ricerca |12 unità di ricerca |18 unità di ricerca |36 unità di ricerca |
| **4 repliche** |4 unità di ricerca |8 unità di ricerca |12 unità di ricerca |16 unità di ricerca |24 unità di ricerca |N/D |
| **5 repliche** |5 unità di ricerca |10 unità di ricerca |15 unità di ricerca |20 unità di ricerca |30 unità di ricerca |N/D |
| **6 repliche** |6 unità di ricerca |12 unità di ricerca |18 unità di ricerca |24 unità di ricerca |36 unità di ricerca |N/D |
| **12 repliche** |12 unità di ricerca |24 unità di ricerca |36 unità di ricerca |N/D |N/D |N/D |

Le unità di ricerca, i prezzi e le capacità sono illustrati in dettaglio nel sito web di Azure. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Il numero di repliche e partizioni divide equamente per 12 (in particolare 1, 2, 3, 4, 6, 12). Ciò è dovuto al fatto che Ricerca cognitiva di Azure predivide ogni indice in 12 partizioni in modo che possa essere distribuito in parti uguali in tutte le partizioni. Ad esempio, se il servizio ha tre partizioni e si crea un indice, ogni partizione conterrà quattro partizioni dell'indice. Il modo in cui Ricerca cognitiva di Azure partiziona un indice è un dettaglio di implementazione, soggetto a modifiche nelle versioni future. Sebbene il numero attualmente sia 12, tale numero potrebbe non essere 12 in futuro.
>

<a id="HA"></a>

## <a name="high-availability"></a>Disponibilità elevata

Poiché è facile e relativamente veloce eseguire la scalabilità verticale, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi eseguire la scalabilità verticale come vengono compilati i volumi di query. I carichi di lavoro di query vengono eseguiti principalmente nelle repliche. Se è necessaria maggiore velocità effettiva o una disponibilità elevata, probabilmente saranno necessarie repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

* Due repliche per la disponibilità elevata di carichi di lavoro di sola lettura, vale a dire query.

* Tre o più repliche per la disponibilità elevata dei carichi di lavoro di lettura e scrittura, vale a dire query e indicizzazione man mano che singoli documenti vengono aggiunti, aggiornati o eliminati.

I contratti di servizio (SLA) per Ricerca cognitiva di Azure sono destinati alle operazioni di query e agli aggiornamenti dell'indice che consistono nell'aggiunta, nell'aggiornamento o nell'eliminazione di documenti.

Il livello Basic prevede una partizione e fino a tre repliche. Se si vuole avere la flessibilità necessaria per rispondere immediatamente alle fluttuazioni della richiesta di indicizzazione e velocità effettiva di query, prendere in considerazione uno dei piani Standard.  Se si rilevano che i requisiti di archiviazione stanno crescendo molto più rapidamente rispetto alla velocità effettiva delle query, prendere in considerazione uno dei livelli ottimizzati per l'archiviazione.

## <a name="disaster-recovery"></a>Ripristino di emergenza

Attualmente, non esiste alcun meccanismo incorporato per il ripristino di emergenza. L'aggiunta di partizioni o repliche sarebbe una strategia errata per soddisfare gli obiettivi di ripristino di emergenza. Il metodo più comune consiste nell'aggiungere ridondanza a livello di servizio impostando un secondo servizio di ricerca in un'altra area. Come con la disponibilità durante la ricostruzione di un indice, la logica di reindirizzamento o di failover deve provenire dal codice.

## <a name="estimate-replicas"></a>Stimare le repliche

In un servizio di produzione, è necessario allocare tre repliche per scopi di contratto di servizio. Se si verifica un rallentamento delle prestazioni delle query, è possibile aggiungere repliche in modo che vengano portate in linea copie aggiuntive dell'indice per supportare carichi di lavoro di query più grandi e per bilanciare il carico delle richieste su più repliche.

Non vengono fornite linee guida sul numero di repliche necessarie per supportare i caricamenti di query. Le prestazioni delle query dipendono dalla complessità della query e dei carichi di lavoro concorrenti. Sebbene l'aggiunta di repliche consenta certamente di migliorare le prestazioni, il risultato non è strettamente lineare. L'aggiunta di tre repliche, infatti, non garantisce una velocità effettiva triplicata.

Per indicazioni sulla stima del QPS per la soluzione, vedere [Scalabilità delle query di prestazioni](search-performance-optimization.md)e [monitoraggioFor](search-monitor-queries.md) guidance in estimating QPS for your solution, see Scale for performance and Monitor queries

## <a name="estimate-partitions"></a>Stimare le partizioni

Il [livello scelto](search-sku-tier.md) determina le dimensioni e la velocità della partizione e ogni livello viene ottimizzato in base a un set di caratteristiche che si adattano a vari scenari. Se si sceglie un livello di fascia alta, potrebbe essere necessario un numero inferiore di partizioni rispetto a S1. Una delle domande a cui è necessario rispondere tramite test autogestiti è se una partizione più grande e più costosa offre prestazioni migliori rispetto a due partizioni più economiche in un servizio di cui è stato eseguito il provisioning a un livello inferiore.

Le applicazioni di ricerca che richiedono l'aggiornamento dei dati quasi in tempo reale avranno bisogno in proporzione di più partizioni che repliche. L'aggiunta di partizioni distribuisce le operazioni di lettura/scrittura su un numero più ampio di risorse di calcolo. Offre inoltre più spazio su disco per l'archiviazione di documenti e indici aggiuntivi.

L'esecuzione di query su indici di dimensioni maggiori può richiedere tempi più lunghi. Di conseguenza, si noterà che ogni aumento incrementale delle partizioni richiede un aumento delle repliche proporzionale ma più limitato. La complessità e il volume delle query influiscono sulla rapidità di esecuzione delle stesse.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere un piano tariffario per Ricerca cognitiva di AzureChoose a pricing tier for Azure Cognitive Search](search-sku-tier.md)