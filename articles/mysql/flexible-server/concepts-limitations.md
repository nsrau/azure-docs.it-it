---
title: Limitazioni-database di Azure per MySQL-server flessibile
description: Questo articolo descrive le limitazioni nel database di Azure per MySQL-server flessibili, ad esempio il numero di opzioni del motore di connessione e di archiviazione.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: a3abde9092519be057dcd73ec63318f970fd5c74
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543086"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Limitazioni nel database di Azure per MySQL-server flessibile (anteprima)

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo descrive le limitazioni del servizio server flessibile del database di Azure per MySQL. Sono applicabili anche le [limitazioni generali](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) nel motore di database MySQL. Per informazioni sui livelli di risorse (calcolo, memoria, archiviazione), vedere l'articolo [calcolo e archiviazione](concepts-compute-storage.md) .

## <a name="server-parameters"></a>Parametri del server

> [!NOTE]
> Se si cercano valori min/max per i parametri del server `max_connections` `innodb_buffer_pool_size` , ad esempio e, queste informazioni sono state spostate nell'articolo parametri server dei concetti del [server](./concepts-server-parameters.md) .

Database di Azure per MySQL supporta l'ottimizzazione dei valori dei parametri del server. Valore minimo e massimo di alcuni parametri (ad esempio `max_connections`, `join_buffer_size` , `query_cache_size` ) è determinato dal livello di calcolo e dalle dimensioni di calcolo del server. Per ulteriori informazioni su questi limiti, fare riferimento ai [parametri del server](./concepts-server-parameters.md) .

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

### <a name="zone-redundant-ha"></a>Disponibilità elevata con ridondanza della zona
- Questa configurazione può essere impostata solo durante la creazione del server.
- Non supportato nel livello di calcolo in sequenza.

### <a name="networking"></a>Rete
- Non è possibile modificare il metodo di connettività dopo la creazione del server. Se il server viene creato con *accesso privato (integrazione VNet)* , non può essere modificato in *accesso pubblico (indirizzi IP consentiti)* dopo la creazione e viceversa
- TLS/SSL è abilitato per impostazione predefinita e non può essere disabilitato.
- La versione minima di TLS supportata nel server è TLS 1.2. Per altre informazioni, vedere [connettersi tramite TLS/SSL](./how-to-connect-tls-ssl.md) .

### <a name="stopstart-operation"></a>Operazione di arresto/avvio
- Non supportato con configurazioni a disponibilità elevata con ridondanza della zona (sia primarie che standby).
- Non supportato con le configurazioni della replica di lettura (origine e repliche).

### <a name="scale-operations"></a>Operazioni di scalabilità
- La riduzione del provisioning dell'archiviazione server non è supportata.

### <a name="read-replicas"></a>Repliche in lettura
- Non supportato con configurazioni a disponibilità elevata con ridondanza della zona (sia primarie che standby).

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni principali del motore di database non è supportata. Se si desidera aggiornare la versione principale, eseguire un [dump e ripristinarlo](../concepts-migrate-dump-restore.md) in un server creato con la nuova versione del motore.

### <a name="restoring-a-server"></a>Rispristino di un server
- Con il ripristino temporizzato, i nuovi server vengono creati con le stesse configurazioni di calcolo e di archiviazione del server di origine su cui si basano. Il calcolo del server appena ripristinato può essere ridotto dopo la creazione del server.
- Il ripristino di un server eliminato non è supportato.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [Opzioni di calcolo e archiviazione disponibili](concepts-compute-storage.md)
- Informazioni sulle [versioni supportate di MySQL](concepts-supported-versions.md)
- Vedere [come eseguire il backup e il ripristino di un server usando il portale di Azure](how-to-restore-server-portal.md)