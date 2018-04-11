---
title: Aggiornamento alla generazione più recente di Azure SQL Data Warehouse | Microsoft Docs
description: Procedura per aggiornare Azure SQL Data Warehouse alla generazione più recente dell'architettura hardware e di archiviazione di Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Aggiornamento alla generazione più recente di Azure SQL Data Warehouse nel portale di Azure

Usare il portale di Azure per aggiornare Azure SQL Data Warehouse alla generazione più recente dell'architettura hardware e di archiviazione di Azure. Con l'aggiornamento sarà possibile ottenere prestazioni più veloci, maggiore scalabilità e sfruttare l'archiviazione illimitata per gli indici columnstore.  

## <a name="applies-to"></a>Si applica a
Questo aggiornamento si applica ai data warehouse nel livello di prestazioni Ottimizzato per l'elasticità.  Queste istruzioni consentono di aggiornare un data warehouse dal livello di prestazioni Ottimizzato per l'elasticità al livello di prestazioni Ottimizzato per il calcolo. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Prima di iniziare

1. Se il data warehouse con livello di prestazioni Ottimizzato per l'elasticità è sospeso, [riprenderne l'esecuzione](pause-and-resume-compute-portal.md).
2. Questa operazione potrebbe comportare alcuni minuti di inattività. 
3. Il processo di aggiornamento termina tutte le sessioni e interrompe tutte le connessioni. Prima dell'aggiornamento, assicurarsi che l'esecuzione delle query sia stata completata. Se si avvia un aggiornamento con transazioni in corso, il ripristino dello stato precedente potrebbe richiedere molto tempo. 

## <a name="start-the-upgrade"></a>Avviare l'aggiornamento

1. Aprire il data warehouse nel portale di Azure e fare clic su **Aggiorna al livello Ottimizzato per il calcolo**.
2. Osservare le opzioni relative al livello di prestazioni Ottimizzato per il calcolo. La selezione predefinita è paragonabile al livello corrente prima dell'aggiornamento.
3. Scegliere un livello di prestazioni. Il prezzo del livello di prestazioni Ottimizzato per il calcolo è attualmente dimezzato per il periodo di anteprima.
4. Fare clic su **Aggiorna**.
5. Controllare lo stato nel portale di Azure.
6. Attendere che lo stato del data warehouse sia Online.

## <a name="rebuild-columnstore-indexes"></a>Ricompilare gli indici columnstore

Quando il data warehouse è online, è possibile caricare dati ed eseguire query. Tuttavia, le prestazioni all'inizio possono essere rallentate poiché un processo in background sta eseguendo la migrazione dei dati nel nuovo hardware. 

Per rendere la migrazione dei dati più veloce possibile, è consigliabile ricompilare gli indici columnstore. A tale scopo, seguire le indicazioni per la [ricompilazione degli indici columnstore per migliorare la qualità dei segmenti](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). 

## <a name="next-steps"></a>Passaggi successivi
Il data warehouse è online. Per sfruttare le nuove funzionalità relative alle prestazioni, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md).
 