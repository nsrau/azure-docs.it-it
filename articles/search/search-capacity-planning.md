---
title: Ridimensionare le partizioni e le repliche per le query e l'indicizzazione-ricerca di Azure
description: Regolare le risorse di calcolo, ovvero partizioni e repliche, dove il prezzo di ogni risorsa è definito in unità di ricerca fatturabili, in Ricerca di Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c048dcf31d8f434f742d2da9351ef9b46f0a71d4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650072"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Ridimensionare le partizioni e le repliche per i carichi di lavoro di query e indicizzazione in ricerca di Azure
Dopo aver [scelto un piano tariffario](search-sku-tier.md) ed [eseguito il provisioning di un servizio di ricerca](search-create-service-portal.md), il passaggio successivo consente di aumentare il numero di repliche o partizioni usate dal servizio. Ogni livello offre un numero fisso di unità di fatturazione. Questo articolo illustra come assegnare le unità per ottenere una configurazione ottimale che bilanci i requisiti per l'esecuzione di query, indicizzazione e archiviazione.

La configurazione delle risorse è disponibile quando si configura un servizio al [livello Basic](https://aka.ms/azuresearchbasic) o a uno dei [livelli standard o ottimizzati](search-limits-quotas-capacity.md)per l'archiviazione. Per tutti i servizi di questi livelli è possibile acquistare capacità a incrementi di *unità di ricerca* (SU). Le singole partizioni e repliche vengono considerate come una unità di ricerca. 

Usando un numero minore di risultati SU in una fattura proporzionalmente inferiore. La fatturazione è attiva per l'intera durata impostata per il servizio. Se temporaneamente non si usa un servizio, l'unico modo per evitare la fatturazione è eliminare il servizio e quindi ricrearlo quando sarà necessario.

> [!Note]
> Eliminando un servizio si elimina tutto il suo contenuto. Non sono disponibili funzionalità all'interno di Ricerca di Azure per eseguire il backup e il ripristino dei dati di ricerca permanenti. Per ridistribuire un indice esistente in un nuovo servizio è necessario eseguire il programma utilizzato in origine per crearlo e caricarlo. 

## <a name="terminology-replicas-and-partitions"></a>Terminologia: repliche e partizioni
Le repliche e le partizioni sono le risorse primarie che eseguono il backup di un servizio di ricerca.

| Risorsa | Definizione |
|----------|------------|
|*Partitions* | Offre l'archiviazione degli indici e l'I/O per le operazioni di lettura e scrittura, ad esempio durante la compilazione o l'aggiornamento di un indice.|
|*Repliche* | Istanze del servizio di ricerca, utilizzate principalmente per il bilanciamento di carico delle operazioni di query. Ogni replica ospita sempre una copia di un indice. Se si dispone di 12 repliche, si disporrà di 12 copie di ogni indice caricate nel servizio.|

> [!NOTE]
> Non è possibile manipolare o scegliere direttamente quali indici devono essere eseguiti su una replica. L'architettura del servizio include una copia di ogni indice in ogni replica.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Come allocare repliche e partizioni
In Ricerca di Azure viene allocato inizialmente a un servizio un livello minimo di risorse costituito da una partizione e una replica. Per i livelli che lo supportano, è possibile regolare in modo incrementale le risorse di elaborazione aumentando le partizioni se si ha bisogno di maggiore spazio di archiviazione e I/O o aggiungendo più repliche per volumi di query maggiori o migliori prestazioni. Un singolo servizio deve disporre di risorse sufficienti per gestire tutti i carichi di lavoro (indicizzazione e query). Non è possibile suddividere i carichi di lavoro tra più servizi.

Per aumentare o modificare l'allocazione delle repliche e delle partizioni, è consigliabile usare il portale di Azure. Il portale impone limiti per le combinazioni consentite che sono inferiori ai limiti massimi. Se è necessario un approccio di provisioning basato su script o basato sul codice, l' [Azure PowerShell](search-manage-powershell.md) o l' [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/services) sono soluzioni alternative.

In generale le applicazioni di ricerca richiedono più repliche che partizioni, in particolare quando fra le operazioni del servizio prevalgono i carichi di lavoro di query. La sezione relativa alla [disponibilità elevata](#HA) spiega perché.

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il servizio di ricerca.

2. In **Impostazioni**aprire la pagina **scala** per modificare le repliche e le partizioni. 

   Lo screenshot seguente mostra un servizio standard di cui è stato effettuato il provisioning con una replica e una partizione. La formula nella parte inferiore indica il numero di unità di ricerca utilizzate (1). Se il prezzo unitario è $100 (non un prezzo reale), il costo mensile per l'esecuzione di questo servizio sarà in media pari a $100.

   ![Pagina scala che mostra i valori correnti](media/search-capacity-planning/1-initial-values.png "Pagina scala che mostra i valori correnti")

3. Utilizzare il dispositivo di scorrimento per aumentare o diminuire il numero di partizioni. La formula nella parte inferiore indica il numero di unità di ricerca utilizzate.

   Questo esempio raddoppia la capacità, con due repliche e partizioni ciascuna. Si noti il numero di unità di ricerca; Ora è quattro perché la formula di fatturazione è rappresentata dalle repliche moltiplicate per le partizioni (2 x 2). Raddoppiare la capacità più di raddoppiare il costo di esecuzione del servizio. Se il costo unitario di ricerca è $100, la nuova fattura mensile sarà $400.

   Per i costi correnti per unità di ogni livello, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/).

   ![Aggiungere repliche e partizioni](media/search-capacity-planning/2-add-2-each.png "Aggiungere repliche e partizioni")

3. Fare clic su **Salva** per confermare le modifiche.

   ![Confermare le modifiche per la scalabilità e la fatturazione](media/search-capacity-planning/3-save-confirm.png "Confermare le modifiche per la scalabilità e la fatturazione")

   Per il completamento delle modifiche nella capacità sono necessarie diverse ore. Non è possibile annullare una volta che il processo è stato avviato e non è disponibile alcun monitoraggio in tempo reale per le modifiche della replica e della partizione. Tuttavia, il messaggio seguente rimane visibile mentre sono in corso le modifiche.

   ![Messaggio di stato nel portale](media/search-capacity-planning/4-updating.png "Messaggio di stato nel portale")


> [!NOTE]
> Dopo aver effettuato il provisioning di un servizio, non è possibile aggiornarlo passando a uno SKU superiore. È necessario creare un servizio di ricerca a un nuovo livello e ricaricare gli indici. Per assistenza sul provisioning del servizio, vedere [Creare un servizio Ricerca di Azure nel portale](search-create-service-portal.md) .
>
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

Le unità di ricerca, i prezzi e le capacità sono illustrati in dettaglio nel sito web di Azure. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Il numero di repliche e partizioni divide equamente per 12 (in particolare 1, 2, 3, 4, 6, 12). In questo modo Ricerca di Azure divide preventivamente ogni indice in 12 partizioni in modo che possa essere distribuito ugualmente tra tutte le partizioni. Ad esempio, se il servizio ha tre partizioni e si crea un indice, ogni partizione conterrà quattro partizioni dell'indice. Il modo in cui Ricerca di Azure suddivide un indice in partizioni è un dettaglio di implementazione ed è soggetto a modifiche nelle versioni successive. Sebbene il numero attualmente sia 12, tale numero potrebbe non essere 12 in futuro.
>


<a id="HA"></a>

## <a name="high-availability"></a>Disponibilità elevata
Poiché è facile e relativamente veloce eseguire la scalabilità verticale, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi eseguire la scalabilità verticale come vengono compilati i volumi di query. I carichi di lavoro di query vengono eseguiti principalmente nelle repliche. Se è necessaria maggiore velocità effettiva o una disponibilità elevata, probabilmente saranno necessarie repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

* Due repliche per la disponibilità elevata di carichi di lavoro di sola lettura, vale a dire query.

* Tre o più repliche per la disponibilità elevata dei carichi di lavoro di lettura e scrittura, vale a dire query e indicizzazione man mano che singoli documenti vengono aggiunti, aggiornati o eliminati.

I contratti di servizio per Ricerca di Azure sono associati a operazioni di query e aggiornamenti di indici che consistono nell'aggiunta, l'aggiornamento o l'eliminazione di documenti.

Il livello Basic prevede una partizione e fino a tre repliche. Se si vuole avere la flessibilità necessaria per rispondere immediatamente alle fluttuazioni della richiesta di indicizzazione e velocità effettiva di query, prendere in considerazione uno dei piani Standard.  Se i requisiti di archiviazione aumentano molto più rapidamente rispetto alla velocità effettiva delle query, prendere in considerazione uno dei livelli ottimizzati per l'archiviazione.

### <a name="index-availability-during-a-rebuild"></a>Disponibilità degli indici durante la ricompilazione

La disponibilità elevata per Ricerca di Azure riguarda gli aggiornamenti di query e indici che non comportano la ricompilazione di un indice. Se si elimina o si rinomina un campo oppure si modifica il tipo di dati, sarà necessario ricompilare l'indice. A tale scopo, è necessario eliminare l'indice, ricrearlo e ricaricare i dati.

> [!NOTE]
> È possibile aggiungere nuovi campi a un indice di Ricerca di Azure senza ricompilare l'indice. Il valore del nuovo campo sarà null per tutti i documenti già presenti nell'indice.

Per mantenere la disponibilità degli indici durante la ricompilazione, è necessario avere una copia dell'indice con un nome diverso nelle stesso servizio oppure una copia dell'indice con lo stesso nome in un servizio diverso e quindi specificare la logica di reindirizzamento o di failover nel codice.

## <a name="disaster-recovery"></a>Ripristino di emergenza
Attualmente, non esiste alcun meccanismo incorporato per il ripristino di emergenza. L'aggiunta di partizioni o repliche sarebbe una strategia errata per soddisfare gli obiettivi di ripristino di emergenza. Il metodo più comune consiste nell'aggiungere ridondanza a livello di servizio impostando un secondo servizio di ricerca in un'altra area. Come con la disponibilità durante la ricostruzione di un indice, la logica di reindirizzamento o di failover deve provenire dal codice.

## <a name="increase-query-performance-with-replicas"></a>Aumentare le prestazioni delle query con le repliche
La latenza delle query indica che sono necessarie repliche aggiuntive. In genere, un primo passo per migliorare le prestazioni delle query consiste nell'aggiungere altre repliche. Quando si aggiungono le repliche, copie aggiuntive dell'indice vengono portate online per supportare carichi di lavoro di query maggiori e per bilanciare il carico delle richieste su più repliche.

Non è possibile fornire stime precise sulle query al secondo. Le prestazioni, infatti, dipendono dalla complessità della query e dai carichi di lavoro concorrenti. Sebbene l'aggiunta di repliche consenta certamente di migliorare le prestazioni, il risultato non è strettamente lineare. L'aggiunta di tre repliche, infatti, non garantisce una velocità effettiva triplicata.

Per informazioni su come stimare il numero di query al secondo per i carichi di lavoro, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca di Azure](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Aumentare le prestazioni dell'indicizzazione con le partizioni
Le applicazioni di ricerca che richiedono l'aggiornamento dei dati quasi in tempo reale avranno bisogno in proporzione di più partizioni che repliche. L'aggiunta di partizioni distribuisce le operazioni di lettura/scrittura su un numero più ampio di risorse di calcolo. Offre inoltre più spazio su disco per l'archiviazione di documenti e indici aggiuntivi.

L'esecuzione di query su indici di dimensioni maggiori può richiedere tempi più lunghi. Di conseguenza, si noterà che ogni aumento incrementale delle partizioni richiede un aumento delle repliche proporzionale ma più limitato. La complessità e il volume delle query influiscono sulla rapidità di esecuzione delle stesse.


## <a name="next-steps"></a>Passaggi successivi

[Scegliere un piano tariffario per ricerca di Azure](search-sku-tier.md)
