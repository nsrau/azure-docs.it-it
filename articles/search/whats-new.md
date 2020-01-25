---
title: Nuovi annunci di funzionalità
titleSuffix: Azure Cognitive Search
description: Annunci di funzionalità nuove e migliorate, tra cui la ridenominazione di un servizio di ricerca di Azure in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: a9c64f4bafa770de9bbbd97ae9a0295b72d81f54
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719824"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novità di Azure ricerca cognitiva

Ecco cosa c'è di nuovo nel servizio. Aggiungere un segnalibro a questa pagina per rimanere aggiornati con il servizio.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nome nuovo servizio

Ricerca di Azure è stato rinominato in **azure ricerca cognitiva** per riflettere l'uso espanso (ancora facoltativo) delle competenze cognitive e dell'elaborazione di intelligenza artificiale nelle operazioni principali. Le versioni API, i pacchetti NuGet, gli spazi dei nomi e gli endpoint sono invariati. Le soluzioni di ricerca nuove e esistenti non sono interessate dalla modifica del nome del servizio.

## <a name="feature-announcements"></a>Annunci di funzionalità

### <a name="january-2020"></a>Gennaio 2020

+ Le [chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) sono ora disponibili a livello generale. Se si usa REST, è possibile accedere alla funzionalità usando `api-version=2019-05-06`. Per il codice gestito, il pacchetto corretto è ancora [.NET SDK versione 8,0-Preview](search-dotnet-sdk-migration-version-9.md) anche se la funzionalità non è in anteprima. 

+ L'accesso privato a un servizio di ricerca è disponibile tramite due meccanismi:

  + È possibile limitare l'accesso a indirizzi IP specifici usando l'API REST di gestione `api-version=2019-10-01-Preview` per creare il servizio. L'API di anteprima include nuove proprietà **IpRule** e **NetworkRuleSet** nell' [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate). Questa funzionalità di anteprima è disponibile nelle aree selezionate. Per altre informazioni, vedere [come usare l'API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Attualmente disponibile tramite un'anteprima con accesso limitato, è possibile effettuare il provisioning di un servizio di ricerca di Azure che supporta l'endpoint privato di Azure per le connessioni dai client nella stessa rete virtuale. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un endpoint privato per una connessione protetta](service-create-private-endpoint.md).

### <a name="december-2019"></a>Dicembre 2019

+ [Crea app (anteprima)](search-create-app-portal.md) è una nuova procedura guidata nel portale che genera un file HTML scaricabile. Il file include uno script incorporato che esegue il rendering di un'app Web di tipo "localhost" operativa, associata a un indice nel servizio di ricerca. Le pagine sono configurabili nella procedura guidata e possono contenere una barra di ricerca, l'area dei risultati, l'esplorazione dell'intestazione laterale e il supporto per le query typeahead. È possibile modificare il codice HTML offline per estendere o personalizzare il flusso di lavoro o l'aspetto.

+ [Creare un endpoint privato per le connessioni sicure (anteprima)](service-create-private-endpoint.md) spiega come configurare un collegamento privato per proteggere le connessioni al servizio di ricerca. Questa funzionalità di anteprima è disponibile su richiesta e usa il [collegamento privato di Azure](../private-link/private-link-overview.md) e la [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) come parte della soluzione.

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