---
title: Limiti - Database di Azure per MySQL
description: Questo articolo descrive i limiti di Database di Azure per MySQL, ad esempio il numero di connessioni e le opzioni del motore di archiviazione.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 38c006bd1cda1494b284f742459aaf539ed4a2d1
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539708"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limiti di Database di Azure per MySQL
Le sezioni seguenti illustrano la capacità, il supporto del motore di archiviazione, dei privilegi e delle istruzioni di gestione dei dati e i limiti funzionali del servizio di database. Vedere anche le [limitazioni generali](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) applicabili al motore di database MySQL.

## <a name="server-parameters"></a>Parametri del server

> [!NOTE]
> Se si cercano valori min/max per parametri del server come `max_connections` e `innodb_buffer_pool_size` , queste informazioni sono state spostate nell'articolo **[parametri del server](./concepts-server-parameters.md)** .

Database di Azure per MySQL supporta l'ottimizzazione dei valori dei parametri del server. Valore minimo e massimo di alcuni parametri (ad esempio `max_connections`, `join_buffer_size` , `query_cache_size` ) è determinato dal piano tariffario e dal Vcore del server. Per ulteriori informazioni su questi limiti, fare riferimento ai [parametri del server](./concepts-server-parameters.md) .

Al momento della distribuzione iniziale, un server Azure per MySQL include le tabelle di sistema per le informazioni sul fuso orario, ma queste tabelle non vengono popolate. Per popolare le tabelle di fuso orario, è possibile chiamare la stored procedure `mysql.az_load_timezone` da uno strumento come la riga di comando di MySQL o MySQL Workbench. Fare riferimento agli articoli sul [portale di Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) per le modalità in cui è possibile chiamare la stored procedure e impostare i fusi orari a livello globale o di sessione.

I plug-in per le password, ad esempio "validate_password" e "caching_sha2_password", non sono supportati dal servizio.

## <a name="storage-engines"></a>Motori di archiviazione

MySQL supporta molti motori di archiviazione. Nel database di Azure per MySQL server flessibile, i motori di archiviazione seguenti sono supportati e non supportati:

### <a name="supported"></a>Supportato
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Non supportato
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Privilegi & supporto per la manipolazione dei dati

Molti parametri e impostazioni del server possono inavvertitamente degradare le prestazioni del server o negare le proprietà ACID del server MySQL. Per mantenere l'integrità del servizio e il contratto di servizio a livello di prodotto, questo servizio non espone più ruoli. 

Il servizio MySQL non consente l'accesso diretto all'file system sottostante. Alcuni comandi di manipolazione dei dati non sono supportati. 

### <a name="unsupported"></a>Non supportato

Gli elementi seguenti non sono supportati:
- Ruolo DBA: con restrizioni. In alternativa, è possibile utilizzare l'utente amministratore, creato durante la creazione di un nuovo server, consente di eseguire la maggior parte delle istruzioni DDL e DML. 
- Privilegi SUPER: in modo analogo, il [privilegio Super](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) è limitato.
- DEFINER: Richiede privilegi avanzati per la creazione e presenta restrizioni. Se vengono importati dati tramite backup, rimuovere i comandi `CREATE DEFINER` manualmente o tramite il comando `--skip-definer` quando si esegue mysqldump.
- Database di sistema: il [database di sistema MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) è di sola lettura e viene usato per supportare varie funzionalità PaaS. Non è possibile apportare modifiche al `mysql` database di sistema.
- `SELECT ... INTO OUTFILE`: Non supportato nel servizio.

### <a name="supported"></a>Supportato
- L'istruzione `LOAD DATA INFILE` è supportata ma è necessario specificare il parametro `[LOCAL]` che deve essere indirizzato a un percorso UNC (archiviazione di Azure montata tramite SMB).

## <a name="functional-limitations"></a>Limitazioni funzionali

### <a name="scale-operations"></a>Operazioni di scalabilità
- Non è attualmente supportata la scalabilità dinamica tra i piani tariffari.
- La riduzione delle dimensioni di archiviazione del server non è supportato.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata. Se si vuole eseguire l'aggiornamento alla versione principale successiva, eseguire un [dump e ripristinarlo](./concepts-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

### <a name="point-in-time-restore"></a>Ripristino temporizzato
- Quando si usa la funzionalità di ripristino temporizzato, il nuovo server viene creato con le stesse configurazioni del server su cui si basa.
- Il ripristino di un server eliminato non è supportato.

### <a name="vnet-service-endpoints"></a>Endpoint del servizio di rete virtuale
- Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.

### <a name="storage-size"></a>Dimensioni dello spazio di archiviazione
- Per i limiti delle dimensioni di archiviazione per ogni piano tariffario, fare riferimento ai [piani tariffari](concepts-pricing-tiers.md).

## <a name="current-known-issues"></a>Problemi attualmente noti
- Quando viene stabilita la connessione, l'istanza del server MySQL visualizza una versione di server errata. Per ottenere la versione corretta del motore dell'istanza del server, usare il comando `select version();`.

## <a name="next-steps"></a>Passaggi successivi
- [Opzioni disponibili in ogni livello di servizio](concepts-pricing-tiers.md)
- [Versioni supportate del database MySQL](concepts-supported-versions.md)
