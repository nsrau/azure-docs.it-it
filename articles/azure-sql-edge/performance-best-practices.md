---
title: Procedure consigliate per le prestazioni e linee guida sulla configurazione-Azure SQL Edge
description: Informazioni sulle procedure consigliate per le prestazioni e le linee guida sulla configurazione in Azure SQL Edge
keywords: SQL Edge, conservazione dei dati
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392011"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>Procedure consigliate per le prestazioni e linee guida per la configurazione

Azure SQL Edge offre diverse funzionalità e funzionalità che possono essere usate per migliorare le prestazioni della distribuzione di SQL Edge. Questo articolo illustra alcune procedure consigliate e consigli per ottimizzare le prestazioni. 

## <a name="best-practices"></a>Procedure consigliate 

### <a name="configure-multiple-tempdb-data-files"></a>Configurare più file di dati tempdb

Azure SQL Edge crea per impostazione predefinita un solo file di dati tempdb come parte dell'inizializzazione del contenitore. È consigliabile prendere in considerazione la creazione di più file di dati tempdb dopo la distribuzione. Per altre informazioni, vedere le linee guida nell'articolo [Suggerimenti per ridurre la contesa per l'allocazione nel database tempdb di SQL Server](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Usare gli indici columnstore cluster laddove possibile

I dispositivi Internet e di tutto tendono a generare un volume elevato di dati che in genere vengono aggregati in un intervallo di tempo per l'analisi. Le singole righe di dati vengono usate raramente per le analisi. Gli indici columnstore sono ideali per archiviare ed eseguire query su set di DataSet di grandi dimensioni. Questo indice usa l'archiviazione dei dati basata su colonne e l'elaborazione delle query per ottenere fino a 10 volte le prestazioni delle query rispetto all'archiviazione tradizionale orientata alle righe. È anche possibile migliorare fino a 10 volte la compressione dei dati rispetto alla dimensione dei dati non compressi. Per altre informazioni, vedere [indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview)

Inoltre, altre funzionalità di Azure SQL Edge, come il flusso di dati e la conservazione dei dati, traggono vantaggio dalle ottimizzazioni columnstore relative all'inserimento e alla rimozione dei dati. 

### <a name="simple-recovery-model"></a>Modello di recupero con registrazione minima

Poiché l'archiviazione può essere vincolata nei dispositivi perimetrali, tutti i database utente in Azure SQL Edge usano il modello di recupero con registrazione minima per impostazione predefinita. Il modello di recupero con registrazione minima recupera automaticamente lo spazio di log per limitare i requisiti di spazio, evitando in tal modo la necessità di gestire lo spazio del log delle transazioni. Sui dispositivi perimetrali con archiviazione limitata disponibile, questo può essere utile. Per ulteriori informazioni sul modello di recupero con registrazione minima e altri modelli di recupero disponibili, vedere [modelli di recupero](/sql/relational-databases/backup-restore/recovery-models-sql-server)

Operazioni quali il log shipping e i ripristini temporizzati, che richiedono i backup del log delle transazioni, non sono supportati dal modello di recupero con registrazione minima.  

## <a name="advanced-configuration"></a>Configurazione avanzata 

### <a name="setting-memory-limits"></a>Impostazione dei limiti di memoria

Azure SQL Edge supporta fino a 64 GB di memoria per il pool di buffer, mentre la memoria aggiuntiva potrebbe essere necessaria per i processi satellite in esecuzione all'interno del contenitore SQL Edge. Nei dispositivi perimetrali più piccoli con memoria limitata è consigliabile limitare la memoria disponibile per i contenitori di SQL Edge, in modo che l'host Docker e altri processi o moduli perimetrali possano funzionare correttamente. La memoria totale disponibile per SQL Edge può essere controllata usando i meccanismi seguenti. 

- Limitazione della memoria disponibile per i contenitori SQL Edge: la memoria totale disponibile per il contenitore SQL Edge può essere limitata usando l'opzione di configurazione runtime del contenitore `--memory` . Per altre informazioni sulla limitazione della memoria disponibile per il contenitore SQL Edge, vedere [Opzioni di runtime con memoria, CPU e GPU](https://docs.docker.com/config/containers/resource_constraints/).

- Limitazione della memoria disponibile per il processo SQL all'interno del contenitore: per impostazione predefinita, il processo SQL all'interno del contenitore usa solo il 80% della RAM fisica disponibile. Per la maggior parte delle distribuzioni, la configurazione predefinita sarà corretta. Tuttavia, possono essere presenti scenari in cui potrebbe essere necessaria memoria aggiuntiva per lo streaming dei dati e i processi di ONNX in esecuzione all'interno dei contenitori di SQL Edge. In questi scenari, la memoria disponibile per il processo SQL può essere controllata usando l' `memory.memorylimitmb` impostazione nel file MSSQL-conf. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione utilizzando il file MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

Quando si impostano i limiti di memoria, prestare attenzione a non impostare un valore troppo basso. Se non si imposta memoria sufficiente per il processo SQL, può influire gravemente sulle prestazioni di SQL.

### <a name="delayed-durability"></a>Durabilità posticipata

Le transazioni in Azure SQL Edge possono essere completamente durevoli, le SQL Server predefinite o con durabilità ritardata (noto anche come lazy commit).

Il commit delle transazioni completamente durevole è sincrono e segnala il completamento del commit restituendo il controllo al client solo dopo che i record del log per la transazione vengono scritti su disco. Il commit delle transazioni con durabilità ritardata è asincrono e segnala il completamento del commit prima che i record del log per la transazione vengano scritti su disco. La scrittura delle voci di log delle transazioni su disco è necessaria affinché una transazione sia durevole. Le transazioni con durabilità ritardata diventano durevoli quando le voci di log delle transazioni vengono scaricate su disco. 

Nelle distribuzioni in cui è possibile tollerare la **perdita di dati** o nei dispositivi perimetrali con archiviazione lenta, è possibile usare la durabilità posticipata per ottimizzare l'inserimento dei dati e la pulizia basata sulla conservazione dei dati. Per altre informazioni, vedere [Controllo della durabilità delle transazioni](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Configurazioni del sistema operativo Linux 

Provare a usare le impostazioni di [configurazione del sistema operativo Linux](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) seguenti per ottenere prestazioni ottimali per un'installazione di SQL.