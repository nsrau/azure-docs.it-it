---
title: Nuovi annunci di funzionalità
titleSuffix: Azure Cognitive Search
description: Annunci di funzionalità nuove e migliorate, tra cui la ridenominazione di un servizio di ricerca di Azure in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 0ce2884a2382c7dff2bdb90bd92934609675f314
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834379"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novità di Azure ricerca cognitiva

Ecco cosa c'è di nuovo nel servizio. Aggiungere un segnalibro a questa pagina per rimanere aggiornati con il servizio.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nuovo nome del servizio per ricerca di Azure

Ricerca di Azure ora è stato rinominato in **azure ricerca cognitiva** per riflettere l'uso esteso delle competenze cognitive e l'elaborazione di intelligenza artificiale nelle operazioni principali. Mentre le competenze cognitive aggiungono nuove funzionalità, l'uso di intelligenza artificiale è strettamente facoltativo. È possibile continuare a usare Azure ricerca cognitiva senza intelligenza artificiale per compilare soluzioni complete di ricerca full-text su contenuto privato, eterogeneo, basato su testo in un indice creato e gestito nel cloud. 

Le versioni API, i pacchetti NuGet, gli spazi dei nomi e gli endpoint sono invariati. Le soluzioni di ricerca esistenti non sono interessate dalla modifica del nome del servizio.

## <a name="feature-announcements"></a>Annunci di funzionalità

### <a name="january-2020"></a>2020 gennaio

+ Le [chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) sono ora disponibili a livello generale. Se si usa REST, è possibile accedere alla funzionalità usando `api-version=2019-05-06`. Per il codice gestito, il pacchetto corretto è ancora [.NET SDK versione 8,0-Preview](search-dotnet-sdk-migration-version-9.md) anche se la funzionalità non è in anteprima. 

+ *L'accesso IP limitato e l'endpoint privato (anteprima)* in un endpoint del servizio di ricerca sono ora disponibili in **API-Version = 2019-10-01-Preview**. È possibile configurare un endpoint sicuro usando le nuove proprietà **IpRule** e **NETWORKRULESET** nell'API REST di gestione di [creazione o aggiornamento](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) . Per altre informazioni sulle versioni dell'API e sulla disponibilità a livello di area, vedere [come usare l'API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="december-2019"></a>Dicembre 2019

+ [Crea app (anteprima)](search-create-app-portal.md) è una nuova procedura guidata nel portale che genera un file HTML scaricabile. Il file include uno script incorporato che esegue il rendering di un'app Web di tipo "localhost" operativa, associata a un indice nel servizio di ricerca. Le pagine sono configurabili nella procedura guidata e possono contenere una barra di ricerca, l'area dei risultati, l'esplorazione dell'intestazione laterale e il supporto per le query typeahead. È possibile modificare il codice HTML offline per estendere o personalizzare il flusso di lavoro o l'aspetto.

### <a name="november-2019---ignite-conference"></a>Novembre 2019-conferenza Ignite

+ L' [arricchimento incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) aggiunge la memorizzazione nella cache e statefullness a una pipeline di arricchimento, in modo da poter lavorare su fasi o fasi specifiche senza perdere contenuto già elaborato. In precedenza, tutte le modifiche apportate a una pipeline di arricchimento richiedevano una ricompilazione completa. Con l'arricchimento incrementale, viene mantenuto l'output dell'analisi costosa, in particolare l'analisi delle immagini.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ L' [estrazione dei documenti (anteprima)](cognitive-search-skill-document-extraction.md) è una competenza cognitiva usata durante l'indicizzazione che consente di estrarre il contenuto di un file dall'interno di un skillt. In precedenza, il cracking del documento si verificava solo prima dell'esecuzione di Skills. Con l'aggiunta di questa competenza, è anche possibile eseguire questa operazione all'interno dell'esecuzione di competenze.

+ [Traduzione testo (anteprima)](cognitive-search-skill-text-translation.md) è una competenza cognitiva utilizzata durante l'indicizzazione che valuta il testo e, per ogni record, restituisce il testo convertito nel linguaggio di destinazione specificato.

+ [Power bi modelli](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) possono avviare rapidamente le visualizzazioni e l'analisi di contenuto arricchito in un archivio informazioni in Power bi desktop. Questo modello è progettato per le proiezioni di tabelle di Azure create tramite la [procedura guidata Importa dati](knowledge-store-create-portal.md).

+ [Azure Data Lake storage Gen2 (anteprima)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB API Gremlin (anteprima)](search-howto-index-cosmosdb.md)e [Cosmos DB API Cassandra (anteprima)](search-howto-index-cosmosdb.md) sono ora supportate negli indicizzatori. È possibile iscriversi con [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Si riceverà un messaggio di posta elettronica di conferma una volta accettato il programma di anteprima.

### <a name="july-2019"></a>Luglio 2019

+ Disponibile a livello generale nel [cloud di Azure per enti pubblici](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Aggiornamenti del servizio

Gli annunci relativi agli [aggiornamenti dei servizi](https://azure.microsoft.com/updates/?product=search&status=all) per Azure ricerca cognitiva sono disponibili nel sito Web di Azure.