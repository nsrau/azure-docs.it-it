---
title: Limiti di Database di Azure per MySQL
description: Questo articolo descrive i limiti di Database di Azure per MySQL, ad esempio il numero di connessioni e le opzioni del motore di archiviazione.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 85e57170c1cbd977d2de6e7e614916333c79e047
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limiti di Database di Azure per MySQL
Il servizio Database di Azure per MySQL è in anteprima pubblica. Le sezioni seguenti illustrano la capacità, il supporto del motore di archiviazione, dei privilegi e delle istruzioni di gestione dei dati e i limiti funzionali del servizio di database. Vedere anche le [limitazioni generali](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) applicabili al motore di database MySQL.

## <a name="service-tier-maximums"></a>Valori massimi del livello di servizio
Database di Azure per MySQL offre più livelli di servizio tra cui è possibile scegliere durante la creazione di un server. Per altre informazioni, vedere [Piano tariffario di Database di Azure per MySQL](concepts-pricing-tiers.md).  

Esiste una quantità massima di connessioni, unità di calcolo e spazio di archiviazione in ogni livello di servizio durante l'anteprima del servizio, come indicato di seguito: 

|**Piano tariffario**| **Generazione di calcolo**|**vCore**| **Numero massimo di connessioni**|
|---|---|---|---|
|Basic| Generazione 4| 1| 50|
|Basic| Generazione 4| 2| 100|
|Basic| Generazione 5| 1| 50|
|Basic| Generazione 5| 2| 100|
|Utilizzo generico| Generazione 4| 2| 200|
|Utilizzo generico| Generazione 4| 4| 400|
|Utilizzo generico| Generazione 4| 8| 800|
|Utilizzo generico| Generazione 4| 16| 1600|
|Utilizzo generico| Generazione 4| 32| 3200|
|Utilizzo generico| Generazione 5| 2| 200|
|Utilizzo generico| Generazione 5| 4| 400|
|Utilizzo generico| Generazione 5| 8| 800|
|Utilizzo generico| Generazione 5| 16| 1600|
|Utilizzo generico| Generazione 5| 32| 3200|
|Con ottimizzazione per la memoria| Generazione 5| 2| 600|
|Con ottimizzazione per la memoria| Generazione 5| 4| 1250|
|Con ottimizzazione per la memoria| Generazione 5| 8| 2500|
|Con ottimizzazione per la memoria| Generazione 5| 16| 5000|
|Con ottimizzazione per la memoria| Generazione 5| 32| 10000| 

Quando viene raggiunto un numero eccessivo di connessioni, è possibile che si riceva l'errore seguente:
> ERROR 1040 (08004): Too many connections (ERRORE 1040 (08004): numero eccessivo di connessioni)

## <a name="storage-engine-support"></a>Supporto del motore di archiviazione

### <a name="supported"></a>Supportato
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Non supportato
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Supporto dei privilegi

### <a name="unsupported"></a>Non supportato
- Ruolo DBA: molti parametri e impostazioni server possono accidentalmente influire in modo negativo sulle prestazioni del server o negare le proprietà ACID del sistema DBMS. Per mantenere quindi l'integrità del servizio e un contratto di servizio a livello di prodotto, il ruolo DBA non è esposto. L'account utente predefinito, costruito quando viene creata una nuova istanza di database, consente agli utenti di eseguire la maggior parte delle istruzioni DDL e DML nell'istanza di database gestita. 
- Privilegi SUPER: in modo analogo, anche i [privilegi SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) presentano limitazioni.

## <a name="data-manipulation-statement-support"></a>Supporto delle istruzioni di gestione dei dati

### <a name="supported"></a>Supportato
- LOAD DATA INFILE: istruzione supportata ma deve contenere la specificazione del parametro [LOCAL] indirizzato a un percorso UNC (archiviazione di Azure montata tramite XSMB).

### <a name="unsupported"></a>Non supportato
- SELECT... INTO OUTFILE

## <a name="preview-functional-limitations"></a>Limiti funzionali dell'anteprima

### <a name="scale-operations"></a>Operazioni di scalabilità
- Non è attualmente supportata la scalabilità dinamica dei server tra i piani tariffari, ovvero il passaggio tra i piani Basic, Utilizzo generico e Con ottimizzazione per la memoria.
- La riduzione delle dimensioni di archiviazione del server non è supportato.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server
- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata.

### <a name="point-in-time-restore"></a>Ripristino temporizzato
- Il ripristino a un livello di servizio diverso e/o a dimensioni delle unità di calcolo e di archiviazione diverse non è consentito.
- Il ripristino di un server eliminato non è supportato.

## <a name="functional-limitations"></a>Limitazioni funzionali

### <a name="subscription-management"></a>Gestione sottoscrizioni
- Lo spostamento dinamico di server creati in precedenza tra le sottoscrizioni e il gruppo di risorse non è attualmente supportato.

## <a name="current-known-issues"></a>Problemi attualmente noti
- Quando viene stabilita la connessione, l'istanza del server MySQL visualizza una versione di server errata. Per ottenere la versione corretta dell'istanza del server, digitare il comando select version(); al prompt di MySQL.

## <a name="next-steps"></a>Passaggi successivi
- [Opzioni e prestazioni disponibili in ogni livello di servizio](concepts-pricing-tiers.md)
- [Versioni supportate del database MySQL](concepts-supported-versions.md)
