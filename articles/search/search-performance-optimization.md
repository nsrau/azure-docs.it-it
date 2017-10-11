---
title: Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca di Azure | Microsoft Docs
description: "Ottimizzare le prestazioni di Ricerca di Azure e configurare una scalabilità ottimale"
services: search
documentationcenter: 
author: LiamCavanagh
manager: pablocas
editor: 
ms.assetid: 4d3cd864-29d2-4921-be0d-a3f1a819de46
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: f4e371fc16bc57e6963f1ec51c0ea864fa568f0c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca di Azure
Un'esperienza eccellente è la chiave del successo per molti dispositivi mobili e applicazioni Web. Dal mercato immobiliare, passando per il mercato dell'usato dei veicoli fino ai negozi online, la ricerca veloce e i risultati rilevanti influiscono sull'esperienza dell'utente. Questo documento aiuta l'utente a scoprire le procedure consigliate su come ottenere il massimo da Ricerca di Azure, in particolare per scenari avanzati con requisiti di scalabilità avanzati, con supporto multilingue o classificazione personalizzata.  Questo documento illustra anche i meccanismi interni e descrive gli approcci efficienti per le applicazioni reali dei clienti.

## <a name="performance-and-scale-tuning-for-search-services"></a>Prestazioni e pianificazione della scalabilità per i servizi di Ricerca
Tutti conosciamo e usiamo motori di ricerca come Bing e Google e le relative prestazioni offerte.  Di conseguenza, quando i clienti usano applicazioni mobili o Web abilitate per la ricerca, si aspettano prestazioni simili.  Per l'ottimizzazione delle prestazioni di ricerca, l'approccio migliore consiste nel concentrarsi sulla latenza, ovvero sul tempo necessario per completare la query e restituire i risultati.  Durante l'ottimizzazione della latenza della ricerca, è importante:

1. Selezione una latenza di destinazione (o la quantità massima di tempo) che una ricerca tipica richiede per il completamento.
2. Creare e testare un carico di lavoro reale nel servizio di ricerca con un set di dati reale per misurare i tassi di latenza.
3. Iniziare con un numero ridotto di query al secondo (QPS) e continuare ad aumentare il numero di query testate fino a quando la latenza delle query diventa inferiore alla latenza di destinazione definita.  Si tratta di un benchmark importante per la pianificazione della scalabilità man mano che aumenta l'uso dell'applicazione.
4. Se possibile, riusare le connessioni HTTP.  Se si usa .NET SDK di Ricerca di Azure, è necessario riusare un'istanza o un'istanza [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient). Se si usa l'API REST, è necessario riusare un singolo HttpClient.

Durante la creazione di questi carichi di lavoro di test, è opportuno considerare alcune caratteristiche di Ricerca di Azure:

1. È possibile eseguire un numero di query di ricerca per volta finché le risorse disponibili nel servizio di Ricerca di Azure non sono sovraccariche.  In questo caso, vengono visualizzati i codici di risposta HTTP 503.  Per questo motivo, è preferibile iniziare con vari intervalli di richieste di ricerca per vedere le differenze nei tassi di latenza all'aggiunta di nuove richieste di ricerca.
2. Il caricamento del contenuto in Ricerca di Azure influisce sulle prestazioni complessive e sulla latenza del servizio Ricerca di Azure.  Se si prevede di inviare i dati mentre gli utenti eseguono ricerche, è importante prendere in considerazione questo carico di lavoro durante i test.
3. Non tutte le query di ricerca verranno eseguite con lo stesso livello di prestazioni.  Ad esempio, la visualizzazione di un documento o un consiglio sulla ricerca viene generalmente eseguito più velocemente rispetto a una query con un numero significativo di facet e filtri.  Durante la compilazione del test, è consigliabile scegliere le varie query che si desidera vedere nel proprio account.  
4. È importante variare le richieste di ricerca perché, se si eseguono continuamente le stesse richieste, verrà avviato il caching dei dati per migliorare le prestazioni rispetto alla qualità offerta da un set di query più eterogeneo.

> [!NOTE]
> [Il test di carico di Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) è un metodo molto valido per eseguire i test di benchmark in quanto consente di eseguire le richieste HTTP nello stesso modo richiesto per l'esecuzione di query con Ricerca di Azure e consente la parallelizzazione delle richieste.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Ridimensionamento di Ricerca di Azure per tassi elevati di query e richieste limitate
Quando si ricevono troppe richieste limitate o si superano i tassi di latenza di destinazione da un carico maggiore di query, è possibile considerare di ridurre il tasso di latenza in uno dei due modi seguenti:

1. **Aumentare le repliche:** una replica è una copia dei dati che consente a Ricerca di Azure di bilanciare il carico con le numerose copie.  Il bilanciamento del carico e la replica dei dati tra le repliche vengono gestiti da Ricerca di Azure ed è possibile modificare in qualsiasi momento il numero delle repliche allocate per il servizio.  È possibile allocare fino a 12 repliche in un servizio di ricerca Standard e 3 repliche in un servizio di ricerca Basic. Le repliche possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).
2. **Aumentare il livello di Ricerca:** sono disponibili [diversi livelli](https://azure.microsoft.com/pricing/details/search/) per Ricerca di Azure, ognuno dei quali offre diversi livelli di prestazioni.  In alcuni casi, è possibile che l'utente disponga di un numero talmente elevato di query che il livello a cui appartiene non fornisce tassi di latenza sufficientemente bassi, anche quando si raggiunge il limite massimo delle repliche.  In questo caso, è consigliabile considerare di usare uno dei livelli di Ricerca di Azure più elevati, ad esempio il livello S3, adatto a scenari con un numero elevato di documenti e carichi di lavoro delle query estremamente elevati.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Ridimensionamento di Ricerca di Azure per le singole query lente
Un altro motivo per cui il tasso di latenza può essere lento è una singola query che impiega troppo tempo.  In questo caso, l'aggiunta di repliche non migliorerà il tasso di latenza.  In questo caso sono disponibili due opzioni:

1. **Aumentare le partizioni**: una partizione è un meccanismo per la suddivisione dei dati tra le risorse aggiuntive.  Per questo motivo, quando si aggiunge una seconda partizione, si ottengono dati divisi in due parti.  Una terza partizione suddivide l'indice in tre parti e così via.  Questo fattore, in alcuni casi, si traduce in un aumento di velocità delle query lente grazie alla parallelizzazione del calcolo.  Sono disponibili alcuni esempi dell'efficienza della parallelizzazione con query che dispongono di query a selettività ridotta.  Si tratta di query che corrispondono a molti documenti o ai casi in cui i facet devono fornire calcoli su un grande numero di documenti.  Poiché sono necessari molti calcoli per valutare la rilevanza dei documenti o per calcolare il numero dei documenti, l'aggiunta di altre partizioni consente di fornire calcoli aggiuntivi.  
   
   Il servizio di ricerca Standard offe fino a un massimo di 12 partizioni, mentre il servizio Basic offre 1 partizione.  Le partizioni possono essere modificate dal [portale di Azure](search-create-service-portal.md) o da [PowerShell](search-manage-powershell.md).
2. **Limitare i campi a cardinalità elevata:** un campo a cardinalità elevata è costituito da un facet o da un campo filtrabile che dispone di un numero significativo di valori univoci e, di conseguenza, richiede molte risorse per calcolare i risultati.   Ad esempio, l'impostazione di un campo ID prodotto o la descrizione come facet/filtrabile comporterebbe un'elevata cardinalità perché la maggior parte dei valori dei documenti è univoca. Se possibile, limitare il numero di campi a cardinalità elevata.
3. **Aumentare il livello di Ricerca:** il passaggio a un livello superiore di Ricerca di Azure può essere un altro modo per migliorare le prestazioni delle query lente.  Ogni livello superiore fornisce CPU più veloce e memoria più ampia, elementi che possono avere un impatto positivo sulle prestazioni delle query.

## <a name="scaling-for-availability"></a>Ridimensionamento per la disponibilità
Le repliche non solo consentono di ridurre la latenza delle query ma possono anche favorire una disponibilità elevata.  Con una singola replica, è necessario prevedere un tempo di inattività periodico dovuto al riavvio del server dopo gli aggiornamenti software o per altri interventi di manutenzione.  Di conseguenza, è importante considerare se l'applicazione richiede disponibilità elevata di ricerche, ovvero query, e operazioni di scrittura, ovvero eventi indicizzazione.  Ricerca di Azure offre opzioni di Contratto di servizio in tutte le offerte a pagamento, con le caratteristiche seguenti:

* 2 repliche per la disponibilità elevata dei carichi di lavoro di sola lettura (query)
* 3 o più repliche per la disponibilità elevata dei carichi di lavoro di lettura/scrittura (query e indicizzazione)

Per ulteriori informazioni, visitare il [Contratto di Servizio per Ricerca di Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Poiché le repliche sono copie dei dati, la presenza di più repliche consente a Ricerca di Azure di riavviare le macchine e di eseguire la manutenzione su una replica per volta, consentendo allo stesso tempo l'esecuzione delle query sulle altre repliche.  Per questo motivo, è necessario anche considerare in che modo il tempo di inattività può influire sulle query che devono ora essere eseguite su una copia dei dati in meno.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Ridimensionamento dei carichi di lavoro distribuiti geograficamente e ridondanza geografica
Per i carichi di lavoro distribuiti geograficamente, si noterà che gli utenti lontani dal data center in cui è ospitato il servizio Ricerca di Azure registrano tassi di latenza superiori.  Per questo motivo, è spesso importante disporre di più servizi di ricerca nelle aree vicine a tali utenti.  Ricerca di Azure attualmente non fornisce un metodo automatizzato per la replica geografica degli indici di Ricerca di Azure tra le aree, ma esistono alcune tecniche che semplificano l'implementazione e la gestione del processo. Queste tecniche vengono illustrate nelle prossime sezioni.

L'obiettivo di un set di servizi di ricerca distribuito geograficamente consiste nel disporre di due o più indici disponibili in due o più aree in cui un utente verrà reindirizzato al servizio di Ricerca di Azure che offre il tasso di latenza più basso, come illustrato in questo esempio:

   ![Incrocio dei servizi per area][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Mantenere sincronizzati i dati tra più servizi di Ricerca di Azure
Sono disponibili due opzioni per mantenere sincronizzati i servizi di ricerca distribuiti: l'uso dell'[indicizzatore di Ricerca di Azure](search-indexer-overview.md) o l'API Push, conosciuta anche come [API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/).  

### <a name="azure-search-indexers"></a>Indicizzatori di Ricerca di Azure
Se si usa l'indicizzatore di Ricerca di Azure, si importano le modifiche ai dati da un archivio dati centrale, ad esempio database SQL di Azure o Azure Cosmos DB. Quando si crea un nuovo servizio di Ricerca, è sufficiente creare un nuovo indicizzatore di Ricerca di Azure per il servizio creato, che punta allo stesso archivio dati. In questo modo, ogni volta che le nuove modifiche entrano nell'archivio dati, vengono indicizzate dai diversi indicizzatori.  

Di seguito è riportato un esempio dell'aspetto dell'architettura.

   ![Singola origine dati con indicizzatore distribuito e combinazioni di servizio][2]

### <a name="push-api"></a>API Push
Se si usa l'API Push di Ricerca di Azure per [aggiornare il contenuto dell'indice di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/update-index), è possibile mantenere sincronizzati i vari servizi di ricerca trasferendo le modifiche apportate a tutti i servizi di ricerca ogni volta che è necessario un aggiornamento.  Quando si esegue questa operazione è importante verificare di gestire i casi in cui si verifica un errore nell'aggiornamento del servizio di ricerca e i casi in cui uno o più aggiornamenti hanno esito positivo.

## <a name="leveraging-azure-traffic-manager"></a>Uso di Gestione traffico di Azure
[Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) consente di indirizzare le richieste su più siti Web geograficamente localizzati e supportati da più servizi di Ricerca di Azure.  Uno dei vantaggi di Gestione traffico è la capacità di esplorare Ricerca di Azure per garantire che sia disponibile e di indirizzare gli utenti a servizi di ricerca alternativi in caso di tempi di inattività.  In aggiunta, se si esegue il routing delle richieste di ricerca tramite siti Web di Azure, Gestione traffico di Azure consente di bilanciare i casi in cui il sito Web è attivo ma Ricerca di Azure non lo è.  Di seguito è riportato un esempio di un'architettura che usa Gestione traffico.

   ![Incrocio dei servizi per area con Gestione traffico centrale][3]

## <a name="monitoring-performance"></a>Monitoraggio delle prestazioni
Ricerca di Azure offre la possibilità di analizzare e monitorare le prestazioni del servizio tramite [Analisi del traffico di ricerca (STA)](search-traffic-analytics.md). Tramite il servizio STA è possibile registrare le singole operazioni di ricerca e le metriche di aggregazione su un account di archiviazione di Azure che può essere elaborato per l'analisi o visualizzato in Power BI.  Con le metriche STA, è possibile esaminare le statistiche sulle prestazioni, ad esempio il numero medio delle query o i tempi di risposta alle query.  In aggiunta, la registrazione delle operazioni consente di esaminare i dettagli di operazioni di ricerca specifiche.

STA è uno strumento utile per comprendere i tassi di latenza da tale punto di vista di Ricerca di Azure.  Poiché le metriche sulle prestazioni delle query registrate sono basate sul tempo necessario per l'elaborazione della query in Ricerca di Azure, ovvero dal momento della richiesta al momento del completamento, è possibile usare questa opzione per determinare se i problemi di latenza provengono dal servizio di Ricerca di Azure o dall'esterno del servizio, ad esempio dalla latenza di rete.  

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
