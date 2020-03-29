---
title: Eseguire la migrazione da DTU a vCore
description: Eseguire la migrazione dal modello DTU al modello vCore. La migrazione a vCore è simile all'aggiornamento o al downgrade tra i livelli standard e premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945411"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Eseguire la migrazione dal modello basato su DTU al modello basato su vCoreMigrate from the DTU-based model to the vCore-based model

## <a name="migrate-a-database"></a>Eseguire la migrazione di un database

La migrazione di un database dal modello di acquisto basato su DTU al modello di acquisto basato su vCore è simile all'aggiornamento o al downgrade tra i livelli di servizio standard e premium nel modello di acquisto basato su DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Eseguire la migrazione di database che utilizzano la replica geograficaMigrate databases that use geo-replication

La migrazione dal modello basato su DTU al modello di acquisto basato su vCore è simile all'aggiornamento o al downgrade delle relazioni di replica geografica tra i database nei livelli di servizio standard e premium. Durante la migrazione, non è necessario arrestare la replica geografica, ma è necessario seguire queste regole di sequenza:During migration, you don't have to stop geo-replication, but you must follow these sequencing rules:

- Quando si esegue l'aggiornamento, è necessario aggiornare prima il database secondario e dopo il database primario.
- Quando si esegue il downgrade, è necessario seguire l'ordine inverso, ovvero eseguire prima il downgrade del database primario e dopo quello del database secondario.

Quando si usa la replica geografica tra due pool elastici, è consigliabile designare un pool come primario e l'altro come secondario. In tal caso, quando si esegue la migrazione di pool elastici è necessario utilizzare le stesse indicazioni di sequenza. Tuttavia, se si dispone di pool elastici che contengono database primari e secondari, considerare il pool con un utilizzo più elevato come primario e seguire di conseguenza le regole di sequenza.  

Nella tabella seguente vengono fornite indicazioni per scenari di migrazione specifici:The following table provides guidance for specific migration scenarios:

|Livello di servizio corrente|Livello di servizio di destinazione|Tipo di migrazione|Azioni utente|
|---|---|---|---|
|Standard|Scopo generico|Laterale|L'ordine di migrazione non è rilevante, ma è necessario verificare che il numero di vCore sia appropriato*|
|Premium|Business Critical|Laterale|L'ordine di migrazione non è rilevante, ma è necessario verificare che il numero di vCore sia appropriato*|
|Standard|Business Critical|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
|Business Critical|Standard|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Premium|Scopo generico|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Scopo generico|Premium|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
|Business Critical|Scopo generico|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Scopo generico|Business Critical|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
||||

\*Come regola generale, ogni 100 DCU nel livello standard richiedono almeno 1 vCore e ogni 125 DCU nel livello premium richiedono almeno 1 vCore. Per altre informazioni, vedere il [modello di acquisto basato su vCore](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Eseguire la migrazione dei gruppi di failoverMigrate failover groups

Per i gruppi di failover con più database è necessario eseguire separatamente la migrazione dei database primari e secondari. Durante questo processo sono valide le stesse considerazioni e regole di sequenziazione. Dopo la conversione dei database nel modello di acquisto basato su vCore, il gruppo di failover rimarrà attivo con le stesse impostazioni dei criteri.

### <a name="create-a-geo-replication-secondary-database"></a>Creare un database secondario di replica geograficaCreate a geo-replication secondary database

È possibile creare un database secondario di replica geografica (un database geografico) solo utilizzando lo stesso livello di servizio usato per il database primario. Per i database con una frequenza di generazione log elevata, è consigliabile creare il database secondario geografico con le stesse dimensioni di calcolo del database primario.

Se si sta creando un database secondario geografico nel pool `maxVCore` elastico per un singolo database primario, assicurarsi che l'impostazione per il pool corrisponda alle dimensioni di calcolo del database primario. Se si sta creando un database geografico secondario per un'area geografica `maxVCore` in un altro pool elastico, è consigliabile che i pool abbiano le stesse impostazioni.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usare la copia del database per convertire un database basato su DTU in un database basato su vCoreUse database-copy to convert a DTU-based database to a vCore-based database

È possibile copiare un database con dimensioni di calcolo basate su DTU in un database con dimensioni di calcolo basate su vCore senza restrizioni o particolari regole di sequenziazione a condizione che le dimensioni di calcolo del database di destinazione supportino le dimensioni massime del database di origine. La copia del database crea uno snapshot dei dati a partire dall'ora di inizio dell'operazione di copia e non sincronizza i dati tra l'origine e la destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Per le dimensioni di calcolo specifiche e le opzioni di dimensione di archiviazione disponibili per i singoli database, vedere Limiti delle [risorse basate su vCore del database SQL per singoli database.](sql-database-vcore-resource-limits-single-databases.md)
- Per le dimensioni di calcolo specifiche e le opzioni di dimensioni di archiviazione disponibili per i pool elastici, vedere Limiti delle risorse basate su vCore del database SQL per i [pool elastici.](sql-database-vcore-resource-limits-elastic-pools.md)
