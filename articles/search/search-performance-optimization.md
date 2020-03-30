---
title: Ridimensionare in base alle prestazioni
titleSuffix: Azure Cognitive Search
description: Informazioni sulle tecniche e le procedure consigliate per ottimizzare le prestazioni di Ricerca cognitiva di Azure e configurare la scalabilità ottimale.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212381"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Scalabilità per le prestazioni in Ricerca cognitiva di AzureScale for performance on Azure Cognitive Search

In questo articolo vengono descritte le procedure consigliate per scenari avanzati con requisiti sofisticati per la scalabilità e la disponibilità.

## <a name="start-with-baseline-numbers"></a>Iniziare con i numeri di base

Prima di eseguire una distribuzione maggiore, assicurarsi di conoscere l'aspetto di un carico di query tipico. Le linee guida seguenti consentono di ottenere numeri di query di base.

1. Selezione una latenza di destinazione (o la quantità massima di tempo) che una ricerca tipica richiede per il completamento.

1. Creare e testare un carico di lavoro reale rispetto al servizio di ricerca con un set di dati realistico per misurare questi tassi di latenza.

1. Iniziare con un numero basso di query al secondo (QPS) e quindi aumentare gradualmente il numero eseguito nel test fino a quando la latenza delle query scende al di sotto della destinazione predefinita. Si tratta di un benchmark importante per la pianificazione della scalabilità man mano che aumenta l'uso dell'applicazione.

1. Se possibile, riusare le connessioni HTTP. Se si usa Azure Cognitive Search .NET SDK, è necessario riutilizzare un'istanza o un'istanza SearchIndexClient e, se si usa l'API REST, è necessario riutilizzare un singolo HttpClient.If you are using the Azure Cognitive Search .NET SDK, this means you should reuse an instance or [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instance, and if you are using the REST API, you should reuse a single HttpClient.

1. Variare la sostanza delle richieste di query in modo che la ricerca venga eseguita su parti diverse dell'indice. Variazione è importante perché se si eseguono continuamente le stesse richieste di ricerca, la memorizzazione dei dati inizierà a rendere le prestazioni un aspetto migliore rispetto a un set di query più disparato.

1. Variare la struttura delle richieste di query in modo da ottenere diversi tipi di query. Non tutte le query di ricerca vengono eseguite allo stesso livello. Ad esempio, una ricerca di documenti o un suggerimento di ricerca è in genere più veloce di una query con un numero significativo di facet e filtri. La composizione dei test deve includere varie query, più o meno gli stessi rapporti previsti nell'ambiente di produzione.  

Durante la creazione di questi carichi di lavoro di test, è necessario tenere presenti alcune caratteristiche di Ricerca cognitiva di Azure:While creating these test workloads, there are some characteristics of Azure Cognitive Search to keep in mind:

+ È possibile sovraccaricare il servizio eseguendo il push di un numero di query di ricerca in una sola volta. In questo caso, vengono visualizzati i codici di risposta HTTP 503. Per evitare un 503 durante il test, iniziare con vari intervalli di richieste di ricerca per visualizzare le differenze nei tassi di latenza man mano che si aggiungono altre richieste di ricerca.

+ Ricerca cognitiva di Azure non esegue attività di indicizzazione in background. Se il servizio gestisce contemporaneamente i carichi di lavoro di query e indicizzazione, tenere lo necessario in considerazione introducendo i processi di indicizzazione nei test di query o esplorando le opzioni per l'esecuzione dei processi di indicizzazione durante le ore non lavorative di punta.

> [!Tip]
> È possibile simulare un carico di query realistico utilizzando gli strumenti di test di carico. Provare il test di [carico con DevOps di Azure](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) o usare una di queste [alternative.](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)

## <a name="scale-for-high-query-volume"></a>Scalabilità per volumi di query elevatiScale for high query volume

Un servizio è sovraccaricato quando le query richiedono troppo tempo o quando il servizio inizia a eliminare le richieste. In questo caso, è possibile risolvere il problema in uno dei due modi seguenti:

+ **Aggiungere repliche**  

  Ogni replica è una copia dei dati, consentendo al servizio di bilanciare il carico delle richieste su più copie.  Il bilanciamento del carico e la replica dei dati vengono gestiti da Ricerca cognitiva di Azure ed è possibile modificare il numero di repliche allocate per il servizio in qualsiasi momento. È possibile allocare fino a 12 repliche in un servizio di ricerca Standard e 3 repliche in un servizio di ricerca Basic. Le repliche possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).

+ **Creare un nuovo servizio a un livello superioreCreate a new service at a higher tier**  

  Ricerca cognitiva di Azure è disponibile in diversi [livelli](https://azure.microsoft.com/pricing/details/search/) e ognuno offre diversi livelli di prestazioni. In alcuni casi, è possibile che siano presenti così tante query in cui il livello in cui ci si trova non è possibile fornire un tempo di svolta sufficiente, anche quando le repliche vengono esaurite. In questo caso, è consigliabile passare a un livello con prestazioni più elevate, ad esempio il livello Standard S3, progettato per scenari con un numero elevato di documenti e carichi di lavoro di query estremamente elevati.

## <a name="scale-for-slow-individual-queries"></a>Scalabilità per singole query lente

Un altro motivo per i tassi di latenza elevata è una singola query che richiede troppo tempo per essere completata. In questo caso, l'aggiunta di repliche non aiuterà. Due possibili opzioni che potrebbero essere utili sono le seguenti:

+ **Aumentare le partizioni**

  Una partizione suddivide i dati tra risorse di elaborazione aggiuntive. Due partizioni suddividono i dati a metà, una terza partizione li divide in terzi e così via. Un effetto collaterale positivo è che le query più lente a volte funzionano più velocemente a causa del calcolo parallelo. È stata notata la parallelizzazione di query a bassa selettività, ad esempio query che corrispondono a molti documenti o facet che forniscono conteggi su un numero elevato di documenti. Poiché è necessario un calcolo significativo per assegnare un punteggio alla pertinenza dei documenti o per contare il numero di documenti, l'aggiunta di partizioni aggiuntive consente di completare più rapidamente il completamento delle query.  
   
  Nel servizio di ricerca Standard possono essere presenti al massimo 12 partizioni e 1 partizione nel servizio di ricerca Basic. Le partizioni possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).

+ **Limita campi ad alta cardinalità**

  Un campo con cardinalità elevata è costituito da un campo facetable o filtrabile con un numero significativo di valori univoci e, di conseguenza, consuma risorse significative durante il calcolo dei risultati. Ad esempio, l'impostazione di un campo ID prodotto o Descrizione come facetable/filtrabile verrebbe conteggiata come cardinalità elevata perché la maggior parte dei valori da documento a documento sono univoci. Se possibile, limitare il numero di campi a cardinalità elevata.

+ **Aumenta livello di ricerca**  

  Passare a un livello di ricerca cognitivo di Azure superiore può essere un altro modo per migliorare le prestazioni delle query lente. Ogni livello superiore offre CPU più veloci e più memoria, entrambe con un impatto positivo sulle prestazioni delle query.

## <a name="scale-for-availability"></a>Scalabilità per la disponibilità

Le repliche non solo consentono di ridurre la latenza delle query, ma possono anche consentire la disponibilità elevata. Con una singola replica, è necessario prevedere un tempo di inattività periodico dovuto al riavvio del server dopo gli aggiornamenti software o per altri interventi di manutenzione. Di conseguenza, è importante considerare se l'applicazione richiede disponibilità elevata di ricerche, ovvero query, e operazioni di scrittura, ovvero eventi indicizzazione. Ricerca cognitiva di Azure offre opzioni del servizio di archiviazione in tutte le offerte di ricerca a pagamento con gli attributi seguenti:Azure Cognitive Search offers SLA options on all the paid search offerings with the following attributes:

+ Due repliche per la disponibilità elevata di carichi di lavoro di sola lettura, vale a dire query.

+ Tre o più repliche per la disponibilità elevata dei carichi di lavoro di lettura/scrittura (query e indicizzazione)Three or more replicas for high availability of read-write workloads (queries and indexing)

Per altre informazioni, vedere il contratto di [servizio Ricerca cognitiva](https://azure.microsoft.com/support/legal/sla/search/v1_0/)di Azure .For more details on this, please visit the Azure Cognitive Search Service Level Agreement .

Poiché le repliche sono copie dei dati, la presenza di più repliche consente a Ricerca cognitiva di Azure di eseguire il riavvio e la manutenzione del computer in una replica, mentre l'esecuzione delle query continua in altre repliche. Al contrario, se si tolgono le repliche, si incorrerà in una riduzione delle prestazioni delle query, presupponendo che tali repliche siano una risorsa sottoutilizzata.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Scalabilità per carichi di lavoro distribuiti geograficamente e ridondanza geograficaScale for geo-distributed workloads and geo-redundancy

Per i carichi di lavoro distribuiti geograficamente, gli utenti che si trovano lontano dal data center host avranno tassi di latenza più elevati. Un'attenuazione consiste nel provisioning di più servizi di ricerca nelle aree con maggiore prossimità di questi utenti.

Ricerca cognitiva di Azure attualmente non fornisce un metodo automatizzato di replica geografica degli indici di Ricerca cognitiva di Azure tra aree, ma esistono alcune tecniche che possono essere usate che possono rendere questo processo semplice da implementare e gestire. Queste tecniche vengono illustrate nelle prossime sezioni.

L'obiettivo di un set di servizi di ricerca distribuito geograficamente è avere due o più indici disponibili in due o più aree, in cui un utente viene instradato al servizio Ricerca cognitiva di Azure che fornisce la latenza più bassa illustrata in questo esempio:The goal of a geo-distributed set of search services is to have two o more indexes available in two or more regions, where a user is routed to the Azure Cognitive Search service that provides the lowest latency as seen in this example:

   ![Incrocio dei servizi per area][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Mantenere sincronizzati i dati tra più servizi

Sono disponibili due opzioni per mantenere sincronizzati i servizi di ricerca distribuiti, che consistono nell'uso [dell'indicizzatore di Ricerca cognitiva](search-indexer-overview.md) di Azure o dell'API Push (nota anche come [API REST di Ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/)di Azure).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Usare gli indicizzatori per aggiornare il contenuto in più serviziUse indexers for updating content on multiple services

Se si usa già l'indicizzatore in un servizio, è possibile configurare un secondo indicizzatore in un secondo servizio per usare lo stesso oggetto origine dati, estraendo i dati dallo stesso percorso. Ogni servizio in ogni area ha il proprio indicizzatore e un indice di destinazione (l'indice di ricerca non è condiviso, ovvero i dati vengono duplicati), ma ogni indicizzatore fa riferimento alla stessa origine dati.

Ecco un oggetto visivo di alto livello di come sarebbe l'architettura.

   ![Singola origine dati con indicizzatore distribuito e combinazioni di servizio][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Usare le API REST per il push degli aggiornamenti del contenuto in più serviziUse REST APIs for push content updates on multiple services

Se si usa l'API REST Ricerca cognitiva di Azure per eseguire il push del [contenuto nell'indice](https://docs.microsoft.com/rest/api/searchservice/update-index)di Ricerca cognitiva di Azure, è possibile mantenere sincronizzati i vari servizi di ricerca eseguendo il push delle modifiche a tutti i servizi di ricerca ogni volta che è necessario un aggiornamento. Nel codice verificare di gestire i casi in cui un aggiornamento a un servizio di ricerca ha esito negativo ma ha esito positivo per altri servizi di ricerca.

## <a name="leverage-azure-traffic-manager"></a>Sfruttare Gestione traffico di Azure

[Gestione traffico](../traffic-manager/traffic-manager-overview.md) di Azure consente di instradare le richieste a più siti Web geolocalizzati che vengono quindi supportati da più servizi di ricerca. Uno dei vantaggi di Gestione traffico è che può eseguire il probe di Ricerca cognitiva di Azure per assicurarsi che sia disponibile e instradare gli utenti a servizi di ricerca alternativi in caso di tempo di inattività. In addition, if you are routing search requests through Azure Web Sites, Azure Traffic Manager allows you to load balance cases where the Website is up but not Azure Cognitive Search. Di seguito è riportato un esempio di un'architettura che usa Gestione traffico.

   ![Incrocio dei servizi per area con Gestione traffico centrale][3]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui piani tariffari e sui limiti dei servizi per ognuno di essi, vedere [Limiti del servizio](search-limits-quotas-capacity.md). Vedere [Pianificare la capacità di](search-capacity-planning.md) ottenere ulteriori informazioni sulle combinazioni di partizioni e repliche.

Per una discussione sulle prestazioni e le dimostrazioni delle tecniche descritte in questo articolo, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
