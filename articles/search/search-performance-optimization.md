---
title: Strategie di distribuzione e le procedure consigliate per ottimizzare le prestazioni - ricerca di Azure
description: Informazioni tecniche e procedure consigliate per l'ottimizzazione delle prestazioni di Ricerca di Azure e la configurazione ottimale per la scalabilità.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 32352a857f0a74dc008dc1ad76b4a5951a36b956
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024561"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Strategie di distribuzione e le procedure consigliate per ottimizzare le prestazioni in ricerca di Azure

Questo articolo descrive le procedure consigliate per scenari avanzati con requisiti sofisticati per la scalabilità e disponibilità. 

## <a name="develop-baseline-numbers"></a>Sviluppare i numeri di riferimento
L'ottimizzazione delle prestazioni di ricerca, è consigliabile concentrarsi sul riducendo il tempo di esecuzione di query. A tale scopo, è necessario sapere come appare un carico di query tipica. Le linee guida seguenti consentono di arrivare a numeri di query della linea di base.

1. Selezione una latenza di destinazione (o la quantità massima di tempo) che una ricerca tipica richiede per il completamento.
2. Creare e testare un carico di lavoro reale nel servizio di ricerca con un set di dati reale per misurare i tassi di latenza.
3. Iniziare con un numero ridotto di query al secondo (QPS) e aumentare gradualmente il numero di eseguite i test di fino a quando la latenza delle query scende sotto la latenza di destinazione definito. Si tratta di un benchmark importante per la pianificazione della scalabilità man mano che aumenta l'uso dell'applicazione.
4. Se possibile, riusare le connessioni HTTP. Se si usa .NET SDK di Ricerca di Azure, è necessario riusare un'istanza o un'istanza [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient). Se si usa l'API REST, è necessario riusare un singolo HttpClient.
5. Variare la sostanza di richieste di query in modo che la ricerca avviene su parti diverse dell'indice. Variazione è importante perché se si eseguono continuamente le stesse richieste di ricerca, la memorizzazione nella cache dei dati inizierà a migliorare le prestazioni migliori rispetto al potrebbe con una query più eterogeneo impostata.
6. Variare la struttura delle richieste di query in modo da ottenere diversi tipi di query. Non tutte le query di ricerca esegue lo stesso livello. Ad esempio, un suggerimento di ricerca o la ricerca di documenti è in genere più veloce rispetto a una query con un numero significativo di facet e filtri. Composizione di test deve includere varie query, in circa i rapporti stesso come previsto nell'ambiente di produzione.  

Durante la creazione di questi carichi di lavoro di test, è opportuno considerare alcune caratteristiche di Ricerca di Azure:

+ È possibile eseguire l'overload del servizio eseguendo il push di troppe query di ricerca in una sola volta. In questo caso, vengono visualizzati i codici di risposta HTTP 503. Per evitare un 503 durante il test, iniziare con vari intervalli di richieste di ricerca per visualizzare le differenze nei tassi di latenza quando si aggiungono nuove richieste di ricerca.

+ Ricerca di Azure non esegue attività di indicizzazione in background. Se il servizio gestisce la query e indicizzazione dei carichi di lavoro contemporaneamente, tenerne conto introducendo entrambi l'indicizzazione di processi dei test di query oppure esplorando le opzioni per l'esecuzione di processi di indicizzazione durante gli orari.

> [!NOTE]
> [Il test di carico di Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) è un metodo molto valido per eseguire i test di benchmark in quanto consente di eseguire le richieste HTTP nello stesso modo richiesto per l'esecuzione di query con Ricerca di Azure e consente la parallelizzazione delle richieste.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Scalabilità per il volume di query elevati e richieste limitate
Quando si ricevono troppe richieste limitate o si superano i tassi di latenza di destinazione da un carico maggiore di query, è possibile considerare di ridurre il tasso di latenza in uno dei due modi:

1. **Aumentare le repliche:**  una replica è una copia dei dati che consente a Ricerca di Azure di bilanciare il carico delle richieste tra più copie.  Il bilanciamento del carico e la replica dei dati tra le repliche vengono gestiti da Ricerca di Azure ed è possibile modificare in qualsiasi momento il numero delle repliche allocate per il servizio.  È possibile allocare fino a 12 repliche in un servizio di ricerca Standard e 3 repliche in un servizio di ricerca Basic. Le repliche possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).
2. **Aumentare il livello di Ricerca:**  Ricerca di Azure è disponibile in [diversi livelli](https://azure.microsoft.com/pricing/details/search/) , ognuno dei quali offre diversi livelli di prestazioni.  In alcuni casi, è possibile che l'utente disponga di un numero talmente elevato di query che il livello a cui appartiene non fornisce tassi di latenza sufficientemente bassi, anche quando si raggiunge il limite massimo delle repliche. In questo caso, è possibile considerare di usare uno dei livelli di ricerca maggiore, ad esempio il livello S3 di ricerca di Azure che è adatto per scenari con un numero elevato di documenti e i carichi di lavoro di query estremamente elevati.

## <a name="scaling-for-slow-individual-queries"></a>Scalabilità per le singole query lente
Un altro motivo per i tassi di latenza elevata è una singola query richiedono troppo tempo per completare. In questo caso, l'aggiunta delle repliche non consentirà. Le opzioni possibili che potrebbero essere utili i seguenti due opzioni:

1. **Aumentare le partizioni**: una partizione è un meccanismo per la suddivisione dei dati tra le risorse aggiuntive. Aggiunta di una seconda partizione suddivide i dati in due, una terza partizione suddivide in tre e così via. Un effetto positivo è che le query più lente a volte eseguono più rapidamente a causa dell'elaborazione parallela. Abbiamo osservato che la parallelizzazione nella query a selettività ridotta, ad esempio le query che corrispondono a molti documenti o i facet che consente un numero elevato di documenti con i conteggi. Poiché un'elevata elaborazione è necessario per valutare la rilevanza dei documenti o per contare il numero dei documenti, aggiunta di altre partizioni consente query completare più rapidamente.  
   
   Il servizio di ricerca Standard offe fino a un massimo di 12 partizioni, mentre il servizio Basic offre 1 partizione.  Le partizioni possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).

2. **Limitare i campi a cardinalità elevata:** Un campo a cardinalità elevata è costituito da un facet o un campo filtrabile che dispone di un numero significativo di valori univoci e di conseguenza, vengono utilizzate molte risorse durante il calcolo dei risultati. Ad esempio, l'impostazione di un campo ID prodotto o la descrizione come facet/filtrabile viene conteggiata come un'elevata cardinalità perché la maggior parte dei valori dal documento al documento siano univoca. Se possibile, limitare il numero di campi a cardinalità elevata.

3. **Aumentare il livello di Ricerca:**  il passaggio a un livello superiore di Ricerca di Azure può essere un altro modo per migliorare le prestazioni delle query lente. Ogni livello superiore fornisce più veloci di CPU e memoria, che dispongono di un impatto positivo sulle prestazioni delle query.

## <a name="scaling-for-availability"></a>Ridimensionamento per la disponibilità
Le repliche non solo consentono di ridurre la latenza delle query ma possono anche favorire una disponibilità elevata. Con una singola replica, è necessario prevedere un tempo di inattività periodico dovuto al riavvio del server dopo gli aggiornamenti software o per altri interventi di manutenzione.  Di conseguenza, è importante considerare se l'applicazione richiede disponibilità elevata di ricerche, ovvero query, e operazioni di scrittura, ovvero eventi indicizzazione. Ricerca di Azure offre opzioni di Contratto di servizio in tutte le offerte a pagamento, con le caratteristiche seguenti:

* 2 repliche per la disponibilità elevata dei carichi di lavoro di sola lettura (query)
* 3 o più repliche per la disponibilità elevata dei carichi di lavoro di lettura/scrittura (query e indicizzazione)

Per ulteriori informazioni, visitare il [Contratto di Servizio per Ricerca di Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Poiché le repliche sono copie dei dati, presenza di più repliche consente a ricerca di Azure riavviare le macchine e manutenzione su una replica, consentendo l'esecuzione della query continua in altre repliche. Viceversa, se sfruttare da subito le repliche, si sarà subire un calo delle prestazioni di query, presupponendo che le repliche sono una risorsa sottoutilizzate.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Scalabilità per carichi di lavoro distribuiti geograficamente e ridondanza geografica
Per carichi di lavoro distribuiti geograficamente, gli utenti che si trovano lontano dal data center che ospita ricerca di Azure avranno tassi di latenza superiori. Una soluzione consiste nel effettuare il provisioning di più servizi di ricerca in aree con vicine a tali utenti. Ricerca di Azure attualmente non fornisce un metodo automatizzato per la replica geografica degli indici di Ricerca di Azure tra le aree, ma esistono alcune tecniche che semplificano l'implementazione e la gestione del processo. Queste tecniche vengono illustrate nelle prossime sezioni.

L'obiettivo di un set di servizi di ricerca distribuito geograficamente consiste nel disporre di due o più indici disponibili in due o più aree in cui un utente viene indirizzato al servizio di ricerca di Azure che fornisce la latenza più bassa, come illustrato in questo esempio:

   ![Incrocio dei servizi per area][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Mantenere sincronizzati i dati tra più servizi di Ricerca di Azure
Sono disponibili due opzioni per mantenere sincronizzati i servizi di ricerca distribuiti: l'uso dell'[indicizzatore di Ricerca di Azure](search-indexer-overview.md) o l'API Push, conosciuta anche come [API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Usare gli indicizzatori per l'aggiornamento del contenuto su più servizi

Se si usa già un indicizzatore su un servizio, è possibile configurare un indicizzatore di secondo in un secondo servizio per utilizzare lo stesso oggetto di origine dati, il pull dei dati dallo stesso percorso. Ogni servizio in ogni area ha un proprio indicizzatore e un indice di destinazione (l'indice di ricerca non è condiviso, ovvero i dati duplicati), ma ogni indicizzatore fa riferimento alla stessa origine dati.

Di seguito è un oggetto visivo ad alto livello di aspetto quell'architettura.

   ![Singola origine dati con indicizzatore distribuito e combinazioni di servizio][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Usare le API REST per effettuare il push degli aggiornamenti di contenuto su più servizi
Se si usa l'API REST di ricerca di Azure per [inserire il contenuto nell'indice di ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/update-index), è possibile mantenere sincronizzati i vari servizi di ricerca trasferendo le modifiche apportate a tutti i servizi di ricerca ogni volta che è necessario un aggiornamento. Nel codice, assicurarsi di gestire casi in cui un aggiornamento servizio di ricerca ha esito negativo ma non riesce per altri servizi di ricerca.

## <a name="leverage-azure-traffic-manager"></a>Usare Gestione traffico di Azure
[Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) consente di indirizzare le richieste su più siti Web geograficamente localizzati e supportati da più servizi di Ricerca di Azure. Uno dei vantaggi di Gestione traffico è la capacità di esplorare Ricerca di Azure per garantire che sia disponibile e di indirizzare gli utenti a servizi di ricerca alternativi in caso di tempi di inattività. In aggiunta, se si esegue il routing delle richieste di ricerca tramite siti Web di Azure, Gestione traffico di Azure consente di bilanciare i casi in cui il sito Web è attivo ma Ricerca di Azure non lo è. Di seguito è riportato un esempio di un'architettura che usa Gestione traffico.

   ![Incrocio dei servizi per area con Gestione traffico centrale][3]

## <a name="monitor-performance"></a>Monitorare le prestazioni
Ricerca di Azure offre la possibilità di analizzare e monitorare le prestazioni del servizio tramite [analitica il traffico di ricerca](search-traffic-analytics.md). Quando si abilita questa funzionalità e aggiunta strumentazione all'app client, è possibile registrare le singole operazioni di ricerca, nonché le metriche aggregate per un account di archiviazione di Azure che può essere elaborato per l'analisi o visualizzato in Power BI. In questo modo le acquisizioni di metriche forniscono statistiche sulle prestazioni, ad esempio il numero medio delle query o tempi di risposta alle query. In aggiunta, la registrazione delle operazioni consente di esaminare i dettagli di operazioni di ricerca specifiche.

Analitica il traffico è utile per comprendere i tassi di latenza da tale punto di vista di ricerca di Azure. Poiché le metriche sulle prestazioni delle query registrate sono basate sul tempo necessario per l'elaborazione della query in Ricerca di Azure, ovvero dal momento della richiesta al momento del completamento, è possibile usare questa opzione per determinare se i problemi di latenza provengono dal servizio di Ricerca di Azure o dall'esterno del servizio, ad esempio dalla latenza di rete.  

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui piani tariffari e sui limiti dei servizi, vedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md).

Per altre informazioni sulle combinazioni di partizione e replica, vedere [Pianificazione della capacità](search-capacity-planning.md) .

Per maggiori dettagli sulle prestazioni e per vedere alcune dimostrazioni su come implementare le ottimizzazioni descritte in questo articolo, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
