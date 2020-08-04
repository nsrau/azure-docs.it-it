---
title: Novità di Ricerca cognitiva di Azure
description: Annunci di funzionalità nuove e migliorate, tra cui il servizio Ricerca di Azure rinominato in Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 07/15/2020
ms.openlocfilehash: 8c6486a3a64dcbe3dd37770844296da73724f3a3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076348"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novità di Ricerca cognitiva di Azure

Ecco cosa c'è di nuovo nel servizio. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati sul servizio.

## <a name="feature-announcements-in-2020"></a>Annunci di funzionalità nel 2020

### <a name="july-2020"></a>Luglio 2020

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Descrizione | Disponibilità  |
|---------|------------------|-------------|---------------|
| [Libreria client Azure.Search.Documents](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) | Azure SDK per .NET | Libreria client .NET rilasciata dal team di Azure SDK, progettata per coerenza con altre librerie client .NET. <br/><br/>La versione 11 è destinata all'API REST Ricerca api-version=2020-06-30, ma non supporta ancora l'archivio conoscenze, i filtri geografici o [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet). | Disponibile a livello generale. </br> Installare il [pacchetto Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) da NuGet. |
| [Libreria client azure.search.documents](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme?view=azure-python)  | Azure SDK per Python| Libreria client Python rilasciata dal team di Azure SDK, progettata per coerenza con altre librerie client Python. <br/><br/>La versione 11 è destinata all'API REST Ricerca api-version=2020-06-30. | Disponibile a livello generale. </br> Installare il [pacchetto azure-search-documents](https://pypi.org/project/azure-search-documents/) da PyPI. |
| [@azure/search-documents libreria client](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest)  | Azure SDK per JavaScript | Libreria client JavaScript rilasciata dal team di Azure SDK, progettata per coerenza con altre librerie client JavaScript. <br/><br/>La versione 11 è destinata all'API REST Ricerca api-version=2020-06-30. | Disponibile a livello generale. </br> Installare il [pacchetto @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) da npm. |

### <a name="june-2020"></a>Giugno 2020

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Descrizione | Disponibilità  |
|---------|------------------|-------------|---------------|
[**Archivio conoscenze**](knowledge-store-concept-intro.md) | Arricchimento con intelligenza artificiale | Output di un indicizzatore arricchito tramite intelligenza artificiale, che archivia il contenuto in Archiviazione di Azure per consentirne l'uso in altre app e processi. | Disponibile a livello generale. </br> Usare l'[API REST di ricerca 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) o versioni successive oppure il portale. |
| [**API REST di ricerca 2020-06-30**](https://docs.microsoft.com/rest/api/searchservice/) | REST | Nuova versione stabile delle API REST. Oltre all'archivio conoscenze, questa versione include miglioramenti relativi alla pertinenza e all'assegnazione di punteggi alle ricerche. | Disponibile a livello generale. |
| [**Algoritmo di pertinenza Okapi BM25**](https://en.wikipedia.org/wiki/Okapi_BM25) | Query | Nuovo algoritmo di classificazione della pertinenza usato automaticamente per tutti i nuovi servizi di ricerca creati dopo il 15 luglio. Per i servizi creati precedentemente è possibile acconsentire esplicitamente impostando la proprietà `similarity` sui campi di indice. | Disponibile a livello generale. </br> Usare l'[API REST di ricerca 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) o versioni successive oppure l'API REST 2019-05-06. |
| **executionEnvironment** | Sicurezza (indicizzatori) | Impostare in modo esplicito questa proprietà di configurazione dell'indicizzatore su `private` per forzare tutte le connessioni a origini dati esterne su un endpoint privato. Applicabile solo ai servizi di ricerca che usano il collegamento privato di Azure. | Disponibile a livello generale. </br> Usare l'[API REST di ricerca 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) per impostare questo parametro di configurazione generale. |

### <a name="may-2020-microsoft-build"></a>Maggio 2020 (Microsoft Build)

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Descrizione | Disponibilità  |
|---------|------------------|-------------|---------------|
| [**Sessioni di debug**](cognitive-search-debug-session.md) | Arricchimento con intelligenza artificiale | Le sessioni di debug forniscono un'interfaccia basata su portale per esaminare e risolvere i problemi relativi a un set di competenze esistente. Le correzioni create nella sessione di debug possono essere salvate nei set di competenze di produzione. Introduzione a [questa esercitazione](cognitive-search-tutorial-debug-sessions.md). | Anteprima pubblica, nel portale. |
| [**Regole IP per il supporto del firewall in ingresso**](service-configure-firewall.md) | Security | Limitano l'accesso a un endpoint di servizio di ricerca a indirizzi IP specifici. | Disponibile a livello generale. </br> Usare l'[API REST di gestione 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) o versioni successive oppure il portale. |
| [**Collegamento privato di Azure per un endpoint di ricerca privato**](service-create-private-endpoint.md) | Sicurezza| Scherma un servizio di ricerca dalla rete Internet pubblica eseguendolo come una risorsa collegamento privato, accessibile solo alle app client e ad altri servizi di Azure nella stessa rete virtuale. | Disponibile a livello generale. </br> Usare l'[API REST di gestione 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) o versioni successive oppure il portale. |
| [**Identità gestita dal sistema (anteprima)** ](search-howto-managed-identities-data-sources.md) | Sicurezza (indicizzatori) | Registra un servizio di ricerca come servizio attendibile con Azure Active Directory per configurare le connessioni a un'origine dati di Azure supportata per l'indicizzazione. Si applica agli [indicizzatori](search-indexer-overview.md) che inseriscono contenuto da origini dati di Azure, ad esempio database SQL di Azure, Azure Cosmos DB e Archiviazione di Azure. | Anteprima pubblica. </br> Usare il portale per registrare il servizio di ricerca. |
| [**Parametro di query sessionId**](index-similarity-and-scoring.md), [scoringStatistics=parametro globale](index-similarity-and-scoring.md#scoring-statistics) | Query (pertinenza) | Aggiungere sessionID a una query per stabilire una sessione per il calcolo dei punteggi di ricerca, con scoringStatistics=global per raccogliere i punteggi da tutte le partizioni, per calcoli più coerenti dei punteggi di ricerca. | Disponibile a livello generale. </br> Usare l'[API REST di ricerca 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) o versioni successive oppure l'API REST 2019-05-06. |
| [**featuresMode (anteprima)** ](index-similarity-and-scoring.md#featuresMode-param) | Query | Aggiungere questo parametro di query per espandere un punteggio di pertinenza in modo da visualizzare maggiori dettagli: punteggio di somiglianza per campo, frequenza di termini per campo e numero di token univoci corrispondenti per campo. È possibile utilizzare questi punti dati negli algoritmi di assegnazione dei punteggi personalizzati. Per un esempio in cui viene illustrata questa funzionalità, vedere [Aggiungere Machine Learning (LearnToRank) per la pertinenza della ricerca](https://github.com/Azure-Samples/search-ranking-tutorial). | Anteprima pubblica. </br> Usare l'[API REST di ricerca 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) o l'API REST 2019-05-06-Preview. |

### <a name="march-2020"></a>Marzo 2020

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Descrizione | Disponibilità  |
|---------|------------------|-------------|---------------|
| [**Eliminazione temporanea BLOB nativa (anteprima)** ](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | Indicizzatori | Un indicizzatore di Archiviazione BLOB di Azure in Ricerca cognitiva di Azure riconoscerà i BLOB in stato di eliminazione temporanea e rimuoverà il documento di ricerca corrispondente durante l'indicizzazione. | Anteprima pubblica. </br> Usare l'[API REST di ricerca 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) e l'API REST 2019-05-06-Preview, con Esegui indicizzatore su un'origine dati BLOB di Azure con l'eliminazione temporanea nativa abilitata. |
| [**API REST di gestione (2020-03-13)** ](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | Nuova API REST stabile per la creazione e la gestione di un servizio di ricerca. Aggiunge il supporto del firewall IP e del collegamento privato | Disponibile a livello generale. |

### <a name="february-2020"></a>Febbraio 2020

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Descrizione | Disponibilità  |
|---------|------------------|-------------|---------------|
| [**Rilevamento di informazioni personali (anteprima)** ](cognitive-search-skill-pii-detection.md) | Arricchimento con intelligenza artificiale | Nuova competenza cognitiva usata durante l'indicizzazione che estrae informazioni personali da un testo di input e offre diverse opzioni per mascherarle. | Anteprima pubblica. </br> Usare il portale oppure l'[API REST di ricerca 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) o l'API REST 2019-05-06-Preview. |
| [**Ricerca di entità personalizzate (anteprima)** ](cognitive-search-skill-custom-entity-lookup.md )| Arricchimento con intelligenza artificiale | Nuova competenza cognitiva che cerca il testo in un elenco personalizzato di parole e frasi definito dall'utente. Con questo elenco vengono etichettati tutti i documenti con entità corrispondenti. La competenza supporta anche un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze simili ma non esatte. | Anteprima pubblica. </br> Usare il portale oppure l'[API REST di ricerca 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) o l'API REST 2019-05-06-Preview. |

### <a name="january-2020"></a>Gennaio 2020

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Descrizione | Disponibilità  |
|---------|------------------|-------------|---------------|
| [**Chiavi di crittografia gestite dal cliente**](search-security-manage-encryption-keys.md) |Sicurezza | Aggiunge un ulteriore livello di crittografia oltre alla crittografia incorporata della piattaforma. Usando una chiave di crittografia creata e gestita personalmente, è possibile crittografare il contenuto dell'indice e le mappe di sinonimi prima che il payload raggiunga un servizio di ricerca. | Disponibile a livello generale. </br> Usare l'API REST di ricerca 2019-05-06 o versioni successive. Per il codice gestito, il pacchetto corretto è ancora [.NET SDK versione 8.0-preview](search-dotnet-sdk-migration-version-9.md) anche se la funzionalità non è più in anteprima. |
| [**Regole IP per il supporto del firewall in ingresso (anteprima)** ](service-configure-firewall.md) | Security | Limitano l'accesso a un endpoint di servizio di ricerca a indirizzi IP specifici. L'API di anteprima include le nuove proprietà **IpRule** e **NetworkRuleSet** nell'[API CreateOrUpdate ](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Questa funzionalità di anteprima è disponibile in specifiche aree. |  Anteprima pubblica con api-version=2019-10-01-Preview.  |
| [**Collegamento privato di Azure per un endpoint di ricerca privato (anteprima)** ](service-create-private-endpoint.md) | Sicurezza| Scherma un servizio di ricerca dalla rete Internet pubblica eseguendolo come una risorsa collegamento privato, accessibile solo alle app client e ad altri servizi di Azure nella stessa rete virtuale. | Anteprima pubblica con api-version=2019-10-01-Preview.  |

## <a name="feature-announcements-in-2019"></a>Annunci di funzionalità nel 2019

### <a name="december-2019"></a>Dicembre 2019

+ [Crea app demo (anteprima)](search-create-app-portal.md) è una nuova procedura guidata disponibile nel portale che consente di generare un file HTML scaricabile con accesso di sola lettura a un indice tramite query. Il file include uno script incorporato che esegue il rendering di un'app Web operativa di tipo "localhost", associata a un indice nel servizio di ricerca. Le pagine sono configurabili nella procedura guidata e possono contenere una barra di ricerca, l'area dei risultati, una barra laterale di spostamento e il supporto per le query con completamento automatico. È possibile modificare il codice HTML offline per estendere o personalizzare il flusso di lavoro o l'aspetto. Non è possibile estendere facilmente un'app demo in modo da includere i livelli di sicurezza e hosting che in genere sono necessari negli scenari di produzione. È quindi opportuno considerarla come uno strumento di convalida e di test e non un collegamento a un'app client completa.

+ [Creare un endpoint privato per connessioni sicure (anteprima)](service-create-private-endpoint.md) spiega come configurare un collegamento privato per proteggere le connessioni al servizio di ricerca. Questa funzionalità di anteprima è disponibile su richiesta e usa [Collegamento privato di Azure](../private-link/private-link-overview.md) e la [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) come parte della soluzione.

### <a name="november-2019---ignite-conference"></a>Novembre 2019 - Conferenza Ignite

+ L'[arricchimento incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) aggiunge la memorizzazione nella cache e il supporto di più stati a una pipeline di arricchimento, consentendo di eseguire specifici passaggi o fasi senza perdere il contenuto già elaborato. In precedenza, tutte le modifiche apportate a una pipeline di arricchimento richiedevano una ricompilazione completa. Con l'arricchimento incrementale, l'output di analisi costose viene mantenuto, in particolare l'analisi delle immagini.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ L'[estrazione di documenti (anteprima)](cognitive-search-skill-document-extraction.md) è una competenza cognitiva usata durante l'indicizzazione che consente di estrarre contenuto da un file all'interno di un set di competenze. In precedenza, il cracking di documenti si verificava solo prima dell'esecuzione del set di competenze. Con l'aggiunta di questa competenza, è anche possibile eseguire questa operazione all'interno dell'esecuzione del set di competenze.

+ [Traduzione testuale](cognitive-search-skill-text-translation.md) è una competenza cognitiva usata durante l'indicizzazione che valuta il testo e, per ogni record, lo restituisce convertito nella lingua di destinazione specificata.

+ I [modelli di Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) possono velocizzare le visualizzazioni e le analisi di contenuto arricchito in un archivio conoscenze in Power BI Desktop. Questo modello è progettato per le proiezioni di tabelle di Azure create tramite l'[Importazione guidata dati](knowledge-store-create-portal.md).

+ Gli indicizzatori ora supportano [Azure Data Lake Storage Gen2 (anteprima)](search-howto-index-azure-data-lake-storage.md), l'[API Gremlin di Cosmos DB (anteprima)](search-howto-index-cosmosdb.md) e l'[API Cassandra di Cosmos DB (anteprima)](search-howto-index-cosmosdb.md). È possibile iscriversi usando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Quando si verrà accettati nel programma di anteprima, si riceverà un messaggio di posta elettronica di conferma.

### <a name="july-2019"></a>Luglio 2019

+ Disponibile a livello generale in [Azure per enti pubblici](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nuovo nome del servizio

Il servizio Ricerca di Azure è stato rinominato in **Ricerca cognitiva di Azure** per riflettere l'uso più ampio (ancora facoltativo) delle competenze cognitive e dell'elaborazione di intelligenza artificiale nelle operazioni principali. Le versioni delle API, i pacchetti NuGet, gli spazi dei nomi e gli endpoint sono rimasti invariati. Le soluzioni di ricerca nuove ed esistenti non sono interessate dalla modifica del nome del servizio.

## <a name="service-updates"></a>Aggiornamenti del servizio

Gli [annunci sugli aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=search&status=all) per Ricerca cognitiva di Azure sono reperibili nel sito Web di Azure.