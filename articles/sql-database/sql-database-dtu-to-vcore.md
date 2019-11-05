---
title: 'Database SQL di Azure: eseguire la migrazione da DTU a vCore | Microsoft Docs'
description: Eseguire la migrazione dal modello DTU al modello vCore. La migrazione a vCore è simile all'aggiornamento o al downgrade tra i livelli standard e Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: c93d14797bb22e71879e6f80d8bbc94a7ecce6ac
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520968"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Eseguire la migrazione dal modello basato su DTU al modello basato su vCore

## <a name="migrate-a-database"></a>Eseguire la migrazione di un database

La migrazione di un database dal modello di acquisto basato su DTU al modello di acquisto basato su vCore è simile all'aggiornamento o al downgrade tra i livelli di servizio standard e Premium nel modello di acquisto basato su DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Eseguire la migrazione di database che usano la replica geografica

La migrazione dal modello basato su DTU al modello di acquisto basato su vCore è simile all'aggiornamento o al downgrade delle relazioni di replica geografica tra i database nei livelli di servizio standard e Premium. Durante la migrazione non è necessario arrestare la replica geografica, ma è necessario seguire le regole di sequenziazione seguenti:

- Quando si esegue l'aggiornamento, è necessario aggiornare prima il database secondario e dopo il database primario.
- Quando si esegue il downgrade, è necessario seguire l'ordine inverso, ovvero eseguire prima il downgrade del database primario e dopo quello del database secondario.

Quando si usa la replica geografica tra due pool elastici, è consigliabile designare un pool come primario e l'altro come secondario. In tal caso, quando si esegue la migrazione dei pool elastici è necessario usare le stesse linee guida per la sequenziazione. Tuttavia, se si dispone di pool elastici che contengono sia database primari che secondari, trattare il pool con l'utilizzo più elevato come primario e seguire le regole di sequenziazione di conseguenza.  

La tabella seguente fornisce indicazioni per scenari di migrazione specifici:

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

\* ogni 100 DTU nel livello standard richiedono almeno 1 vCore e ogni 125 DTU nel livello Premium richiede almeno 1 vCore.

## <a name="migrate-failover-groups"></a>Eseguire la migrazione dei gruppi di failover

Per i gruppi di failover con più database è necessario eseguire separatamente la migrazione dei database primari e secondari. Durante questo processo sono valide le stesse considerazioni e regole di sequenziazione. Dopo la conversione dei database nel modello di acquisto basato su vCore, il gruppo di failover rimarrà in vigore con le stesse impostazioni dei criteri.

### <a name="create-a-geo-replication-secondary-database"></a>Creare un database secondario con replica geografica

È possibile creare un database secondario con replica geografica, ovvero una replica geografica secondaria, solo usando lo stesso livello di servizio usato per il database primario. Per i database con una frequenza di generazione dei log elevata, è consigliabile creare la replica geografica secondaria con le stesse dimensioni di calcolo della replica primaria.

Se si sta creando una replica geografica secondaria nel pool elastico per un singolo database primario, assicurarsi che l'impostazione `maxVCore` per il pool corrisponda alle dimensioni di calcolo del database primario. Se si crea un database di replica geografica secondario per un database primario in un altro pool elastico, è consigliabile che i pool abbiano le stesse impostazioni `maxVCore`.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usare la copia del database per convertire un database basato su DTU in un database basato su vCore

È possibile copiare un database con dimensioni di calcolo basate su DTU in un database con dimensioni di calcolo basate su vCore senza restrizioni o particolari regole di sequenziazione a condizione che le dimensioni di calcolo del database di destinazione supportino le dimensioni massime del database di origine. La copia del database crea uno snapshot dei dati a partire dall'ora di inizio dell'operazione di copia e non sincronizza i dati tra l'origine e la destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Per le opzioni di calcolo e dimensioni di archiviazione specifiche disponibili per i singoli database, vedere [limiti delle risorse basate su vCore del database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md).
- Per le opzioni di calcolo e dimensioni di archiviazione specifiche disponibili per i pool elastici, vedere [limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).
