---
title: Strategie di distribuzione e procedure consigliate per l'ottimizzazione delle prestazioni-ricerca di Azure
description: Informazioni tecniche e procedure consigliate per l'ottimizzazione delle prestazioni di Ricerca di Azure e la configurazione ottimale per la scalabilità.
author: LiamCavanagh
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 566c208ef415f6fc9f3ada419e2f9e9244bc066d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333154"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Strategie di distribuzione e procedure consigliate per l'ottimizzazione delle prestazioni in ricerca di Azure

Questo articolo descrive le procedure consigliate per gli scenari avanzati con requisiti sofisticati per la scalabilità e la disponibilità. 

## <a name="develop-baseline-numbers"></a>Sviluppare numeri di base
Quando si ottimizzano le prestazioni di ricerca, è necessario concentrarsi sulla riduzione del tempo di esecuzione delle query. A tale scopo, è necessario conoscere l'aspetto di un carico di query tipico. Le linee guida seguenti consentono di arrivare ai numeri di query di base.

1. Selezione una latenza di destinazione (o la quantità massima di tempo) che una ricerca tipica richiede per il completamento.
2. Creare e testare un carico di lavoro reale nel servizio di ricerca con un set di dati reale per misurare i tassi di latenza.
3. Iniziare con un numero ridotto di query al secondo (query al secondo) e aumentare gradualmente il numero eseguito nel test fino a quando la latenza di query scende sotto la latenza di destinazione definita. Si tratta di un benchmark importante per la pianificazione della scalabilità man mano che aumenta l'uso dell'applicazione.
4. Se possibile, riusare le connessioni HTTP. Se si usa .NET SDK di Ricerca di Azure, è necessario riusare un'istanza o un'istanza [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient). Se si usa l'API REST, è necessario riusare un singolo HttpClient.
5. Variare la sostanza delle richieste di query in modo che la ricerca venga eseguita su diverse parti dell'indice. La variazione è importante perché se si eseguono continuamente le stesse richieste di ricerca, la memorizzazione nella cache dei dati inizierà a migliorare le prestazioni rispetto a una serie di query più diverse.
6. Variare la struttura delle richieste di query in modo da ottenere tipi diversi di query. Non tutte le query di ricerca vengono eseguite allo stesso livello. Ad esempio, una ricerca di documenti o un suggerimento di ricerca è in genere più veloce di una query con un numero significativo di facet e filtri. La composizione dei test deve includere diverse query, approssimativamente le stesse proporzioni che ci si aspetterebbe nell'ambiente di produzione.  

Durante la creazione di questi carichi di lavoro di test, è opportuno considerare alcune caratteristiche di Ricerca di Azure:

+ È possibile sovraccaricare il servizio effettuando un numero eccessivo di query di ricerca alla volta. In questo caso, vengono visualizzati i codici di risposta HTTP 503. Per evitare un 503 durante i test, iniziare con diversi intervalli di richieste di ricerca per vedere le differenze nei tassi di latenza quando si aggiungono altre richieste di ricerca.

+ Ricerca di Azure non esegue attività di indicizzazione in background. Se il servizio gestisce contemporaneamente i carichi di lavoro di query e di indicizzazione, è necessario prendere in considerazione l'introduzione dei processi di indicizzazione nei test di query o l'esplorazione delle opzioni per l'esecuzione di processi di indicizzazione durante gli orari di minore attività.

> [!NOTE]
> [Il test di carico di Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) è un metodo molto valido per eseguire i test di benchmark in quanto consente di eseguire le richieste HTTP nello stesso modo richiesto per l'esecuzione di query con Ricerca di Azure e consente la parallelizzazione delle richieste.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Ridimensionamento per un volume di query elevato e richieste limitate
Quando si ricevono troppe richieste limitate o si superano i tassi di latenza di destinazione da un carico di query più elevato, è possibile cercare di ridurre i tassi di latenza in uno dei due modi seguenti:

1. **Aumentare le repliche:** una replica è una copia dei dati che consente a Ricerca di Azure di bilanciare il carico con le numerose copie.  Il bilanciamento del carico e la replica dei dati tra le repliche vengono gestiti da Ricerca di Azure ed è possibile modificare in qualsiasi momento il numero delle repliche allocate per il servizio.  È possibile allocare fino a 12 repliche in un servizio di ricerca Standard e 3 repliche in un servizio di ricerca Basic. Le repliche possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).
2. **Aumentare il livello di Ricerca:** sono disponibili [diversi livelli](https://azure.microsoft.com/pricing/details/search/) per Ricerca di Azure, ognuno dei quali offre diversi livelli di prestazioni.  In alcuni casi, è possibile che si disponga di un numero così elevato di query su cui il livello si è in grado di fornire tassi di latenza sufficientemente bassa, anche quando le repliche sono esaurite. In questo caso, è consigliabile usare uno dei livelli di ricerca più elevati, ad esempio il livello S3 di ricerca di Azure particolarmente adatto per scenari con un numero elevato di documenti e carichi di lavoro di query estremamente elevati.

## <a name="scaling-for-slow-individual-queries"></a>Ridimensionamento per singole query lente
Un altro motivo per i tassi di latenza elevata è una singola query che richiede troppo tempo per il completamento. In questo caso, l'aggiunta di repliche non è utile. Di seguito sono riportate due possibili opzioni che possono essere utili:

1. **Aumentare le partizioni**: una partizione è un meccanismo per la suddivisione dei dati tra le risorse aggiuntive. L'aggiunta di una seconda partizione suddivide i dati in due, una terza partizione la suddivide in tre e così via. Un effetto collaterale positivo è che le query più lente a volte vengono eseguite più velocemente grazie al calcolo parallelo. È stata annotata la parallelizzazione su query con selettività bassa, ad esempio query che corrispondono a molti documenti, o facet che forniscono conteggi su un numero elevato di documenti. Poiché è necessario un calcolo significativo per assegnare un punteggio alla pertinenza dei documenti o per conteggiare il numero di documenti, l'aggiunta di partizioni aggiuntive consente di completare più velocemente le query.  
   
   Il servizio di ricerca Standard offe fino a un massimo di 12 partizioni, mentre il servizio Basic offre 1 partizione.  Le partizioni possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).

2. **Limitare i campi di cardinalità elevata:** Un campo di cardinalità elevata è costituito da un campo di facet o filtrabile con un numero significativo di valori univoci e, di conseguenza, utilizza risorse significative durante il calcolo dei risultati. Ad esempio, l'impostazione di un campo ID prodotto o descrizione come facet/filtrabile viene conteggiata come alta cardinalità poiché la maggior parte dei valori dal documento al documento sono univoci. Se possibile, limitare il numero di campi a cardinalità elevata.

3. **Aumentare il livello di Ricerca:** il passaggio a un livello superiore di Ricerca di Azure può essere un altro modo per migliorare le prestazioni delle query lente. Ogni livello superiore fornisce CPU più veloci e una maggiore quantità di memoria, che hanno un impatto positivo sulle prestazioni delle query.

## <a name="scaling-for-availability"></a>Ridimensionamento per la disponibilità
Le repliche non solo consentono di ridurre la latenza delle query ma possono anche favorire una disponibilità elevata. Con una singola replica, è necessario prevedere un tempo di inattività periodico dovuto al riavvio del server dopo gli aggiornamenti software o per altri interventi di manutenzione.  Di conseguenza, è importante considerare se l'applicazione richiede disponibilità elevata di ricerche, ovvero query, e operazioni di scrittura, ovvero eventi indicizzazione. Ricerca di Azure offre opzioni di Contratto di servizio in tutte le offerte a pagamento, con le caratteristiche seguenti:

* 2 repliche per la disponibilità elevata dei carichi di lavoro di sola lettura (query)
* 3 o più repliche per la disponibilità elevata dei carichi di lavoro di lettura/scrittura (query e indicizzazione)

Per ulteriori informazioni, visitare il [Contratto di Servizio per Ricerca di Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Poiché le repliche sono copie dei dati, la presenza di più repliche consente a ricerca di Azure di eseguire riavvii del computer e manutenzione su una replica consentendo al tempo stesso l'esecuzione delle query su altre repliche. Viceversa, se si eliminano le repliche, si verifica un calo delle prestazioni delle query, presupponendo che tali repliche fossero una risorsa sottoutilizzata.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Scalabilità per carichi di lavoro distribuiti geograficamente e ridondanza geografica
Per i carichi di lavoro distribuiti geograficamente, gli utenti che si trovano lontano dall'data center che ospita ricerca di Azure avranno tassi di latenza più elevati. Una mitigazione consiste nel provisioning di più servizi di ricerca in aree con prossimità più vicina a questi utenti. Ricerca di Azure attualmente non fornisce un metodo automatizzato per la replica geografica degli indici di Ricerca di Azure tra le aree, ma esistono alcune tecniche che semplificano l'implementazione e la gestione del processo. Queste tecniche vengono illustrate nelle prossime sezioni.

L'obiettivo di un set con distribuzione geografica di servizi di ricerca consiste nel disporre di due o più indici disponibili in due o più aree in cui un utente viene indirizzato al servizio ricerca di Azure che offre la latenza più bassa, come illustrato in questo esempio:

   ![Incrocio dei servizi per area][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Mantenere sincronizzati i dati tra più servizi di Ricerca di Azure
Sono disponibili due opzioni per mantenere sincronizzati i servizi di ricerca distribuiti: l'uso dell'[indicizzatore di Ricerca di Azure](search-indexer-overview.md) o l'API Push, conosciuta anche come [API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Usare gli indicizzatori per aggiornare il contenuto su più servizi

Se si usa già l'indicizzatore in un servizio, è possibile configurare un secondo indicizzatore in un secondo servizio per usare lo stesso oggetto origine dati, estraendo i dati dallo stesso percorso. Ogni servizio in ogni area ha un proprio indicizzatore e un indice di destinazione (l'indice di ricerca non è condiviso, il che significa che i dati sono duplicati), ma ogni indicizzatore fa riferimento alla stessa origine dati.

Di seguito è riportato un oggetto visivo di alto livello dell'aspetto dell'architettura.

   ![Singola origine dati con indicizzatore distribuito e combinazioni di servizio][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Usare le API REST per il push degli aggiornamenti del contenuto su più servizi
Se si usa l'API REST di ricerca di Azure per eseguire il [push del contenuto nell'indice di ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/update-index), è possibile sincronizzare i vari servizi di ricerca eseguendo il push delle modifiche a tutti i servizi di ricerca ogni volta che è necessario un aggiornamento. Nel codice, assicurarsi di gestire i casi in cui un aggiornamento a un servizio di ricerca ha esito negativo, ma ha esito positivo per altri servizi di ricerca.

## <a name="leverage-azure-traffic-manager"></a>Sfruttare gestione traffico di Azure
[Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) consente di indirizzare le richieste su più siti Web geograficamente localizzati e supportati da più servizi di Ricerca di Azure. Uno dei vantaggi di Gestione traffico è la capacità di esplorare Ricerca di Azure per garantire che sia disponibile e di indirizzare gli utenti a servizi di ricerca alternativi in caso di tempi di inattività. In aggiunta, se si esegue il routing delle richieste di ricerca tramite siti Web di Azure, Gestione traffico di Azure consente di bilanciare i casi in cui il sito Web è attivo ma Ricerca di Azure non lo è. Di seguito è riportato un esempio di un'architettura che usa Gestione traffico.

   ![Incrocio dei servizi per area con Gestione traffico centrale][3]

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
