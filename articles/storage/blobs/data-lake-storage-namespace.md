---
title: Spazio dei nomi gerarchico per Azure Data Lake Storage Gen2
description: Descrive la nozione di spazio dei nomi gerarchico per Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 0b98892bd31b097e3dc217d54f52f12550599d32
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847139"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Spazio dei nomi gerarchico per Azure Data Lake Storage Gen2

Meccanismo chiave che consente ad Azure Data Lake Storage Gen2 di fornire prestazioni del file system scalabili in base all'archiviazione di oggetti e ai prezzi tramite l'aggiunta di uno **spazio dei nomi gerarchico**. Consente la raccolta di oggetti o file all'interno di un account per organizzarli in una gerarchia di directory e sottodirectory annidate allo stesso modo in cui sono organizzate nel file system o sul computer. Con lo spazio dei nomi gerarchico abilitato, l'account di archiviazione è in grado di offrire la scalabilità e l'efficacia di archiviazione di oggetti, con la semantica del file system già nota ai motori e ai framework di analisi.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Vantaggi dello spazio dei nomi gerarchico

I file system che implementano uno spazio dei nomi gerarchico per i dati BLOB godono dei seguenti vantaggi:

- **Modifica della directory atomica:** gli archivi di oggetti simulano una gerarchia di directory adottando una convenzione che incorpora slash (/) nel nome oggetto per indicare i segmenti di tracciato. Quando questa convenzione viene usata per organizzare gli oggetti, non prevede alcuna assistenza per le azioni quali lo spostamento, la ridenominazione o l'eliminazione di directory. Senza directory reali, le applicazioni devono elaborare potenzialmente milioni di singoli BLOB per offrire attività a livello di directory. Al contrario, lo spazio dei nomi gerarchico elabora queste attività mediante l'aggiornamento di una singola voce (la directory principale).

    Questa ottimizzazione significativa è particolarmente importante per molti framework di analisi dei Big Data. Strumenti quali Hive, Spark e altri ancora scrivono spesso l'output su percorsi temporanei, per poi rinominare il percorso al termine del processo. Senza lo spazio dei nomi gerarchico, la ridenominazione spesso può richiedere più tempo del processo di analisi stesso. Una latenza di processo più bassa implica una riduzione del costo totale di proprietà (TCO) per i carichi di lavoro analitici.

- **Stile interfaccia familiare:** i file system sono riconosciuti appieno da sviluppatori e utenti. Non è necessario apprendere un nuovo paradigma di archiviazione quando si esegue la migrazione nel cloud, dato che l'interfaccia del file system esposta da Data Lake Storage Gen2 è lo stesso paradigma usato dai computer di piccole e grandi dimensioni.

Uno dei motivi per cui gli archivi di oggetti non hanno tradizionalmente mai supportato gli spazi dei nomi gerarchici è perché questi ultimi limitano la scalabilità. Tuttavia, lo spazio dei nomi gerarchico di Data Lake Storage Gen2 scala in senso lineare, senza ridurre né la capacità dei dati, né le prestazioni.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Quando abilitare lo spazio dei nomi gerarchico

È consigliabile attivare lo spazio dei nomi gerarchico per carichi di lavoro di archiviazione progettati per file system che consentono di modificare le directory. Sono inclusi tutti i carichi di lavoro destinati principalmente all'elaborazione analitica. Anche i set di dati che richiedono un elevato livello di organizzazione potranno usufruire dell'abilitazione dello spazio dei nomi gerarchico.

I motivi per abilitare lo spazio dei nomi gerarchico sono determinati da un'analisi del costo totale di proprietà. In generale, i miglioramenti in termini di latenza di carico di lavoro dovuti a un'accelerazione dell'archiviazione richiederanno risorse di calcolo per un tempo inferiore. È possibile migliorare la latenza per molti carichi di lavoro tramite la modifica della directory atomica, abilitata dallo spazio dei nomi gerarchico. In molti carichi di lavoro, la risorsa di calcolo rappresenta > 85% del costo totale e pertanto anche una riduzione modesta della latenza del carico di lavoro implica un risparmio significativo in termini di costo totale di proprietà. Anche nei casi in cui l'abilitazione dello spazio dei nomi gerarchico aumenta i costi di archiviazione, il costo totale di proprietà è comunque ridotto per via dei minori costi di calcolo.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Quando disabilitare lo spazio dei nomi gerarchico

È possibile che alcuni carichi di lavoro di archiviazione di oggetti non ottengano alcun vantaggio abilitando lo spazio dei nomi gerarchico, come nel caso di backup, archiviazione di immagini e altre applicazioni in cui l'organizzazione di oggetti è archiviata separatamente dagli oggetti stessi (ad esempio, in un database separato).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazione](./data-lake-storage-quickstart-create-account.md)
