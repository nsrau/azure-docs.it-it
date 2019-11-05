---
title: Novità del servizio
titleSuffix: Azure Cognitive Search
description: Annunci di funzionalità nuove e migliorate, tra cui la ridenominazione di un servizio di ricerca di Azure in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e81eaf232e3234ac4de0cfb7412e23709f0c0b99
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549104"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novità di Azure ricerca cognitiva

Informazioni sulle novità del servizio. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati con il servizio.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nuovo nome del servizio per ricerca di Azure

Ricerca di Azure ora è stato rinominato in **azure ricerca cognitiva** per riflettere l'uso esteso delle competenze cognitive e l'elaborazione di intelligenza artificiale nelle operazioni principali. Mentre le competenze cognitive aggiungono nuove funzionalità, l'uso di intelligenza artificiale è strettamente facoltativo. È possibile continuare a usare Azure ricerca cognitiva senza intelligenza artificiale per compilare soluzioni complete di ricerca full-text su contenuto privato, eterogeneo, basato su testo in un indice creato e gestito nel cloud. 

Le versioni API, i pacchetti NuGet, gli spazi dei nomi e gli endpoint sono invariati. Le soluzioni di ricerca esistenti non sono interessate dalla modifica del nome del servizio.

## <a name="feature-announcements"></a>Annunci di funzionalità

4 novembre, 2019-conferenza Ignite

+ L' [indicizzazione incrementale](cognitive-search-incremental-indexing-conceptual.md), ora disponibile in anteprima, consente di elaborare o rielaborare solo i passaggi assolutamente necessari per apportare modifiche a una pipeline di arricchimento. Questa operazione è particolarmente utile se si dispone di contenuto immagine analizzato in precedenza. L'output dell'analisi costosa viene archiviato e quindi usato come base per l'indicizzazione o l'arricchimento aggiuntivo.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ L' [estrazione dei documenti](cognitive-search-skill-document-extraction.md) è una competenza cognitiva usata durante l'indicizzazione che consente di estrarre il contenuto di un file dall'interno di un skillt. In precedenza, il cracking del documento si verificava solo prima dell'esecuzione di Skills. Con l'aggiunta di questa competenza, è anche possibile eseguire questa operazione all'interno dell'esecuzione di competenze.

+ La [traduzione del testo](cognitive-search-skill-text-translation.md) è una competenza cognitiva utilizzata durante l'indicizzazione che valuta il testo e, per ogni record, restituisce il testo convertito nel linguaggio di destinazione specificato.

+ [Power bi modelli](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) possono avviare rapidamente le visualizzazioni e l'analisi di contenuto arricchito in un archivio informazioni in Power bi desktop. Questo modello è progettato per le proiezioni di tabelle di Azure create tramite la [procedura guidata Importa dati](knowledge-store-create-portal.md).

## <a name="service-updates"></a>Aggiornamenti del servizio

Gli annunci relativi agli [aggiornamenti dei servizi](https://azure.microsoft.com/updates/?product=search&status=all) per Azure ricerca cognitiva sono disponibili nel sito Web di Azure.