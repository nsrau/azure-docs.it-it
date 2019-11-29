---
title: Nuovi annunci di funzionalità
titleSuffix: Azure Cognitive Search
description: Annunci di funzionalità nuove e migliorate, tra cui la ridenominazione di un servizio di ricerca di Azure in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 247378461a99d42f21a67a2c0aebc7019727f3a0
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555150"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novità di Azure ricerca cognitiva

Ecco cosa c'è di nuovo nel servizio. Aggiungere un segnalibro a questa pagina per rimanere aggiornati con il servizio.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nuovo nome del servizio per ricerca di Azure

Ricerca di Azure ora è stato rinominato in **azure ricerca cognitiva** per riflettere l'uso esteso delle competenze cognitive e l'elaborazione di intelligenza artificiale nelle operazioni principali. Mentre le competenze cognitive aggiungono nuove funzionalità, l'uso di intelligenza artificiale è strettamente facoltativo. È possibile continuare a usare Azure ricerca cognitiva senza intelligenza artificiale per compilare soluzioni complete di ricerca full-text su contenuto privato, eterogeneo, basato su testo in un indice creato e gestito nel cloud. 

Le versioni API, i pacchetti NuGet, gli spazi dei nomi e gli endpoint sono invariati. Le soluzioni di ricerca esistenti non sono interessate dalla modifica del nome del servizio.

## <a name="feature-announcements"></a>Annunci di funzionalità

4 novembre, 2019-conferenza Ignite

+ L' [indicizzazione incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) consente di scegliere i passaggi da rielaborare quando si apportano modifiche a una pipeline di arricchimento. L'indicizzazione incrementale è utile se il contenuto dell'immagine è stato analizzato in precedenza. L'output dell'analisi costosa viene archiviato e quindi usato come base per l'indicizzazione o l'arricchimento aggiuntivo.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ L' [estrazione dei documenti (anteprima)](cognitive-search-skill-document-extraction.md) è una competenza cognitiva usata durante l'indicizzazione che consente di estrarre il contenuto di un file dall'interno di un skillt. In precedenza, il cracking del documento si verificava solo prima dell'esecuzione di Skills. Con l'aggiunta di questa competenza, è anche possibile eseguire questa operazione all'interno dell'esecuzione di competenze.

+ [Traduzione testo (anteprima)](cognitive-search-skill-text-translation.md) è una competenza cognitiva utilizzata durante l'indicizzazione che valuta il testo e, per ogni record, restituisce il testo convertito nel linguaggio di destinazione specificato.

+ [Power bi modelli](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) possono avviare rapidamente le visualizzazioni e l'analisi di contenuto arricchito in un archivio informazioni in Power bi desktop. Questo modello è progettato per le proiezioni di tabelle di Azure create tramite la [procedura guidata Importa dati](knowledge-store-create-portal.md).

+ [Azure Data Lake storage Gen2 (anteprima)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB API Gremlin (anteprima)](search-howto-index-cosmosdb.md)e [Cosmos DB API Cassandra (anteprima)](search-howto-index-cosmosdb.md) sono ora supportate negli indicizzatori. È possibile iscriversi con [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Si riceverà un messaggio di posta elettronica di conferma una volta accettato il programma di anteprima.

24 2019 luglio

+ Disponibile a livello generale nel [cloud di Azure per enti pubblici](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Aggiornamenti del servizio

Gli annunci relativi agli [aggiornamenti dei servizi](https://azure.microsoft.com/updates/?product=search&status=all) per Azure ricerca cognitiva sono disponibili nel sito Web di Azure.