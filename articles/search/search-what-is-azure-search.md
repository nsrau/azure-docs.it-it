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
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 3720aed95706f6e8dd9a28085a6de0bc1f756a9b
ms.lasthandoff: 04/26/2017

---
# <a name="what-is-azure-search"></a>Che cos'è la Ricerca di Azure?
Ricerca di Azure è una soluzione di ricerca distribuita come servizio cloud che delega la gestione di server e infrastruttura a Microsoft, offrendo un servizio pronto per l'uso che consente di inserire dati e di aggiungere ricerche sulle applicazioni Web o mobili. Ricerca di Azure consente di implementare facilmente un'esperienza di ricerca affidabile nelle applicazioni usando una semplice [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) o [.NET SDK](search-howto-dotnet-sdk.md) senza dover gestire l'infrastruttura di ricerca o diventare esperti di ricerca.

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>Incorporare un'esperienza di ricerca avanzata nell'app o nel sito


### <a name="full-text-search-and-text-analysis"></a>Ricerca full-text e analisi del testo

Le query possono essere formulate mediante una [semplice sintassi di query](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), in grado di offrire operatori logici, operatori di ricerca di una frase, operatori di suffisso, operatori di precedenza. Inoltre, la [sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) consente la ricerca fuzzy, la ricerca per prossimità, l'aumento della priorità dei termini e le espressioni regolari. La Ricerca di Azure supporta anche gli [analizzatori lessicali personalizzati](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) per consentire all'applicazione di gestire query di ricerca complesse mediante la corrispondenza fonetica e le espressioni regolari.

### <a name="language-support"></a>Supporto per le lingue

La Ricerca di Azure supporta analizzatori lessicali per [56 lingue diverse](https://docs.microsoft.com/rest/api/searchservice/language-support). Tramite gli analizzatori Lucene e Microsoft (perfezionati da anni di elaborazione del linguaggio naturale in Office e Bing), la Ricerca di Azure può analizzare il testo nella casella di ricerca dell'applicazione per gestire in modo intelligente le funzionalità linguistiche di una lingua specifica, tra cui i tempi verbali, il genere, i sostantivi plurali irregolari (ad esempio "uomo" rispetto a "uomini"), la scomposizione delle parole, il ritorno a capo (per le lingue senza spazi) e altro.

### <a name="data-integration"></a>Integrazione dei dati

È possibile inserire strutture di dati JSON per popolare un indice di Ricerca di Azure. Inoltre, per le origini dati supportate, è possibile usare gli [indicizzatori](search-indexer-overview.md) per eseguire automaticamente una ricerca per indicizzazione nel database SQL di Azure, in Azure DocumentDB o nell'[archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md) per sincronizzare il contenuto dell'indice di ricerca con l'archivio dati primario.

È disponibile la **decifrazione del documento** per [leggere e indicizzare i principali formati di file](search-howto-indexing-azure-blob-storage.md) inclusi Microsoft Office, nonché i documenti PDF e HTML.

### <a name="search-experience"></a>Esperienza di ricerca

+ I **suggerimenti per la ricerca** possono essere abilitati per le barre di ricerca con completamento automatico e le query di digitazione. [suggeriti i documenti presenti nell'indice](https://docs.microsoft.com/rest/api/searchservice/suggesters) .

+ L'**esplorazione in base a facet** viene abilitata tramite un [singolo parametro di query](https://docs.microsoft.com/azure/search/search-faceted-navigation). La Ricerca di Azure restituisce una struttura di esplorazione in base a facet che è possibile usare come codice alla base di un elenco di categorie per i filtri autoindirizzati, ad esempio per filtrare gli elementi di un catalogo in base all'intervallo di prezzi o al marchio.

+ È possibile usare **filtri** per incorporare l'esplorazione in base a facet nell'interfaccia utente dell'applicazione, migliorare la formulazione di query e filtrare in base a criteri specificati dall'utente o dallo sviluppatore. Creare filtri mediante la [sintassi OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ L'**evidenziazione dei risultati** [applica la formattazione visiva a una parola chiave corrispondente nei risultati della ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). È possibile scegliere i campi che devono restituire i frammenti evidenziati.

+ **semplicità di assegnazione dei punteggi** . I [profili di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) vengono usati per modellare la rilevanza in funzione dei valori nei documenti stessi. Ad esempio, si potrebbe decidere che prodotti più recenti o prodotti scontati compaiano prima tra i risultati della ricerca. È inoltre possibile compilare profili di puntaggio utilizzando tag per il punteggio personalizzati in base alle preferenze di ricerca cliente che sono stati rilevati e archiviati separatamente.

+ **ordinamento** è disponibile per più campi tramite lo schema dell'indice e viene attivato o disattivato in fase di query con un solo parametro di ricerca.

+ **paging** e la limitazione dei risultati della ricerca [non comportano alcuna difficoltà grazie al controllo accurato](search-pagination-page-layout.md) che Ricerca di Azure offre sui risultati della ricerca.  

### <a name="geosearch"></a>Ricerca geografica

La Ricerca di Azure elabora, filtra e visualizza le posizioni geografiche in modo intelligente. Consente inoltre agli utenti di esplorare i dati in base alla prossimità di un risultato della ricerca in una posizione specificata o in base a una specifica area geografica. Il funzionamento viene spiegato nel video [Channel 9: Ricerca di Azure e dati geospaziali](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

### <a name="cloud-service-advantages"></a>Vantaggi del servizio cloud

+ **disponibilità elevata** garantisce un'esperienza del servizio di ricerca estremamente affidabile. Se si applica una scala corretta, [Ricerca di Azure offre un contratto di servizio con disponibilità del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **gestione completa** come soluzione end-to-end, Ricerca di Azure non richiede assolutamente alcun tipo di gestione dell'infrastruttura. Il servizio può essere adattato alle proprie esigenze scalando in due dimensioni per gestire più archivi di documenti, carichi di query maggiori o entrambi.

### <a name="monitoring-and-reporting"></a>Monitoraggio e reporting

+ I dati sull'**analisi del traffico di ricerca** possono essere [raccolti e analizzati](search-traffic-analytics.md) per dedurre informazioni sulla base delle parole digitate dagli utenti nella casella di ricerca.

+ Le metriche sulle query al secondo, sulla latenza e sulla limitazione vengono acquisite e inserite in report nelle pagine del portale, senza necessità di configurazioni aggiuntive. È anche possibile monitorare con facilita i conteggi di indici e documenti in modo da regolare la capacità in base alle necessità. 

### <a name="tools-for-prototyping-and-inspection"></a>Strumenti per la creazione di prototipi e l'ispezione

Nel portale è possibile usare la procedura guidata di **importazione dei dati** per configurare gli indicizzatori, indicizzare la finestra di progettazione e usare **Esplora ricerche** per inviare query per tutti gli indici direttamente dal portale di Azure dell'account, in modo da poter testare le query e migliorare i profili di punteggio. È inoltre possibile aprire un indice qualsiasi per visualizzarne lo schema.

## <a name="how-it-works"></a>Funzionamento
### <a name="step-1-provision-service"></a>Passaggio 1: Effettuare il provisioning del servizio
È possibile attivare un servizio Ricerca di Azure usando il [portale di Azure](https://portal.azure.com/) o l'[API Azure Resource Management](https://msdn.microsoft.com/library/azure/dn832684.aspx).

A seconda della configurazione del servizio di ricerca, si userà il livello gratuito del servizio, in condivisione con altri sottoscrittori di Ricerca di Azure o il [servizio a pagamento](https://azure.microsoft.com/pricing/details/search/) che riserva risorse all’uso esclusivo del servizio da parte di utenti singoli. Durante il provisioning del servizio, è anche possibile scegliere l'area del data center che ospita il servizio.

A seconda del livello del servizio scelto, è possibile ridimensionare il servizio in due sensi: 1) aggiungere repliche per aumentare la capacità di gestire ingenti carichi di query e 2) aggiungere partizioni per aumentare lo spazio di archiviazione per altri documenti. Grazie alla gestione separata dell’archiviazione dei documenti e della velocità effettiva delle query è possibile personalizzare il servizio di ricerca in base alle esigenze.

### <a name="step-2-create-index"></a>Passaggio 2: Creare un indice
Per poter caricare il contenuto nel servizio Ricerca di Azure, è innanzitutto necessario definire un indice di Ricerca di Azure. Un indice è simile a una tabella di database che contiene i dati e può accettare query di ricerca. È necessario definire lo schema di indice per eseguire il mapping della struttura dei documenti in cui si desidera eseguire la ricerca, in modo analogo ai campi in un database.

Lo schema di questi indici può essere creato nel portale di Azure o a livello di codice [usando .NET SDK](search-howto-dotnet-sdk.md) o [l'API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Una volta definito l’indice, è quindi possibile caricare i dati nel servizio Ricerca di Azure dove sono successivamente indicizzati.

### <a name="step-3-index-data"></a>Passaggio 3: Indicizzare i dati
Dopo aver definito i campi e gli attributi dell'indice, è possibile caricare il contenuto nell'indice, scegliendo il modello push o pull per caricare i dati.

Il modello pull è fornito tramite gli indicizzatori che possono essere configurati su richiesta o con aggiornamenti pianificati (vedere [operazioni indicizzatore (API REST del servizio Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), permettendo di acquisire facilmente dati e modifiche ai dati da Azure DocumentDB, database SQL di Azure, archivio BLOB di Azure o SQL Server ospitato in una macchina virtuale di Azure.

Il modello push viene fornito tramite il componente SDK o l'API REST per l'invio di documenti aggiornati a un indice. È possibile eseguire il push dei dati da qualsiasi set di dati usando il formato JSON. Per informazioni sul caricamento dei dati, vedere [Aggiungere, aggiornare o eliminare documenti](https://msdn.microsoft.com/library/azure/dn798930.aspx) oppure [Come usare .NET SDK](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Passaggio 4: Eseguire la ricerca
Dopo avere compilato l'indice di Ricerca di Azure, è ora possibile [eseguire query di ricerca](https://msdn.microsoft.com/library/azure/dn798927.aspx) nell'endpoint del servizio tramite semplici richieste HTTP con l'API REST o .NET SDK.

## <a name="rest-api--net-sdk"></a>API REST | .NET SDK

Mentre è possibile eseguire nel portale una serie di attività, la Ricerca di Azure è pensata per gli sviluppatori che vogliono integrare funzionalità di ricerca in applicazioni esistenti. Sono disponibili le seguenti interfacce di programmazione.

|Piattaforma |Descrizione |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | Comandi HTTP supportati da qualsiasi piattaforma e linguaggio di programmazione, inclusi Xamarin, Java e JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | Il wrapper .NET per l'API REST offre una codifica efficiente in C# e altri linguaggi di codice destinati a .NET Framework |

## <a name="free-trial"></a>Versione di prova gratuita
I sottoscrittori di Azure possono [effettuare il provisioning di un servizio al livello gratuito](search-create-service-portal.md).

Se non si dispone di una sottoscrizione, è possibile [aprire un account Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F): si riceveranno crediti da usare per provare i servizi di Azure a pagamento e, anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio Siti Web. La carta di credito non verrà mai addebitata, a meno l'utente non modifichi le impostazioni e che richieda esplicitamente di essere addebitato.

In alternativa, è possibile [attivare i benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento. 

## <a name="watch-a-short-video"></a>Guarda un breve video

I motori di ricerca sono i principali fautori del recupero delle informazioni nelle app per dispositivi mobili, sul Web e negli archivi dati aziendali. La Ricerca di Azure offre gli strumenti per creare un'esperienza di ricerca simile a quella dei siti Web commerciali di grandi dimensioni. In questo video di 9 minuti, il program manager Liam Cavanagh spiega i vantaggi dell'integrazione di un motore di ricerca nell'app, le funzionalità chiave di Ricerca di Azure e l'aspetto tipico di un flusso di lavoro. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Da 0 a 3 minuti: funzionalità chiave e casi d'uso.
+ Da 3 a 4 minuti: provisioning del servizio. 
+ Da 4 a 6 minuti: procedura guidata di importazione dei dati per creare un indice usando il set di dati predefinito per il mercato immobiliare.
+ Da 6 a 9 minuti: Esplora ricerche e varie query.



