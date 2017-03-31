---
title: Informazioni su Ricerca di Azure | Documentazione Microsoft
description: "Motore di ricerca con funzionalità avanzate per la barra di ricerca in app, archivi di dati e file aziendali e siti Web personalizzati."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
experiment_id: heidist-20170221
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 8f4947ba2641bc8a39a71945ff2a6818d1ff7830
ms.lasthandoff: 03/08/2017

---
# <a name="what-is-azure-search"></a>Che cos'è la Ricerca di Azure?

La Ricerca di Azure offre un motore di ricerca dedicato e programmabile con comportamenti di ricerca intelligente per inserire barre di ricerca dei contenuti su siti Web, app e archivi di file o dati aziendali. 

![Barra di ricerca con tecnologia Ricerca di Azure](./media/search-what-is-azure-search/search-powered-by-azsearch3.png)

Un'esperienza di ricerca solida è una necessità per la maggior parte delle app Web e per dispositivi mobili. Nonostante la complessità tecnica alla base dell'esperienza, poter disporre di una ricerca full-text con termini di query a completamento automatico, correzioni di ortografia e corrispondenze basate su input identici a livello semantico ma diversi in superficie, ad esempio "macchina" e "auto", è il minimo che gli utenti si aspettano. I requisiti operativi di scalabilità, affidabilità e sincronizzazione tra ricerche e archivi dati di back-end sono altrettanto importanti. 

Ricerca di Azure offre funzionalità complete per soddisfare sia i requisiti di ricerca che quelli operativi.

![Barra di ricerca e pagina di ricerca personalizzata con funzionalità tipiche](./media/search-what-is-azure-search/search-page-callouts3.png)

## <a name="how-it-works"></a>Funzionamento

Per usare la ricerca di Azure, eseguire il provisioning di un servizio gratuito o a pagamento nella sottoscrizione di Azure, creare e caricare un indice contenente il contenuto ricercabile e quindi chiamare le API per inoltrare le richieste di ricerca e gestire i risultati. Se si desidera avere risorse dedicate in base alla scalabilità, è necessario un servizio a pagamento.

|Livello |Descrizione |
|-----|------------|
|[Free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) | Un servizio condiviso con un provisioning rapido, usato per piccoli carichi di lavoro. Comune negli scenari di apprendimento e valutazione. |
|[Fatturabile](search-sku-tier.md) | Un servizio dedicato a livelli di capacità progressivi, da Basic a Standard High Density. Ideale per un'ampia gamma di configurazioni di distribuzione con piani tariffari diversi.|

La Ricerca di Azure viene eseguita nel cloud come servizio gestito da Microsoft. Può essere integrata con codice personalizzato in qualsiasi piattaforma di applicazione. Il servizio completamente gestito e i contenuti privati sono disponibili globalmente, programmabili, scalabili e ripristinabili. 

I servizi dedicati vengono eseguiti 24 ore su 24 e 7 giorni su 7 in base alla scalabilità, con [contratti di servizio del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/) supportati da Microsoft Azure.

## <a name="how-it-compares"></a>Confronto

Alcuni provider di servizi cloud offrono motori di ricerca personalizzati con funzionalità per disporre di una barra di ricerca nelle app personalizzate. Alcuni offrono simili funzionalità di base, con ricerca full-text, ricerca geografica e la possibilità di gestire un certo livello di ambiguità negli input di ricerca. In genere sono una [funzionalità specializzata](#feature-drilldown) o la semplicità e la facilità d'uso generale di API, strumenti e gestione a determinare la scelta ideale.

Paragonata con altre soluzioni di ricerca, Ricerca di Azure offre le migliori prestazioni nei carichi di lavoro con ricerca full-text in archivi di contenuti e database in Azure, con un livello di funzionalità aggiuntive necessarie per le app che fanno principalmente affidamento sulla ricerca per il recupero delle informazioni e l'esplorazione dei contenuti. Ecco i principali vantaggi:

+ Integrazione dei dati in Azure (crawler) al livello di indicizzazione
+ Portale di Azure per la gestione centralizzata
+ Scalabilità, affidabilità e massima disponibilità proprie di Azure
+ Analisi linguistica e personalizzata, con analizzatori per una robusta ricerca full-text in 56 lingue
+ Funzionalità di base comuni per le app basate sulla ricerca, incluse rilevanza, esplorazione in base a facet, suggerimenti, sinonimi, ricerca geografica e così via (elencate di seguito).

> [!Note]
> Le origini dati non di Azure sono pienamente supportate. È possibile reindirizzare qualsiasi raccolta di documenti JSON in un indice di Ricerca di Azure.

I casi di utilizzo in grado di sfruttare la gamma più ampia di funzionalità di Ricerca di Azure includono cataloghi online, programmi line of business e applicazioni di individuazione di documenti.

### <a name="feature-drilldown"></a>Funzione in dettaglio

#### <a name="full-text-search-and-text-analysis"></a>Ricerca full-text e analisi del testo

Le query possono essere formulate mediante una [semplice sintassi di query](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), in grado di offrire operatori logici, operatori di ricerca di una frase, operatori di suffisso, operatori di precedenza. Inoltre, la [sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) consente la ricerca fuzzy, la ricerca per prossimità, l'aumento della priorità dei termini e le espressioni regolari. La Ricerca di Azure supporta anche gli [analizzatori lessicali personalizzati](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) per consentire all'applicazione di gestire query di ricerca complesse mediante la corrispondenza fonetica e le espressioni regolari.

#### <a name="language-support"></a>Supporto per le lingue

La Ricerca di Azure supporta analizzatori lessicali per [56 lingue diverse](https://docs.microsoft.com/rest/api/searchservice/language-support). Tramite gli analizzatori Lucene e Microsoft (perfezionati da anni di elaborazione del linguaggio naturale in Office e Bing), la Ricerca di Azure può analizzare il testo nella casella di ricerca dell'applicazione per gestire in modo intelligente le funzionalità linguistiche di una lingua specifica, tra cui i tempi verbali, il genere, i sostantivi plurali irregolari (ad esempio "uomo" rispetto a "uomini"), la scomposizione delle parole, il ritorno a capo (per le lingue senza spazi) e altro.

#### <a name="data-integration"></a>Integrazione dei dati

È possibile inserire strutture di dati JSON per popolare un indice di Ricerca di Azure. Inoltre, per le origini dati supportate, è possibile usare gli [indicizzatori](search-indexer-overview.md) per eseguire automaticamente una ricerca per indicizzazione nel database SQL di Azure, in Azure DocumentDB o nell'[archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md) per sincronizzare il contenuto dell'indice di ricerca con l'archivio dati primario.

È disponibile la **decifrazione del documento** per [leggere e indicizzare i principali formati di file](search-howto-indexing-azure-blob-storage.md) inclusi Microsoft Office, nonché i documenti PDF e HTML.

#### <a name="search-experience"></a>Esperienza di ricerca

+ I **suggerimenti per la ricerca** possono essere abilitati per le barre di ricerca con completamento automatico e le query di digitazione. [suggeriti i documenti presenti nell'indice](https://docs.microsoft.com/rest/api/searchservice/suggesters) .

+ L'**esplorazione in base a facet** viene abilitata tramite un [singolo parametro di query](https://docs.microsoft.com/azure/search/search-faceted-navigation). La Ricerca di Azure restituisce una struttura di esplorazione in base a facet che è possibile usare come codice alla base di un elenco di categorie per i filtri autoindirizzati, ad esempio per filtrare gli elementi di un catalogo in base all'intervallo di prezzi o al marchio.

+ È possibile usare **filtri** per incorporare l'esplorazione in base a facet nell'interfaccia utente dell'applicazione, migliorare la formulazione di query e filtrare in base a criteri specificati dall'utente o dallo sviluppatore. Creare filtri mediante la [sintassi OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ L'**evidenziazione dei risultati** [applica la formattazione visiva a una parola chiave corrispondente nei risultati della ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). È possibile scegliere i campi che devono restituire i frammenti evidenziati.

+ **semplicità di assegnazione dei punteggi** . I [profili di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) vengono usati per modellare la rilevanza in funzione dei valori nei documenti stessi. Ad esempio, si potrebbe decidere che prodotti più recenti o prodotti scontati compaiano prima tra i risultati della ricerca. È inoltre possibile compilare profili di puntaggio utilizzando tag per il punteggio personalizzati in base alle preferenze di ricerca cliente che sono stati rilevati e archiviati separatamente.

+ **ordinamento** è disponibile per più campi tramite lo schema dell'indice e viene attivato o disattivato in fase di query con un solo parametro di ricerca.

+ **paging** e la limitazione dei risultati della ricerca [non comportano alcuna difficoltà grazie al controllo accurato](search-pagination-page-layout.md) che Ricerca di Azure offre sui risultati della ricerca.  

#### <a name="geosearch"></a>Ricerca geografica

La Ricerca di Azure elabora, filtra e visualizza le posizioni geografiche in modo intelligente. Consente inoltre agli utenti di esplorare i dati in base alla prossimità di un risultato della ricerca in una posizione specificata o in base a una specifica area geografica. Il funzionamento viene spiegato nel video [Channel 9: Ricerca di Azure e dati geospaziali](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

#### <a name="cloud-service-advantages"></a>Vantaggi del servizio cloud

+ **disponibilità elevata** garantisce un'esperienza del servizio di ricerca estremamente affidabile. Se si applica una scala corretta, [Ricerca di Azure offre un contratto di servizio con disponibilità del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **gestione completa** come soluzione end-to-end, Ricerca di Azure non richiede assolutamente alcun tipo di gestione dell'infrastruttura. Il servizio può essere adattato alle proprie esigenze scalando in due dimensioni per gestire più archivi di documenti, carichi di query maggiori o entrambi.

#### <a name="monitoring-and-reporting"></a>Monitoraggio e reporting

+ I dati sull'**analisi del traffico di ricerca** possono essere [raccolti e analizzati](search-traffic-analytics.md) per dedurre informazioni sulla base delle parole digitate dagli utenti nella casella di ricerca.

+ Le metriche sulle query al secondo, sulla latenza e sulla limitazione vengono acquisite e inserite in report nelle pagine del portale, senza necessità di configurazioni aggiuntive. È anche possibile monitorare con facilita i conteggi di indici e documenti in modo da regolare la capacità in base alle necessità. 

#### <a name="tools-for-prototyping-and-inspection"></a>Strumenti per la creazione di prototipi e l'ispezione

Nel portale è possibile usare la procedura guidata di **importazione dei dati** per configurare gli indicizzatori, indicizzare la finestra di progettazione e usare **Esplora ricerche** per inviare query per tutti gli indici direttamente dal portale di Azure dell'account, in modo da poter testare le query e migliorare i profili di punteggio. È inoltre possibile aprire un indice qualsiasi per visualizzarne lo schema.

## <a name="how-to-get-started"></a>Attività iniziali

Iniziare con un servizio gratuito, quindi [creare ed eseguire la query per un indice usando i dati di esempio predefiniti](search-get-started-portal.md). È possibile usare il portale per tutte le attività, un modo semplice per provare la Ricerca di Azure prima di dover scrivere codice.

1. I sottoscrittori di Azure possono [effettuare il provisioning di un servizio al livello gratuito](search-create-service-portal.md).

  Coloro che non possiedono una sottoscrizione possono [aprire un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e usare i crediti gratuiti per provare i servizi di Azure a pagamento. Dopo avere consumato i crediti, è possibile mantenere l'account e usare i servizi di Azure gratuiti, ad esempio Siti Web. Verranno applicati addebiti alla carta di credito solo se l'utente modifica le impostazioni e richiede esplicitamente l'addebito.

  In alternativa, [attivare i benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). con l'abbonamento MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento. 

2. Eseguire la procedura guidata di **importazione dei dati** nel [set di dati di esempio predefinito per il mercato immobiliare](search-get-started-portal.md#create-index).

  Questa procedura guidata può generare e caricare un indice dalla maggior parte delle origini dati di Azure. Il codice è necessario per le origini dati non espressamente supportate dalla procedura guidata.

3. Usare [Esplora ricerche](search-get-started-portal.md#query-index) per eseguire una query per l'indice.

## <a name="rest-api--net-sdk"></a>API REST | .NET SDK

Mentre è possibile eseguire nel portale una serie di attività, la Ricerca di Azure è pensata per gli sviluppatori che vogliono integrare funzionalità di ricerca in applicazioni esistenti. Sono disponibili le seguenti interfacce di programmazione.

|Piattaforma |Descrizione |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | Comandi HTTP supportati da qualsiasi piattaforma e linguaggio di programmazione, inclusi Xamarin, Java e JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | Il wrapper .NET per l'API REST offre una codifica efficiente in C# e altri linguaggi di codice destinati a .NET Framework |

## <a name="watch-a-short-video"></a>Guarda un breve video

I motori di ricerca sono i principali fautori del recupero delle informazioni nelle app per dispositivi mobili, sul Web e negli archivi dati aziendali. La Ricerca di Azure offre gli strumenti per creare un'esperienza di ricerca simile a quella dei siti Web commerciali di grandi dimensioni. In questo video di 9 minuti, il program manager Liam Cavanagh spiega i vantaggi dell'integrazione di un motore di ricerca nell'app, le funzionalità chiave di Ricerca di Azure e l'aspetto tipico di un flusso di lavoro. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Da&0; a&3; minuti: funzionalità chiave e casi d'uso.
+ Da&3; a&4; minuti: provisioning del servizio. 
+ Da&4; a&6; minuti: procedura guidata di importazione dei dati per creare un indice usando il set di dati predefinito per il mercato immobiliare.
+ Da&6; a&9; minuti: Esplora ricerche e varie query.


