---
title: "Pianificazione della capacità per Ricerca di Azure | Documentazione Microsoft"
description: "Regolare le risorse di calcolo, ovvero partizioni e repliche, dove il prezzo di ogni risorsa è definito in unità di ricerca fatturabili, in Ricerca di Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 1dc16afe-56f9-439d-8874-1733ae1a2b74
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/08/2017
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: 08682b7986cc2210ed21f254e2a9a63b5355e583
ms.openlocfilehash: bfed40417d800e86de7ef437c42162b1e1a0d886
ms.contentlocale: it-it
ms.lasthandoff: 02/24/2017

---

# <a name="scale-resource-levels-for-query-and-indexing-workloads-in-azure-search"></a>Ridimensionare i livelli di risorse per i carichi di lavoro di indicizzazione e query in Ricerca di Azure
Dopo aver [scelto un piano tariffario](search-sku-tier.md) ed [eseguito il provisioning di un servizio di ricerca](search-create-service-portal.md), il passaggio successivo consente di aumentare il numero di repliche o partizioni usate dal servizio. Ogni livello offre un numero fisso di unità di fatturazione. Questo articolo illustra come assegnare le unità per ottenere una configurazione ottimale che bilanci i requisiti per l'esecuzione di query, indicizzazione e archiviazione.

La configurazione delle risorse è disponibile se si imposta un servizio al [livello Basic](http://aka.ms/azuresearchbasic) o a uno dei [livelli Standard](search-limits-quotas-capacity.md). Per tutti i servizi fatturabili di questi livelli è possibile acquistare capacità a incrementi di *unità di ricerca* (SU). Le singole partizioni e repliche vengono considerate come una unità di ricerca. 

Usando un numero minore di risultati SU in una fattura proporzionalmente inferiore. La fatturazione è attiva per l'intera durata impostata per il servizio. Se temporaneamente non si usa un servizio, l'unico modo per evitare la fatturazione è eliminare il servizio e quindi ricrearlo quando sarà necessario.

> [!Note]
> Eliminando un servizio si elimina tutto il suo contenuto. Non sono disponibili funzionalità all'interno di Ricerca di Azure per eseguire il backup e il ripristino dei dati di ricerca permanenti. Per ridistribuire un indice esistente in un nuovo servizio è necessario eseguire il programma utilizzato in origine per crearlo e caricarlo. 

## <a name="terminology-partitions-and-replicas"></a>Terminologia: partizioni e repliche
Partizioni e repliche sono le risorse primarie alla base di un servizio di ricerca.

| Risorsa | Definizione |
|----------|------------|
|*Partizioni* | Offre l'archiviazione degli indici e l'I/O per le operazioni di lettura e scrittura, ad esempio durante la compilazione o l'aggiornamento di un indice.|
|*Repliche* | Istanze del servizio di ricerca, utilizzate principalmente per il bilanciamento di carico delle operazioni di query. Ogni replica ospita sempre una copia di un indice. Se si dispone di 12 repliche, si disporrà di 12 copie di ogni indice caricate nel servizio.|

> [!NOTE]
> Non è possibile manipolare o scegliere direttamente quali indici devono essere eseguiti su una replica. L'architettura del servizio include una copia di ogni indice in ogni replica.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Modalità di allocazione di partizioni e repliche
In Ricerca di Azure viene allocato inizialmente a un servizio un livello minimo di risorse costituito da una partizione e una replica. Per i livelli che lo supportano, è possibile regolare in modo incrementale le risorse di elaborazione aumentando le partizioni se si ha bisogno di maggiore spazio di archiviazione e I/O o aggiungendo più repliche per volumi di query maggiori o migliori prestazioni. Un singolo servizio deve disporre di risorse sufficienti per gestire tutti i carichi di lavoro (indicizzazione e query). Non è possibile suddividere i carichi di lavoro tra più servizi.

Per aumentare o modificare l'allocazione delle repliche e delle partizioni, è consigliabile usare il portale di Azure. Il portale applica limiti alle combinazioni consentite inferiori ai limiti massimi:

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il servizio di ricerca.
2. In **Impostazioni** aprire il pannello **Piano** e usare i dispositivi di scorrimento per aumentare o ridurre il numero di partizioni e repliche.

Se è necessario un approccio di provisioning basato script o su codice, invece del portale è possibile usare l'[API REST di gestione](https://msdn.microsoft.com/library/azure/dn832687.aspx).

In generale le applicazioni di ricerca richiedono più repliche che partizioni, in particolare quando fra le operazioni del servizio prevalgono i carichi di lavoro di query. La sezione relativa alla [disponibilità elevata](#HA) spiega perché.

> [!NOTE]
> Dopo aver effettuato il provisioning di un servizio, non è possibile aggiornarlo passando a uno SKU superiore. È necessario creare un servizio di ricerca a un nuovo livello e ricaricare gli indici. Per assistenza sul provisioning del servizio, vedere [Creare un servizio Ricerca di Azure nel portale](search-create-service-portal.md) .
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Disponibilità elevata
Poiché è facile e relativamente veloce eseguire la scalabilità verticale, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi eseguire la scalabilità verticale come vengono compilati i volumi di query. Per molti servizi al livello Basic o S1, una partizione offre risorse di archiviazione e I/O sufficienti, con 15 milioni di documenti per partizione.

I carichi di lavoro di query vengono eseguiti principalmente nelle repliche. Se è necessaria maggiore velocità effettiva o una disponibilità elevata, probabilmente saranno necessarie repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

* Due repliche per la disponibilità elevata di carichi di lavoro di sola lettura, vale a dire query.
* Tre o più repliche per la disponibilità elevata dei carichi di lavoro di lettura e scrittura, vale a dire query e indicizzazione man mano che singoli documenti vengono aggiunti, aggiornati o eliminati.

I contratti di servizio per Ricerca di Azure sono associati a operazioni di query e aggiornamenti di indici che consistono nell'aggiunta, l'aggiornamento o l'eliminazione di documenti.

### <a name="index-availability-during-a-rebuild"></a>Disponibilità degli indici durante la ricompilazione

La disponibilità elevata per Ricerca di Azure riguarda gli aggiornamenti di query e indici che non comportano la ricompilazione di un indice. Se si elimina o si rinomina un campo oppure si modifica il tipo di dati, sarà necessario ricompilare l'indice. A tale scopo, è necessario eliminare l'indice, ricrearlo e ricaricare i dati.

> [!NOTE]
> È possibile aggiungere nuovi campi a un indice di Ricerca di Azure senza ricompilare l'indice. Il valore del nuovo campo sarà null per tutti i documenti già presenti nell'indice.

Per mantenere la disponibilità degli indici durante la ricompilazione, è necessario avere una copia dell'indice con un nome diverso nelle stesso servizio oppure una copia dell'indice con lo stesso nome in un servizio diverso e quindi specificare la logica di reindirizzamento o di failover nel codice.

## <a name="disaster-recovery"></a>Ripristino di emergenza
Attualmente, non esiste alcun meccanismo incorporato per il ripristino di emergenza. L'aggiunta di partizioni o repliche sarebbe una strategia errata per soddisfare gli obiettivi di ripristino di emergenza. Il metodo più comune consiste nell'aggiungere ridondanza a livello di servizio impostando un secondo servizio di ricerca in un'altra area. Come con la disponibilità durante la ricostruzione di un indice, la logica di reindirizzamento o di failover deve provenire dal codice.

## <a name="increase-query-performance-with-replicas"></a>Aumentare le prestazioni delle query con le repliche
La latenza delle query indica che sono necessarie repliche aggiuntive. In genere, un primo passo per migliorare le prestazioni delle query consiste nell'aggiungere altre repliche. Quando si aggiungono le repliche, copie aggiuntive dell'indice vengono portate online per supportare carichi di lavoro di query maggiori e per bilanciare il carico delle richieste su più repliche.

Non è possibile fornire stime precise sulle query al secondo. Le prestazioni, infatti, dipendono dalla complessità della query e dai carichi di lavoro concorrenti. In media, una replica agli SKU Basic o S1 può soddisfare circa 15 QPS, ma la velocità effettiva sarà leggermente superiore o inferiore a seconda della complessità della query (le query con facet sono più complesse) e della latenza di rete. È importante sapere che mentre l'aggiunta di repliche consente certamente di migliorare la scalabilità e le prestazioni, il risultato non è strettamente lineare. Vale a dire che l'aggiunta di tre repliche non garantisce una velocità effettiva triplicata.

Per altre informazioni sulle query al secondo, inclusi i metodi per la stima di tale valore in funzione dei carichi di lavoro, vedere [Gestire il servizio di ricerca](search-manage.md).

## <a name="increase-indexing-performance-with-partitions"></a>Aumentare le prestazioni dell'indicizzazione con le partizioni
Le applicazioni di ricerca che richiedono l'aggiornamento dei dati quasi in tempo reale avranno bisogno in proporzione di più partizioni che repliche. L'aggiunta di partizioni distribuisce le operazioni di lettura/scrittura su un numero più ampio di risorse di calcolo. Offre inoltre più spazio su disco per l'archiviazione di documenti e indici aggiuntivi.

L'esecuzione di query su indici di dimensioni maggiori può richiedere tempi più lunghi. Di conseguenza, si noterà che ogni aumento incrementale delle partizioni richiede un aumento delle repliche proporzionale ma più limitato. La complessità e il volume delle query influiscono sulla rapidità di esecuzione delle stesse.

## <a name="basic-tier-partition-and-replica-combinations"></a>Livello Basic: combinazioni di partizioni e repliche
Il servizio Basic prevede esattamente una partizione e fino a tre repliche, per un massimo di tre unità di ricerca. Le repliche sono l'unica risorsa regolabile. Per la disponibilità elevata relativa alle query è necessario un minimo di due repliche.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Livello Standard: combinazioni di partizioni e repliche
La tabella seguente mostra le unità di ricerca necessarie per supportare combinazioni di repliche e partizioni entro il limite di 36 unità di ricerca, per tutti i livelli Standard.

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
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Formula di fatturazione per le risorse di replica e partizione
La formula per il calcolo del numero di unità di ricerca usate per combinazioni specifiche è il prodotto delle repliche e delle partizioni, vale a dire (R X P = SU). Ad esempio, tre repliche moltiplicate per tre partizioni vengono fatturate come nove unità di ricerca.

Il costo per SU è determinato dal livello, con una tariffa di fatturazione per unità inferiore per il livello Basic rispetto al livello Standard. Le tariffe per ogni livello sono disponibili in [Prezzi di Ricerca](https://azure.microsoft.com/pricing/details/search/).

