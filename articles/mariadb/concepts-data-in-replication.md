---
title: 'Replica dei dati: database di Azure per MariaDB'
description: Informazioni sull'uso della replica dei dati per la sincronizzazione da un server esterno al servizio database di Azure per MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533430"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Eseguire la replica dei dati in Database di Azure per MariaDB

La funzione per la replica dei dati in ingresso consente di sincronizzare i dati da un server MariaDB, eseguito in locale, in macchine virtuali o servizi di database ospitati da altri provider cloud, nel servizio Database di Azure per MariaDB. La replica dei dati in ingresso si basa sulla replica nativa di MariaDB in base alla posizione di file di log binari (binlog). Per altre informazioni su questo tipo di replica, vedere [binlog replication overview](https://mariadb.com/kb/en/library/replication-overview/) (Panoramica della replica basata su binlog).

## <a name="when-to-use-data-in-replication"></a>Quando usare la replica dei dati in ingresso
Gli scenari principali da considerare quando si usa la funzione di replica dei dati in ingresso sono i seguenti:

- **Sincronizzazione ibrida dei dati** : con la replica dei dati in ingresso è possibile mantenere i dati sincronizzati tra i server locali e Database di Azure per MariaDB. Questa sincronizzazione è utile per la creazione di applicazioni ibride. Il metodo è particolarmente interessante quando si ha già un server di database locale, ma si vogliono spostare i dati in un'area più vicina agli utenti finali.
- **Sincronizzazione multi-cloud** : per soluzioni cloud complesse, usare la replica dei dati in ingresso per sincronizzare i dati tra Database di Azure per MariaDB e diversi provider cloud, inclusi servizi di database e macchine virtuali ospitati nei cloud.

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

### <a name="data-not-replicated"></a>Dati non replicati
Il [*database di sistema MySQL*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) nel server di origine non viene replicato. Le modifiche apportate agli account e alle autorizzazioni nel server di origine non vengono replicate. Se si crea un account nel server di origine e questo account deve accedere al server di replica, creare manualmente lo stesso account sul lato server di replica. Per informazioni sulle tabelle contenute nel database di sistema, vedere la [documentazione di MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requisiti
- La versione del server di origine deve essere almeno la versione 10,2 di MariaDB.
- Le versioni del server di origine e di replica devono essere uguali. Ad esempio, in entrambi i server deve essere installata la versione 10.2 di MariaDB.
- Ogni tabella deve avere una chiave primaria.
- Il server di origine deve usare il motore InnoDB.
- L'utente deve disporre delle autorizzazioni per configurare la registrazione binaria e creare nuovi utenti nel server di origine.
- Se nel server di origine è abilitato SSL, verificare che il certificato della CA SSL fornito per il dominio sia stato incluso nel `mariadb.az_replication_change_master` stored procedure. Fare riferimento agli [esempi](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) seguenti e al `master_ssl_ca` parametro.
- Verificare che l'indirizzo IP del server di origine sia stato aggiunto al database di Azure per le regole del firewall del server di replica di MariaDB. Aggiornare le regole firewall usando il [portale di Azure](howto-manage-firewall-portal.md) o l'[interfaccia della riga di comando di Azure](howto-manage-firewall-cli.md).
- Verificare che il computer che ospita il server di origine consenta il traffico in ingresso e in uscita sulla porta 3306.
- Verificare che il server di origine disponga di un **indirizzo IP pubblico** , che il DNS sia accessibile pubblicamente o che disponga di un nome di dominio completo (FQDN).

### <a name="other"></a>Altro
- La replica dei dati in ingresso è supportata solo nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare la replica dei dati in ingresso](howto-data-in-replication.md).
