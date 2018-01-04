---
title: Limiti del Database di Azure per MySQL | Microsoft Docs
description: Descrive i limiti dell'anteprima del Database di Azure per MySQL.
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 12/09/2017
ms.openlocfilehash: e16982e4e57ba9f2f11e9ee59f88f24b3fe3fe3f
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitazioni nel Database di Azure per MySQL
Il servizio Database di Azure per MySQL è in anteprima pubblica. Le sezioni seguenti descrivono la capacità, supporto del motore di archiviazione, il supporto di privilegi, supporto istruzione di manipolazione dei dati e limiti funzionali nel servizio del database. Vedere anche le [limitazioni generali](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) applicabili al motore di database MySQL.

## <a name="service-tier-maximums"></a>Valori massimi del livello di servizio
Database di Azure per MySQL offre più livelli di servizio tra cui è possibile scegliere durante la creazione di un server. Per altre informazioni vedere [Opzioni e prestazioni disponibili in ogni livello di servizio](concepts-service-tiers.md).  

Esiste una quantità massima di connessioni, unità di calcolo e spazio di archiviazione in ogni livello di servizio durante l'anteprima del servizio, come indicato di seguito: 

|                            |                   |
| :------------------------- | :---------------- |
| **Numero massimo di connessioni**        |                   |
| Base con 50 unità di calcolo     | 50 connessioni    |
| Base con 100 unità di calcolo    | 100 connessioni   |
| Standard con 100 unità di calcolo | 200 connessioni   |
| Standard con 200 unità di calcolo | 400 connessioni   |
| Standard con 400 unità di calcolo | 800 connessioni   |
| Standard con 800 unità di calcolo | 1600 connessioni  |
| **Unità di calcolo massime**      |                   |
| Livello di servizio Basic         | 100 unità di calcolo |
| Livello di servizio Standard      | 800 unità di calcolo |
| **Spazio di archiviazione massimo**            |                   |
| Livello di servizio Basic         | 1 TB              |
| Livello di servizio Standard      | 1 TB              |

Quando viene raggiunto un numero eccessivo di connessioni, è possibile che si riceva l'errore seguente:
> ERROR 1040 (08004): Too many connections (ERRORE 1040 (08004): numero eccessivo di connessioni)

## <a name="storage-engine-support"></a>Supporto del motore di archiviazione

### <a name="supported"></a>Supportato
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Non supportato
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVIO](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATI](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Supporto di privilegi

### <a name="unsupported"></a>Non supportato
- Ruolo di amministratore di database molti server parametri e impostazioni inavvertitamente possono influire negativamente sulle prestazioni del server o negare le proprietà ACID del sistema DBMS. Di conseguenza, per mantenere l'integrità del servizio e un contratto di servizio a un livello di prodotto è necessario non esporre il ruolo di amministratore di database per i clienti. L'account utente predefinito, che viene costruito quando viene creata una nuova istanza di database, consente ai clienti di eseguire la maggior parte delle istruzioni DDL e DML nell'istanza di database gestito. 
- SUPER privilegio Analogamente [privilegio SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) anche è limitato.

## <a name="data-manipulation-statement-support"></a>Supporto di istruzione di modifica dati

### <a name="supported"></a>Supportato
- CARICAMENTO dati INFILE - supportato, ma è necessario specificare il parametro di tipo [LOCAL] che viene indirizzato a un percorso UNC (archiviazione di Azure montata tramite XSMB).

### <a name="unsupported"></a>Non supportato
- SELEZIONA... NEL FILE DI OUTPUT

## <a name="preview-functional-limitations"></a>Limiti funzionali dell'anteprima

### <a name="scale-operations"></a>Operazioni di scalabilità
- La scalabilità dinamica dei server tra i livelli di servizio non è attualmente supportata, vale a dire il passaggio tra i livelli di servizio Base e Standard.
- L'aumento dinamico su richiesta dell'archiviazione sul server creato in precedenza non è attualmente supportato.
- La riduzione delle dimensioni di archiviazione del server non è supportato.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata.

### <a name="point-in-time-restore"></a>Ripristino temporizzato
- Il ripristino a un livello di servizio diverso e/o a dimensioni delle unità di calcolo e di archiviazione diverse non è consentito.
- Il ripristino di un server eliminato non è supportato.

## <a name="functional-limitations"></a>Limitazioni funzionali

### <a name="subscription-management"></a>Gestione sottoscrizioni
- Lo spostamento dinamico di server creati in precedenza tra le sottoscrizioni e il gruppo di risorse non è attualmente supportato.

## <a name="current-known-issues"></a>Problemi noti correnti:
- Istanza di MySQL server consente di visualizzare la versione del server non è valida quando viene stabilita una connessione. Per ottenere il server corretto controllo delle versioni di istanza, usare select viene; comando al prompt di MySQL.

## <a name="next-steps"></a>Passaggi successivi
- [Opzioni e prestazioni disponibili in ogni livello di servizio](concepts-service-tiers.md)
- [Versioni supportate del database MySQL](concepts-supported-versions.md)
