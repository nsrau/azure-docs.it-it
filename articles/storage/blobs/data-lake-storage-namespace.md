---
title: Spazio dei nomi gerarchico per Azure Data Lake Storage Gen2
description: Descrive il concetto di spazio dei nomi gerarchico per Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153078"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Spazio dei nomi gerarchico per Azure Data Lake Storage Gen2

Meccanismo chiave che consente ad Azure Data Lake Storage Gen2 di fornire prestazioni del file system scalabili in base all'archiviazione di oggetti e ai prezzi tramite l'aggiunta di uno **spazio dei nomi gerarchico**. Consente la raccolta di oggetti o file all'interno di un account per organizzarli in una gerarchia di directory e sottodirectory annidate allo stesso modo in cui sono organizzate nel file system o sul computer. Con uno spazio dei nomi gerarchico abilitato, un account di archiviazione diventa in grado di fornire la scalabilità e l'efficacia in termini di costi dell'archiviazione degli oggetti, con la semantica del file system familiare ai motori di analisi e ai framework.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Vantaggi di uno spazio dei nomi gerarchico

I file system che implementano uno spazio dei nomi gerarchico per i dati BLOB godono dei seguenti vantaggi:

- **Modifica della directory atomica:** gli archivi di oggetti simulano una gerarchia di directory adottando una convenzione che prevede l'inserimento di slash (/) nel nome oggetto per indicare i segmenti di tracciato. Quando questa convenzione viene usata per organizzare gli oggetti, non prevede alcuna assistenza per le azioni quali lo spostamento, la ridenominazione o l'eliminazione di directory. Senza directory reali, le applicazioni devono elaborare potenzialmente milioni di singoli BLOB per offrire attività a livello di directory. Al contrario, uno spazio dei nomi gerarchico elabora queste attività aggiornando una singola voce (la directory padre).

    Questa ottimizzazione significativa è particolarmente importante per molti framework di analisi dei Big Data. Strumenti quali Hive, Spark e altri ancora scrivono spesso l'output su percorsi temporanei, per poi rinominare il percorso al termine del processo. Senza uno spazio dei nomi gerarchico, questa ridenominazione può spesso richiedere più tempo rispetto al processo di analisi stesso. Una latenza di processo più bassa implica una riduzione del costo totale di proprietà (TCO) per i carichi di lavoro analitici.

- **Stile interfaccia familiare:** i file system sono riconosciuti appieno da sviluppatori e utenti. Non è necessario apprendere un nuovo paradigma di archiviazione quando si esegue la migrazione nel cloud, dato che l'interfaccia del file system esposta da Data Lake Storage Gen2 è lo stesso paradigma usato dai computer di piccole e grandi dimensioni.

Uno dei motivi per cui gli archivi di oggetti non hanno tradizionalmente mai supportato gli spazi dei nomi gerarchici è perché questi ultimi limitano la scalabilità. Tuttavia, lo spazio dei nomi gerarchico di Data Lake Storage Gen2 scala in senso lineare, senza ridurre né la capacità dei dati, né le prestazioni.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Decidere se abilitare uno spazio dei nomi gerarchicoDecideing whether to enable a hierarchical namespace

Dopo aver abilitato uno spazio dei nomi gerarchico nell'account, non è possibile ripristinarlo a uno spazio dei nomi semplice. Pertanto, considerare se ha senso abilitare uno spazio dei nomi gerarchico in base alla natura dei carichi di lavoro dell'archivio oggetti.

Alcuni carichi di lavoro potrebbero non trarre alcun vantaggio abilitando uno spazio dei nomi gerarchico. come nel caso di backup, archiviazione di immagini e altre applicazioni in cui l'organizzazione di oggetti è archiviata separatamente dagli oggetti stessi (ad esempio, in un database separato). 

Inoltre, mentre il supporto per le funzionalità di archiviazione BLOB e l'ecosistema dei servizi di Azure continua a crescere, esistono ancora alcune funzionalità e servizi di Azure che non sono ancora supportati negli account con uno spazio dei nomi gerarchico. Vedere [Problemi noti](data-lake-storage-known-issues.md). 

In generale, è consigliabile attivare uno spazio dei nomi gerarchico per i carichi di lavoro di archiviazione progettati per i file system che modificano le directory. Sono inclusi tutti i carichi di lavoro destinati principalmente all'elaborazione analitica. Anche i set di dati che richiedono un elevato livello di organizzazione trarranno vantaggio dall'abilitazione di uno spazio dei nomi gerarchico.

I motivi per abilitare uno spazio dei nomi gerarchico sono determinati da un'analisi del valore di applicazione delle proprietà del contenuto. In generale, i miglioramenti in termini di latenza di carico di lavoro dovuti a un'accelerazione dell'archiviazione richiederanno risorse di calcolo per un tempo inferiore. La latenza per molti carichi di lavoro può essere migliorata a causa della manipolazione atomica della directory abilitata da uno spazio dei nomi gerarchico. In molti carichi di lavoro, la risorsa di calcolo rappresenta > 85% del costo totale e pertanto anche una riduzione modesta della latenza del carico di lavoro implica un risparmio significativo in termini di costo totale di proprietà. Anche nei casi in cui l'abilitazione di uno spazio dei nomi gerarchico aumenta i costi di archiviazione, il costo duo è comunque ridotto a causa della riduzione dei costi di calcolo.

Per analizzare le differenze nei prezzi di archiviazione dei dati, nei prezzi delle transazioni e nei prezzi di prenotazione della capacità di archiviazione tra account con uno spazio dei nomi gerarchico semplice e uno spazio dei nomi gerarchico, vedere [Prezzi di Azure Data Lake Storage Gen2.](https://azure.microsoft.com/pricing/details/storage/data-lake/)

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazioneCreate a Storage account](./data-lake-storage-quickstart-create-account.md)
