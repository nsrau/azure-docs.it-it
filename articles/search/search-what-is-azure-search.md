---
title: Informazioni su Ricerca di Azure | Documentazione Microsoft
description: "Ricerca di Azure è un servizio di ricerca interamente ospitato sul cloud. In questa panoramica delle funzionalità sono fornite ulteriori informazioni sul servizio."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/24/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: db227bfea10255322c090e68b197cfb2dd1cf15b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="what-is-azure-search"></a>Che cos'è la Ricerca di Azure?
Ricerca di Azure è una soluzione di ricerca distribuita come servizio cloud che delega la gestione di server e infrastruttura a Microsoft, offrendo un servizio pronto per l'uso che consente di inserire dati e di aggiungere ricerche sulle applicazioni Web o mobili. Ricerca di Azure consente di implementare facilmente un'esperienza di ricerca affidabile nelle applicazioni usando una semplice [API REST](/rest/api/searchservice/) o [.NET SDK](search-howto-dotnet-sdk.md) senza dover gestire l'infrastruttura di ricerca o diventare esperti di ricerca.

<a name="feature-drilldown"></a>

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>Incorporare un'esperienza di ricerca avanzata nell'app o nel sito 

Informazioni sulle funzionalità in Ricerca di Azure.

### <a name="full-text-search-and-text-analysis"></a>Ricerca full-text e analisi del testo

[Ricerca full-text](https://en.wikipedia.org/wiki/Full_text_search) è un caso di utilizzo principale per la maggior parte delle app basate sulla ricerca. In Ricerca di Azure le query possono essere formulate usando una [semplice sintassi di query](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), in grado di offrire operatori logici, operatori di ricerca di una frase, operatori di suffisso, operatori di precedenza. Inoltre, la [sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) consente la ricerca fuzzy, la ricerca per prossimità, l'aumento della priorità dei termini e le espressioni regolari. La Ricerca di Azure supporta anche gli [analizzatori lessicali personalizzati](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) per consentire all'applicazione di gestire query di ricerca complesse mediante la corrispondenza fonetica e le espressioni regolari.

### <a name="language-support"></a>Supporto per le lingue

La Ricerca di Azure supporta analizzatori lessicali per [56 lingue diverse](https://docs.microsoft.com/rest/api/searchservice/language-support). Tramite gli analizzatori Lucene e Microsoft (perfezionati da anni di elaborazione del linguaggio naturale in Office e Bing), la Ricerca di Azure può analizzare il testo nella casella di ricerca dell'applicazione per gestire in modo intelligente le funzionalità linguistiche di una lingua specifica, tra cui i tempi verbali, il genere, i sostantivi plurali irregolari (ad esempio "uomo" rispetto a "uomini"), la scomposizione delle parole, il ritorno a capo (per le lingue senza spazi) e altro.

### <a name="data-integration"></a>Integrazione dei dati

È possibile inserire strutture di dati JSON per popolare un indice di Ricerca di Azure. Per le origini dati supportate è anche possibile usare gli [indicizzatori](search-indexer-overview.md) per eseguire automaticamente una ricerca per indicizzazione nel [database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), in [Azure Cosmos DB](search-howto-index-documentdb.md) o nell'[archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md) per sincronizzare il contenuto dell'indice di ricerca con l'archivio dati primario.

È disponibile la *decifrazione del documento* per [leggere e indicizzare i principali formati di file](search-howto-indexing-azure-blob-storage.md) inclusi Microsoft Office, nonché i documenti PDF e HTML.

### <a name="search-experience"></a>Esperienza di ricerca

+ I **suggerimenti per la ricerca** possono essere abilitati per le barre di ricerca con completamento automatico e le query di digitazione. [suggeriti i documenti presenti nell'indice](https://docs.microsoft.com/rest/api/searchservice/suggesters) .

+ L'**Esplorazione in base a facet** viene abilitata tramite un [singolo parametro di query](https://docs.microsoft.com/azure/search/search-faceted-navigation). La Ricerca di Azure restituisce una struttura di esplorazione in base a facet che è possibile usare come codice alla base di un elenco di categorie per i filtri autoindirizzati, ad esempio per filtrare gli elementi di un catalogo in base all'intervallo di prezzi o al marchio.

+ È possibile usare **filtri** per incorporare l'esplorazione in base a facet nell'interfaccia utente dell'applicazione, migliorare la formulazione di query e filtrare in base a criteri specificati dall'utente o dallo sviluppatore. Creare filtri mediante la [sintassi OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ L'**evidenziazione dei risultati** [applica la formattazione visiva a una parola chiave corrispondente nei risultati della ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). È possibile scegliere i campi che devono restituire i frammenti evidenziati.

+ **semplicità di assegnazione dei punteggi** . I [profili di punteggio](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) vengono usati per modellare la rilevanza in funzione dei valori nei documenti stessi. Ad esempio, si potrebbe decidere che prodotti più recenti o prodotti scontati compaiano prima tra i risultati della ricerca. È inoltre possibile compilare profili di puntaggio utilizzando tag per il punteggio personalizzati in base alle preferenze di ricerca cliente che sono stati rilevati e archiviati separatamente.

+ **ordinamento** è disponibile per più campi tramite lo schema dell'indice e viene attivato o disattivato in fase di query con un solo parametro di ricerca.

+ **paging** e la limitazione dei risultati della ricerca [non comportano alcuna difficoltà grazie al controllo accurato](search-pagination-page-layout.md) che Ricerca di Azure offre sui risultati della ricerca.  

### <a name="geosearch"></a>Ricerca geografica

La Ricerca di Azure elabora, filtra e visualizza le posizioni geografiche in modo intelligente. Consente agli utenti di esplorare i dati in base alla prossimità di un risultato della ricerca a una posizione fisica. Il funzionamento viene spiegato nel video [Channel 9: Ricerca di Azure e dati geospaziali](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

### <a name="monitoring-and-reporting"></a>Monitoraggio e reporting

+ I dati sull'**analisi del traffico di ricerca** possono essere [raccolti e analizzati](search-traffic-analytics.md) per dedurre informazioni sulla base delle parole digitate dagli utenti nella casella di ricerca.

+ Le metriche sulle query al secondo, sulla latenza e sulla limitazione vengono acquisite e inserite in report nelle pagine del portale, senza necessità di configurazioni aggiuntive. È anche possibile monitorare con facilita i conteggi di indici e documenti in modo da regolare la capacità in base alle necessità. 

### <a name="tools-for-prototyping-and-inspection"></a>Strumenti per la creazione di prototipi e l'ispezione

Nel portale è possibile usare la procedura guidata di **importazione dei dati** per configurare gli indicizzatori, indicizzare la finestra di progettazione e usare **Esplora ricerche** per testare le query e migliorare i profili di punteggio. È inoltre possibile aprire un indice qualsiasi per visualizzarne lo schema.

<a name="cloud-service-advantage"></a>

### <a name="cloud-service-advantages"></a>Vantaggi del servizio cloud

+ **disponibilità elevata** garantisce un'esperienza del servizio di ricerca estremamente affidabile. Se si applica una scala corretta, [Ricerca di Azure offre un contratto di servizio con disponibilità del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **Gestione completa e scalabile** come soluzione end-to-end, Ricerca di Azure non richiede assolutamente alcun tipo di gestione dell'infrastruttura. Il servizio può essere adattato alle proprie esigenze scalando in due dimensioni per gestire più archivi di documenti, carichi di query maggiori o entrambi.

## <a name="how-it-works"></a>Funzionamento
### <a name="step-1-provision-service"></a>Passaggio 1: Effettuare il provisioning del servizio
È possibile attivare un servizio Ricerca di Azure nel [Portale di Azure](https://portal.azure.com/) o attraverso l'[API Azure Resource Management](/rest/api/searchmanagement/). È possibile scegliere il servizio gratuito condiviso con altri sottoscrittori, o un [livello a pagamento](https://azure.microsoft.com/pricing/details/search/) che dedica risorse usate solo dal proprio servizio.

Per i livelli a pagamento, è possibile scalare il servizio in due dimensioni: 

- Aggiungere Repliche per aumentare la capacità di gestire carichi di lavoro elevati di query.   
- Aggiungere Partizioni per aumentare la risorsa di archiviazione per più documenti. 

Tramite la gestione separata della risorsa di archiviazione del documento e della velocità effettiva della query, è possibile calibrare le risorse in base ai requisiti di produzione.

### <a name="step-2-create-index"></a>Passaggio 2: Creare un indice
Per poter caricare il contenuto ricercabile, è innanzitutto necessario definire un indice di Ricerca di Azure. Un indice è simile a una tabella di database che contiene i dati e può accettare query di ricerca. È necessario definire lo schema di indice per riflettere la struttura dei documenti in cui si desidera eseguire la ricerca, in modo analogo ai campi in un database.

È possibile creare uno schema nel portale di Azure o a livello di codice usando [.NET SDK](search-howto-dotnet-sdk.md) o l'[API REST](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>Passaggio 3: Indicizzare i dati
Dopo aver definito un indice, si è pronti per caricare il contenuto. È possibile usare un modello push o pull.

Il modello pull recupera i dati da origini dati esterne. È supportato tramite *indicizzatori* che semplificano e automatizzano degli aspetti dell'operazione di inserimento di dati, ad esempio la connessione, la lettura e la serializzazione dei dati. Gli [indicizzatori](/rest/api/searchservice/Indexer-operations) sono disponibili per Azure Cosmos DB, Database SQL di Azure, Archiviazione BLOB di Azure e SQL Server ospitato in una macchina virtuale di Azure. È possibile configurare un indicizzatore per l'aggiornamento dati su richiesta o pianificato.

Il modello push viene fornito tramite il componente SDK o l'API REST per l'invio di documenti aggiornati a un indice. È possibile eseguire il push dei dati da qualsiasi set di dati usando il formato JSON. Per informazioni sul caricamento dei dati, vedere [Aggiungere, aggiornare o eliminare documenti](/rest/api/searchservice/addupdate-or-delete-documents) oppure [Come usare .NET SDK](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Passaggio 4: Eseguire la ricerca
Dopo avere compilato un indice, è possibile [eseguire query di ricerca](/rest/api/searchservice/Search-Documents) nell'endpoint di servizio tramite semplici richieste HTTP con l'API REST o .NET SDK.

## <a name="how-it-compares"></a>Confronto

I clienti spesso chiedono com'è la [ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md) in confronto alla [ricerca full-text](https://en.wikipedia.org/wiki/Full_text_search) nel loro prodotto database. La risposta è che le funzionalità del linguaggio di Ricerca di Azure sono più ricche e più flessibili, con il supporto per query Lucene, gli analizzatori di linguaggi Lucene e Microsoft, gli analizzatori personalizzati per la fonetica o altri input specializzati e la possibilità di unire i dati da più origini nell'indice di ricerca. 

Un altro punto di flesso è che una soluzione di ricerca fa riferimento all'intera esperienza di ricerca. Ad esempio, si può volere un punteggio personalizzato dei risultati, un'esplorazione in base a facet per i filtri autoindirizzati, un'evidenziazione dei risultati e suggerimenti di query typeahead. 

Gli strumenti per il monitoraggio e la comprensione delle attività di query possono anche fattorizzarsi in una decisione di soluzione della ricerca. Ad esempio, Ricerca di Azure supporta l'[Analisi del traffico di ricerca](search-traffic-analytics.md) per la metrica sul tasso di click-through, le ricerche più frequenti, le ricerche senza clic e così via. Nei prodotti in cui la ricerca è un componente aggiuntivo è improbabile trovare queste funzionalità.

L'uso delle risorse è un'altra considerazione. La ricerca del linguaggio naturale è spesso impegnativa da un punto di vista computazionale. Alcuni dei nostri clienti hanno scaricato le operazioni di ricerca in Ricerca di Azure per conservare le risorse del computer per l'elaborazione delle transazioni. Dalla ricerca di esternalizzazione, è possibile modificare facilmente la scala in modo che corrisponda al volume della query.

Dopo aver deciso di passare alla ricerca dedicata, la scelta successiva è tra un servizio cloud o un server locale. Un servizio cloud è la scelta giusta se si desidera una [soluzione chiavi in mano con sovraccarico e manutenzione minimi e scala regolabile](#cloud-service-advantage).

All'interno del paradigma di cloud, molti provider offrono funzionalità di base simili, con ricerca full-text, ricerca geografica e la possibilità di gestire un certo livello di ambiguità negli input di ricerca. In genere sono una [funzionalità specializzata](#feature-drilldown) o la semplicità e la facilità d'uso generale di API, strumenti e gestione a determinare la scelta ideale.

Tra i provider di cloud, Ricerca di Azure offre le migliori prestazioni nei carichi di lavoro con ricerca full-text in archivi di contenuti e database in Azure, per le app che fanno principalmente affidamento sulla ricerca per il recupero delle informazioni e l'esplorazione dei contenuti. Ecco i principali vantaggi:

+ Integrazione dei dati in Azure (crawler) al livello di indicizzazione
+ Portale di Azure per la gestione centralizzata
+ Scalabilità, affidabilità e massima disponibilità proprie di Azure
+ Analisi linguistica e personalizzata, con analizzatori per una robusta ricerca full-text in 56 lingue
+ [Funzionalità di base comuni per le app basate sulla ricerca](#feature-drilldown): assegnazione dei punteggi, esplorazione in base a facet, suggerimenti, sinonimi, ricerca geografica e così via.

> [!Note]
> Per maggiore chiarezza, le origini dati diverse da Azure non sono completamente supportate, ma si basano su una metodologia di push più dispendiosa a livello di codice piuttosto che sugli indicizzatori. Usando le API, è possibile reindirizzare qualsiasi raccolta di documenti JSON in un indice di Ricerca di Azure.

Tra i clienti, i casi in grado di sfruttare la gamma più ampia di funzionalità di Ricerca di Azure includono cataloghi online, programmi line of business e applicazioni di individuazione di documenti.

## <a name="rest-api--net-sdk"></a>API REST | .NET SDK

Mentre è possibile eseguire nel portale molte attività, la Ricerca di Azure è pensata per gli sviluppatori che vogliono integrare funzionalità di ricerca in applicazioni esistenti. Sono disponibili le seguenti interfacce di programmazione.

|Piattaforma |Descrizione |
|-----|------------|
|[REST](/rest/api/searchservice/) | Comandi HTTP supportati da qualsiasi piattaforma e linguaggio di programmazione, inclusi Xamarin, Java e JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | Il wrapper .NET per l'API REST offre una codifica efficiente in C# e altri linguaggi di codice destinati a .NET Framework |

## <a name="free-trial"></a>Versione di prova gratuita
I sottoscrittori di Azure possono [effettuare il provisioning di un servizio al livello gratuito](search-create-service-portal.md).

Se non si è abbonati, è possibile [aprire un account Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Si riceveranno crediti per provare i servizi di Azure a pagamento. Quando i crediti saranno esauriti, sarà possibile mantenere l'account e usare i [servizi di Azure gratuiti](https://azure.microsoft.com/free/). Verranno applicati addebiti alla carta di credito solo se l'utente modifica le impostazioni e richiede esplicitamente l'addebito.

In alternativa, è possibile [attivare i benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento. 

## <a name="watch-a-short-video"></a>Guarda un breve video

I motori di ricerca sono i principali fautori del recupero delle informazioni nelle app per dispositivi mobili, sul Web e negli archivi dati aziendali. La Ricerca di Azure offre gli strumenti per creare un'esperienza di ricerca simile a quella dei siti Web commerciali di grandi dimensioni.

In questo video di 9 minuti prodotto dal program manager Liam Cavanagh, sono presenti informazioni su come l'app possa trarre vantaggio dall'integrazione di un motore di ricerca. Brevi demo riguardano le funzionalità principali in Ricerca di Azure e l'aspetto di un tipico flusso di lavoro. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Da 0 a 3 minuti: funzionalità chiave e casi d'uso.
+ Da 3 a 4 minuti: provisioning del servizio. 
+ Da 4 a 6 minuti: procedura guidata di importazione dei dati per creare un indice usando il set di dati predefinito per il mercato immobiliare.
+ Da 6 a 9 minuti: Esplora ricerche e varie query.



