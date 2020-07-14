---
title: Novità di Ricerca cognitiva di Azure
description: Annunci di funzionalità nuove e migliorate, tra cui il servizio Ricerca di Azure rinominato in Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/30/2020
ms.openlocfilehash: 078892691bfaec62f71f9d601a42de3f80221149
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958158"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novità di Ricerca cognitiva di Azure

Ecco cosa c'è di nuovo nel servizio. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati sul servizio.

## <a name="feature-announcements"></a>Annunci di funzionalità

### <a name="june-2020"></a>Giugno 2020

+ L'[archivio conoscenze](knowledge-store-concept-intro.md) è ora disponibile a livello generale.

+ L'[API REST del servizio di ricerca 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) è la nuova versione stabile delle API REST. Oltre all'archivio conoscenze, questa versione disponibile a livello generale include miglioramenti relativi alla pertinenza e all'assegnazione di punteggi alle ricerche.

+ Il nuovo algoritmo di classificazione della pertinenza è ora [BM25](https://en.wikipedia.org/wiki/Okapi_BM25) per qualsiasi nuovo servizio creato. Per i servizi esistenti è possibile acconsentire esplicitamente impostando la proprietà `similarity` sui campi di indice. Questa proprietà è disponibile a livello generale.

+ Il nuovo indicizzatore `executionEnvironment` può essere impostato in modo esplicito su `private`. Questa funzionalità supporta l'accesso dell'indicizzatore ai dati esterni tramite endpoint privati ed è disponibile a livello generale.

+ [Azure Machine Learning (AML)](cognitive-search-aml-skill.md) è un nuovo tipo di competenza per l'integrazione di un endpoint di inferenza da Azure Machine Learning. L'esperienza del portale supporta l'individuazione e l'integrazione dell'endpoint di Azure Machine Learning in un set di competenze di Ricerca cognitiva. Per l'individuazione è necessario distribuire i servizi di Ricerca cognitiva e Azure ML nella stessa sottoscrizione. Questa competenza è disponibile a livello generale. Introduzione a [questa esercitazione](cognitive-search-tutorial-aml-custom-skill.md).

### <a name="may-2020-microsoft-build"></a>Maggio 2020 (Microsoft Build)

+ La funzionalità [Sessioni di debug](cognitive-search-debug-session.md) è ora disponibile in anteprima. Le sessioni di debug forniscono un'interfaccia basata su portale per esaminare e risolvere i problemi relativi a un set di competenze. Le correzioni create nella sessione di debug possono essere salvate nei set di competenze di produzione. Introduzione a [questa esercitazione](cognitive-search-tutorial-debug-sessions.md).

+ Per schermare un endpoint del servizio di ricerca dalla rete Internet pubblica, è possibile [configurare le regole IP per il supporto del firewall in ingresso](service-configure-firewall.md) o sfruttare il [collegamento privato di Azure per un endpoint di ricerca privato](service-create-private-endpoint.md). Entrambe le funzionalità sono disponibili a livello generale.

+ Usare una [identità gestita dal sistema (anteprima)](search-howto-managed-identities-data-sources.md) per impostare una connessione a un'origine dati di Azure per l'indicizzazione. Si applica agli [indicizzatori](search-indexer-overview.md) che inseriscono contenuto da origini dati di Azure, ad esempio database SQL di Azure, Azure Cosmos DB e Archiviazione di Azure.

+ Modificare la base per il calcolo dei punteggi di ricerca, da ogni partizione a tutte le partizioni, usando i parametri di query [sessionId](index-similarity-and-scoring.md) e [scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics). Questi parametri sono disponibili a livello generale.

+ Aggiungere un parametro di query [featuresMode (anteprima)](index-similarity-and-scoring.md#featuresMode-param) per espandere un punteggio di pertinenza in modo da visualizzare maggiori dettagli: punteggio di somiglianza per campo, frequenza per termini di campo e numero di token univoci corrispondenti per campo. È possibile utilizzare questi punti dati negli algoritmi di assegnazione dei punteggi personalizzati. Per un esempio in cui viene illustrata questa funzionalità, vedere [Aggiungere Machine Learning (LearnToRank) per la pertinenza della ricerca](https://github.com/Azure-Samples/search-ranking-tutorial).

### <a name="march-2020"></a>Marzo 2020

+ L'[eliminazione temporanea di BLOB nativi (anteprima)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) significa che l'indicizzatore di archiviazione BLOB di Azure in Ricerca cognitiva di Azure riconoscerà i BLOB in uno stato di eliminazione temporanea e rimuoverà il documento di ricerca corrispondente durante l'indicizzazione.

+ È ora disponibile a livello generale una nuova [API REST di gestione (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) stabile. 

### <a name="february-2020"></a>Febbraio 2020

+ Il [rilevamento di informazioni personali (anteprima)](cognitive-search-skill-pii-detection.md) è una competenza cognitiva usata durante l'indicizzazione che estrae informazioni personali da un testo di input e offre diverse opzioni per mascherarle.

+ La [ricerca di entità personalizzate (anteprima)](cognitive-search-skill-custom-entity-lookup.md ) cerca il testo in un elenco personalizzato di parole e frasi definito dall'utente. Con questo elenco vengono etichettati tutti i documenti con entità corrispondenti. La competenza supporta anche un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze simili ma non proprio esatte. 

### <a name="january-2020"></a>Gennaio 2020

+ Le [chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) sono ora disponibili a livello generale. Se si usa REST, è possibile accedere alla funzionalità tramite `api-version=2019-05-06` o versioni successive. Per il codice gestito, il pacchetto corretto è ancora [.NET SDK versione 8.0-preview](search-dotnet-sdk-migration-version-9.md) anche se la funzionalità non è più in anteprima. 

+ L'accesso privato a un servizio di ricerca è disponibile tramite due meccanismi, attualmente in anteprima:

  + È possibile limitare l'accesso a specifici indirizzi IP usando l'API REST di gestione `api-version=2019-10-01-Preview` per creare il servizio. L'API di anteprima include le nuove proprietà **IpRule** e **NetworkRuleSet** nell'[API CreateOrUpdate ](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Questa funzionalità di anteprima è disponibile in specifiche aree. Per altre informazioni, vedere [Come usare l'API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

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