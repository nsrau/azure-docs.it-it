---
title: Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro in Ricerca di Azure | Microsoft Docs
description: La pianificazione della capacità in Ricerca di Azure si basa su combinazioni di risorse di calcolo costituite da partizioni e repliche, in cui ogni risorsa è quotata in unità di ricerca fatturabili.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal

ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: heidist

---
# Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro in Ricerca di Azure
Dopo aver [scelto uno SKU](search-sku-tier.md) ed [eseguito il prvisioning di un servizio di ricerca](search-create-service-portal.md), il passaggio successivo è configurare facoltativamente le risorse di servizio.

In Ricerca di Azure viene allocato inizialmente a un servizio un livello minimo di risorse costituito da una partizione e una replica. Per i livelli che lo supportano, è possibile regolare in modo incrementale le risorse di elaborazione aumentando le partizioni se si ha bisogno di maggiore spazio di archiviazione e IO o di repliche per volumi di query maggiori o migliori prestazioni. Un singolo servizio deve disporre di risorse sufficienti per gestire tutti i carichi di lavoro (indicizzazione e query). Non è possibile suddividere i carichi di lavoro tra più servizi.

Le impostazioni di scalabilità sono disponibili se si effettua il provisioning di un servizio fatturabile al [livello Basic](http://aka.ms/azuresearchbasic) o a uno dei [livelli Standard](search-limits-quotas-capacity.md). Per gli SKU fatturabili le *unità di ricerca* (SU) rappresentano gli incrementi di capacità acquistabili. Ogni partizione e ogni replica è considerata come un'unità di ricerca. Rimanendo al di sotto dei limiti massimi si usano meno unità di ricerca con una fattura proporzionalmente inferiore. La fatturazione è attiva per l'intera durata del provisioning del servizio. Se temporaneamente non si usa un servizio, l'unico modo per evitare la fatturazione è eliminare il servizio e quindi ricrearlo in un secondo momento quando sarà necessario.

Per aumentare o modificare l'allocazione delle repliche e delle partizioni, si consiglia di usare il portale. Il portale applicherà limiti alle combinazioni consentite inferiori ai limiti massimi:

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il servizio di ricerca.
2. In Impostazioni aprire il pannello Scala e usare i dispositivi di scorrimento per aumentare o ridurre il numero di partizioni e repliche.

Come regola generale, le applicazioni di ricerca richiedono più repliche che partizioni, in particolare quando fra le operazioni del servizio prevalgono i carichi di lavoro di query. La sezione relativa alla [disponibilità elevata](#HA) spiega perché.

> [!NOTE]
> Una volta eseguito il provisioning di un servizio, non è possibile aggiornarlo sul posto a uno SKU superiore. È necessario creare un nuovo servizio di ricerca a un nuovo livello e ricaricare gli indici. Per assistenza sul provisioning del servizio, vedere [Creare un servizio Ricerca di Azure nel portale](search-create-service-portal.md).
> 
> 

## Terminologia: partizioni e repliche
Partizioni e repliche sono le risorse primarie alla base di un servizio di ricerca.

Le **partizioni** offrono l'archiviazione degli indici e l'I/O per le operazioni di lettura e scrittura (ad esempio durante la compilazione o l'aggiornamento di un indice).

Le **repliche** sono istanze del servizio di ricerca, usate principalmente per il bilanciamento di carico delle operazioni di query. Ogni replica ospita sempre una copia di un indice. Se si dispone di 12 repliche, si disporrà di 12 copie di ogni indice caricate nel servizio.

> [!NOTE]
> Non è possibile manipolare o scegliere direttamente quali indici devono essere eseguiti su una replica. L'architettura del servizio include una copia di ogni indice in ogni replica.
> 
> 

<a id="HA"></a>

## Disponibilità elevata
Poiché ridimensionare in aumento è facile e relativamente veloce, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi ridimensionare in aumento quando vengono compilati i volumi di query, fino alle repliche e partizioni massime supportate dallo SKU. Per molti servizi al livello Basic o S1, una partizione fornisce archiviazione e I/O sufficienti (15 milioni di documenti per partizione).

I carichi di lavoro delle query vengono eseguiti principalmente sulle repliche. Se è necessaria maggiore velocità effettiva o elevata disponibilità, si richiederanno probabilmente repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

* 2 repliche per la disponibilità elevata dei carichi di lavoro di sola lettura (query)
* 3 o più repliche per la disponibilità elevata dei carichi di lavoro di lettura e scrittura (query e indicizzazione al momento dell'aggiunta, aggiornamento ed eliminazione di singoli documenti)

I contratti di livello di servizio (SLA) per Ricerca di Azure sono basati sulle operazioni di query e sugli aggiornamenti degli indici che consistono di aggiunta, aggiornamento o eliminazione di documenti.

**Disponibilità degli indici durante la ricompilazione**

La disponibilità elevata per Ricerca di Azure riguarda gli aggiornamenti di query e indici che non comportano la ricompilazione di un indice. Se l'indice richiede una ricompilazione, ad esempio se si aggiunge o si elimina un campo, si modifica un tipo di dati o si rinomina un campo, è necessario ricompilare l'indice mediante le operazioni seguenti: eliminare l'indice, ricreare l'indice e ricaricare i dati.

Per mantenere la disponibilità degli indici durante la ricompilazione, è necessario disporre di una seconda copia dell'indice già in produzione sullo stesso servizio (con un altro nome) o di un indice con lo stesso nome su un servizio diverso e poi fornire la logica di reindirzzamento o di failover nel codice.

## Ripristino di emergenza
Attualmente, non esiste alcun meccanismo incorporato per il ripristino di emergenza. L'aggiunta di partizioni o repliche sarebbe una strategia errata per soddisfare gli obiettivi di ripristino di emergenza. Il metodo più comune consiste nell'aggiungere ridondanza a livello di servizio con il provisioning di un secondo servizio di ricerca in un'altra area. Come con la disponibilità durante la ricostruzione di un indice, la logica di reindirizzamento o di failover deve provenire dal codice.

## Aumentare le prestazioni delle query con le repliche
La latenza delle query indica che sono necessarie repliche aggiuntive. In genere, un primo passo per migliorare le prestazioni delle query consiste nell'aggiungere una maggiore quantità di questa risorsa. Quando si aggiungono le repliche, copie aggiuntive dell'indice vengono portate online per supportare carichi di lavoro di query maggiori e per bilanciare il carico delle richieste su più repliche.

Si noti che non è possibile fornire stime precise sulle query al secondo. Le prestazioni, infatti, dipendono dalla complessità della query e dai carichi di lavoro concorrenti. In media, una replica agli SKU Basic o S1 può soddisfare circa 15 QPS, ma la velocità effettiva sarà leggermente superiore o inferiore a seconda della complessità della query (le query con facet sono più complesse) e della latenza di rete. Inoltre, è importante riconoscere che mentre l'aggiunta delle repliche aggiunge in modo definito scalabilità e prestazioni, il risultato finale non è strettamente lineare: l'aggiunta di 3 repliche non garantisce di triplicare la velocità effettiva.

Per altre informazioni sulle query al secondo, inclusi i metodi per la stima di tale valore in funzione dei carichi di lavoro, vedere [Gestire il servizio di ricerca](search-manage.md).

## Aumentare le prestazioni dell'indicizzazione con le partizioni
Le applicazioni di ricerca che richiedono l'aggiornamento dei dati quasi in tempo reale avranno bisogno in proporzione di più partizioni che repliche. L'aggiunta di partizioni distribuisce le operazioni di lettura/scrittura su un numero più ampio di risorse di calcolo. Offre inoltre più spazio su disco per l'archiviazione di documenti e indici aggiuntivi.

L'esecuzione di query su indici di dimensioni maggiori può richiedere tempi più lunghi. Di conseguenza, si noterà che ogni aumento incrementale delle partizioni richiede un aumento delle repliche proporzionale ma più limitato. Come accennato in precedenza, la complessità delle query e il volume di query influiranno sulla rapidità di esecuzione delle query.

## Livello Basic: combinazioni di partizioni e repliche
Il servizio Basic prevede esattamente 1 partizione e fino a 3 repliche, per un massimo di 3 unità di ricerca. Le repliche sono l'unica risorsa regolabile. Come accennato in precedenza, per la disponibilità elevata relativa alle query è necessario un minimo di 2 repliche.

<a id="chart"></a>

## Livello Standard: combinazioni di partizioni e repliche
Questa tabella mostra le unità di ricerca necessarie per supportare le combinazioni di repliche e di partizioni entro il limite di 36 unità di ricerca (SU) (sono esclusi i livelli Basic e S3 HD).

| - | - | - | - | - | - | - |
| --- | --- | --- | --- | --- | --- | --- |
| **12 repliche** |12 unità di ricerca |24 unità di ricerca |36 unità di ricerca |N/D |N/D |N/D |
| **6 repliche** |6 unità di ricerca |12 unità di ricerca |18 unità di ricerca |24 unità di ricerca |36 unità di ricerca |N/D |
| **5 repliche** |5 unità di ricerca |10 unità di ricerca |15 unità di ricerca |20 unità di ricerca |30 unità di ricerca |N/D |
| **4 repliche** |4 unità di ricerca |8 unità di ricerca < |12 unità di ricerca |16 unità di ricerca |24 unità di ricerca |N/D |
| **3 repliche** |3 unità di ricerca |6 unità di ricerca |9 unità di ricerca |12 unità di ricerca |18 unità di ricerca |36 unità di ricerca |
| **2 repliche** |2 unità di ricerca |4 unità di ricerca |6 unità di ricerca |8 unità di ricerca |12 unità di ricerca |24 unità di ricerca |
| **1 replica.** |1 unità di ricerca |2 unità di ricerca |3 unità di ricerca |4 unità di ricerca |6 unità di ricerca |12 unità di ricerca |
| N/D |**1 partizione** |**2 partizioni** |**3 partizioni** < |**4 partizioni** |**6 partizioni** |**12 partizioni** |

Le unità di ricerca, i prezzi e le capacità sono illustrati in dettaglio nel sito web di Azure. Per altre informazioni, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Il numero di repliche e partizioni si deve dividere equamente per 12 (in particolare 1, 2, 3, 4, 6, 12). In questo modo Ricerca di Azure divide preventivamente ogni indice in 12 partizioni in modo che possa essere distribuito ugualmente tra tutte le partizioni. Ad esempio, se il servizio dispone di tre partizioni e si crea un nuovo indice, ogni partizione conterrà 4 partizioni dell'indice. Come viene suddiviso in partizioni un indice in Ricerca di Azure è un dettaglio di implementazione, soggetto a modifiche nella prossima versione. Sebbene il numero attualmente sia 12, tale numero potrebbe non essere 12 in futuro.
> 
> 

## S3 High Density: combinazioni di partizioni e repliche
S3 HD prevede 1 partizione e fino a 12 repliche, per un massimo di 12 unità di ricerca. Le repliche sono l'unica risorsa regolabile.

## Calcolare le unità di ricerca per combinazioni di ricerca specifiche: R X P = SU
La formula per calcolare il numero di SU necessarie prevede la moltiplicazione del numero di repliche per il numero di partizioni. Ad esempio, 3 repliche moltiplicate per 3 partizioni vengono fatturate come 9 unità di ricerca.

Entrambi i livelli partono da una replica e da una partizione, conteggiate come una unità di ricerca (SU, Search Unit). Questo è l'unico caso in cui una replica e una partizione vengono considerate come una sola unità di ricerca. Ogni risorsa aggiuntiva, che si tratti di una replica o di una partizione, viene conteggiata come SU specifica.

Il costo per SU è determinato dal livello. Il costo per SU è più basso al livello Basic rispetto al livello Standard. Le tariffe per ogni livello sono disponibili in [Prezzi di Ricerca](https://azure.microsoft.com/pricing/details/search/).

<!---HONumber=AcomDC_0914_2016-->