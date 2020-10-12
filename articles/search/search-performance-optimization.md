---
title: Ridimensionare in base alle prestazioni
titleSuffix: Azure Cognitive Search
description: Informazioni sulle tecniche e sulle procedure consigliate per ottimizzare le prestazioni di Azure ricerca cognitiva e configurare la scalabilità ottimale.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5fd949466978714fe1dc0c4ccc67a3cb8f993314
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934957"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Scalabilità per le prestazioni in Azure ricerca cognitiva

Questo articolo descrive le procedure consigliate per gli scenari avanzati con requisiti sofisticati per la scalabilità e la disponibilità.

## <a name="start-with-baseline-numbers"></a>Inizia con i numeri di base

Prima di intraprendere un lavoro di distribuzione più ampio, assicurarsi di conoscere l'aspetto di un carico di query tipico. Le linee guida seguenti consentono di arrivare ai numeri di query di base.

1. Selezione una latenza di destinazione (o la quantità massima di tempo) che una ricerca tipica richiede per il completamento.

1. Creare e testare un carico di lavoro reale nel servizio di ricerca con un set di dati realistico per misurare questi tassi di latenza.

1. Iniziare con un numero ridotto di query al secondo (query al secondo) e quindi aumentare gradualmente il numero eseguito nel test fino a quando la latenza della query scende sotto la destinazione predefinita. Si tratta di un benchmark importante per la pianificazione della scalabilità man mano che aumenta l'uso dell'applicazione.

1. Se possibile, riusare le connessioni HTTP. Se si usa Azure ricerca cognitiva .NET SDK, questo significa che è necessario riutilizzare un'istanza di o un'istanza di [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) . Se si usa l'API REST, è necessario riusare un singolo HttpClient.

1. Variare la sostanza delle richieste di query in modo che la ricerca venga eseguita su diverse parti dell'indice. La variazione è importante perché se si eseguono continuamente le stesse richieste di ricerca, la memorizzazione nella cache dei dati inizierà a migliorare le prestazioni rispetto a una serie di query più diverse.

1. Variare la struttura delle richieste di query in modo da ottenere tipi diversi di query. Non tutte le query di ricerca vengono eseguite allo stesso livello. Ad esempio, una ricerca di documenti o un suggerimento di ricerca è in genere più veloce di una query con un numero significativo di facet e filtri. La composizione dei test deve includere diverse query, approssimativamente le stesse proporzioni che ci si aspetterebbe nell'ambiente di produzione.  

Durante la creazione di questi carichi di lavoro di test, è necessario tenere presenti alcune caratteristiche di Azure ricerca cognitiva:

+ È possibile sovraccaricare il servizio effettuando un numero eccessivo di query di ricerca alla volta. In questo caso, vengono visualizzati i codici di risposta HTTP 503. Per evitare un 503 durante i test, iniziare con diversi intervalli di richieste di ricerca per vedere le differenze nei tassi di latenza quando si aggiungono altre richieste di ricerca.

+ Azure ricerca cognitiva non esegue attività di indicizzazione in background. Se il servizio gestisce contemporaneamente i carichi di lavoro di query e di indicizzazione, è necessario prendere in considerazione l'introduzione dei processi di indicizzazione nei test di query o l'esplorazione delle opzioni per l'esecuzione di processi di indicizzazione durante gli orari di minore attività.

> [!Tip]
> È possibile simulare un carico di query realistico usando gli strumenti di test di carico. Provare [a eseguire il test di carico con Azure DevOps](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) o usare una di queste [alternative](/azure/devops/test/load-test/overview?view=azure-devops#alternatives).

## <a name="scale-for-high-query-volume"></a>Scalabilità per un volume di query elevato

Un servizio viene sovraccaricato quando le query richiedono troppo tempo o quando il servizio inizia a eliminare richieste. In tal caso, è possibile risolvere il problema in uno dei due modi seguenti:

+ **Aggiungere repliche**  

  Ogni replica è una copia dei dati, consentendo al servizio di bilanciare il carico delle richieste rispetto a più copie.  Il bilanciamento del carico e la replica dei dati vengono gestiti da Azure ricerca cognitiva ed è possibile modificare il numero di repliche allocate per il servizio in qualsiasi momento. È possibile allocare fino a 12 repliche in un servizio di ricerca Standard e 3 repliche in un servizio di ricerca Basic. Le repliche possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).

+ **Creare un nuovo servizio a un livello superiore**  

  Azure ricerca cognitiva è disponibile in diversi [livelli, ognuno dei quali](https://azure.microsoft.com/pricing/details/search/) offre diversi livelli di prestazioni. In alcuni casi, è possibile che si disponga di un numero così elevato di query di cui il livello si è in grado di fornire un tempo di indisponibilità sufficiente anche quando le repliche sono al massimo In questo caso, si consiglia di passare a un livello di prestazioni superiore, ad esempio il livello S3 standard, progettato per scenari con un numero elevato di documenti e carichi di lavoro di query estremamente elevati.

## <a name="scale-for-slow-individual-queries"></a>Scalabilità per singole query lente

Un altro motivo per i tassi di latenza elevata è una singola query che richiede troppo tempo per il completamento. In questo caso, l'aggiunta di repliche non è utile. Di seguito sono riportate due possibili opzioni che possono essere utili:

+ **Aumentare le partizioni**

  Una partizione suddivide i dati tra le risorse di elaborazione aggiuntive. Due partizioni suddividono i dati a metà, una terza partizione la suddivide in tre e così via. Un effetto collaterale positivo è che le query più lente a volte vengono eseguite più velocemente grazie al calcolo parallelo. È stata annotata la parallelizzazione su query con selettività bassa, ad esempio query che corrispondono a molti documenti, o facet che forniscono conteggi su un numero elevato di documenti. Poiché è necessario un calcolo significativo per assegnare un punteggio alla pertinenza dei documenti o per conteggiare il numero di documenti, l'aggiunta di partizioni aggiuntive consente di completare più velocemente le query.  
   
  Il servizio di ricerca standard e una partizione nel servizio di ricerca di base possono contenere un massimo di 12 partizioni. Le partizioni possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).

+ **Limitare i campi di cardinalità elevata**

  Un campo di cardinalità elevata è costituito da un campo di facet o filtrabile con un numero significativo di valori univoci e, di conseguenza, utilizza risorse significative durante il calcolo dei risultati. Ad esempio, l'impostazione di un campo ID prodotto o descrizione come facet/filtrabile viene conteggiata come alta cardinalità poiché la maggior parte dei valori dal documento al documento sono univoci. Se possibile, limitare il numero di campi a cardinalità elevata.

+ **Aumenta livello di ricerca**  

  Il passaggio a un livello di ricerca cognitiva di Azure superiore può essere un altro modo per migliorare le prestazioni delle query lente. Ogni livello superiore fornisce CPU più veloci e una maggiore quantità di memoria, che hanno un impatto positivo sulle prestazioni delle query.

## <a name="scale-for-availability"></a>Scalabilità per la disponibilità

Le repliche non solo consentono di ridurre la latenza delle query, ma possono anche garantire disponibilità elevata. Con una singola replica, è necessario prevedere un tempo di inattività periodico dovuto al riavvio del server dopo gli aggiornamenti software o per altri interventi di manutenzione. Di conseguenza, è importante considerare se l'applicazione richiede disponibilità elevata di ricerche, ovvero query, e operazioni di scrittura, ovvero eventi indicizzazione. Azure ricerca cognitiva offre opzioni di SLA per tutte le offerte di ricerca a pagamento con i seguenti attributi:

+ Due repliche per la disponibilità elevata di carichi di lavoro di sola lettura, vale a dire query.

+ Tre o più repliche per la disponibilità elevata dei carichi di lavoro di lettura e scrittura (query e indicizzazione)

Per altri dettagli, visitare il [Contratto di servizio ricerca cognitiva di Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Poiché le repliche sono copie dei dati, la presenza di più repliche consente ad Azure ricerca cognitiva di eseguire riavvii del computer e manutenzione su una replica, mentre l'esecuzione delle query continua su altre repliche. Viceversa, se si eliminano le repliche, si verifica un calo delle prestazioni delle query, presupponendo che tali repliche fossero una risorsa sottoutilizzata.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Scalabilità per carichi di lavoro distribuiti geograficamente e ridondanza geografica

Per i carichi di lavoro distribuiti geograficamente, gli utenti che si trovano lontano dall'host data center avranno tassi di latenza superiori. Una mitigazione consiste nel provisioning di più servizi di ricerca in aree con prossimità più vicina a questi utenti.

Azure ricerca cognitiva attualmente non offre un metodo automatizzato per la replica geografica degli indici di ricerca cognitiva di Azure tra le aree, ma sono disponibili alcune tecniche che possono rendere questo processo semplice da implementare e gestire. Queste tecniche vengono illustrate nelle prossime sezioni.

L'obiettivo di un set con distribuzione geografica di servizi di ricerca consiste nel disporre di due o più indici disponibili in due o più aree, in cui un utente viene indirizzato al servizio ricerca cognitiva di Azure che offre la latenza più bassa, come illustrato in questo esempio:

   ![Incrocio dei servizi per area][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Mantieni i dati sincronizzati tra più servizi

Sono disponibili due opzioni per mantenere sincronizzati i servizi di ricerca distribuiti, che possono essere usati con l' [indicizzatore ricerca cognitiva di Azure](search-indexer-overview.md) o l'API push (detta anche [API REST di Azure ricerca cognitiva](/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Usare gli indicizzatori per aggiornare il contenuto su più servizi

Se si usa già l'indicizzatore in un servizio, è possibile configurare un secondo indicizzatore in un secondo servizio per usare lo stesso oggetto origine dati, estraendo i dati dallo stesso percorso. Ogni servizio in ogni area ha un proprio indicizzatore e un indice di destinazione (l'indice di ricerca non è condiviso, il che significa che i dati sono duplicati), ma ogni indicizzatore fa riferimento alla stessa origine dati.

Di seguito è riportato un oggetto visivo di alto livello dell'aspetto dell'architettura.

   ![Singola origine dati con indicizzatore distribuito e combinazioni di servizio][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Usare le API REST per il push degli aggiornamenti del contenuto su più servizi

Se si usa l'API REST di Azure ricerca cognitiva per eseguire il [push del contenuto nell'indice ricerca cognitiva di Azure](/rest/api/searchservice/update-index), è possibile sincronizzare i vari servizi di ricerca eseguendo il push delle modifiche a tutti i servizi di ricerca ogni volta che è necessario un aggiornamento. Nel codice, assicurarsi di gestire i casi in cui un aggiornamento a un servizio di ricerca ha esito negativo, ma ha esito positivo per altri servizi di ricerca.

## <a name="leverage-azure-traffic-manager"></a>Sfruttare gestione traffico di Azure

[Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) consente di instradare le richieste a più siti Web con localizzazione geografica, supportati da più servizi di ricerca. Un vantaggio di gestione traffico è che può sondare Azure ricerca cognitiva per assicurarsi che sia disponibile e indirizzare gli utenti a servizi di ricerca alternativi in caso di tempi di inattività. Inoltre, se si esegue il routing delle richieste di ricerca tramite siti Web di Azure, gestione traffico di Azure consente di bilanciare il carico dei casi in cui il sito Web è attivo, ma non di Azure ricerca cognitiva. Di seguito è riportato un esempio di un'architettura che usa Gestione traffico.

   ![Incrocio dei servizi per area con Gestione traffico centrale][3]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui piani tariffari e sui limiti dei servizi, vedere [limiti del servizio](search-limits-quotas-capacity.md). Per altre informazioni sulle combinazioni di partizioni e repliche, vedere [pianificare la capacità](search-capacity-planning.md) .

Per informazioni sulle prestazioni e le dimostrazioni delle tecniche descritte in questo articolo, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png