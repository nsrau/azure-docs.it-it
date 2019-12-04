---
title: 'Replica dei dati: database di Azure per MariaDB'
description: Informazioni sull'uso della replica dei dati per la sincronizzazione da un server esterno al servizio database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e98f0dffe1ae004905c2b0969d825a1bca89014a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772639"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Eseguire la replica dei dati in Database di Azure per MariaDB

La funzione per la replica dei dati in ingresso consente di sincronizzare i dati da un server MariaDB, eseguito in locale, in macchine virtuali o servizi di database ospitati da altri provider cloud, nel servizio Database di Azure per MariaDB. La replica dei dati in ingresso si basa sulla replica nativa di MariaDB in base alla posizione di file di log binari (binlog). Per altre informazioni su questo tipo di replica, vedere [binlog replication overview](https://mariadb.com/kb/en/library/replication-overview/) (Panoramica della replica basata su binlog).

## <a name="when-to-use-data-in-replication"></a>Quando usare la replica dei dati in ingresso
Gli scenari principali da considerare quando si usa la funzione di replica dei dati in ingresso sono i seguenti:

- **Sincronizzazione ibrida dei dati**: con la replica dei dati in ingresso è possibile mantenere i dati sincronizzati tra i server locali e Database di Azure per MariaDB. Questa sincronizzazione è utile per la creazione di applicazioni ibride. Il metodo è particolarmente interessante quando si ha già un server di database locale, ma si vogliono spostare i dati in un'area più vicina agli utenti finali.
- **Sincronizzazione multi-cloud**: per soluzioni cloud complesse, usare la replica dei dati in ingresso per sincronizzare i dati tra Database di Azure per MariaDB e diversi provider cloud, inclusi servizi di database e macchine virtuali ospitati nei cloud.

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

### <a name="data-not-replicated"></a>Dati non replicati
Il [*database di sistema mysql*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) sul server master non viene replicato. Le modifiche agli account e alle autorizzazioni sul server master non vengono replicate. Se si crea un account sul server master e questo deve accedere al server di replica, creare manualmente lo stesso account sul lato del server di replica. Per informazioni sulle tabelle contenute nel database di sistema, vedere la [documentazione di MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requisiti
- Nel server master deve essere installata almeno la versione 10.2 di MariaDB.
- Le versioni del server master e del server di replica devono essere identiche. Ad esempio, in entrambi i server deve essere installata la versione 10.2 di MariaDB.
- Ogni tabella deve avere una chiave primaria.
- Il server master deve usare il motore InnoDB.
- L'utente deve disporre delle autorizzazioni necessarie per configurare la registrazione binaria e creare nuovi utenti sul server master.
- Se nel server master è abilitato SSL, verificare che il certificato della CA SSL fornito per il dominio sia stato incluso nel stored procedure di `mariadb.az_replication_change_master`. Fare riferimento agli [esempi](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) seguenti e al parametro `master_ssl_ca`.
- Verificare che l'indirizzo IP del server master sia stato aggiunto alle regole firewall del server di replica di Database di Azure per MariaDB. Aggiornare le regole firewall usando il [portale di Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Verificare che il computer che ospita il server master consenta sia il traffico in ingresso che in uscita sulla porta 3306.
- Verificare che il server master disponga di un **indirizzo IP pubblico**, che il DNS sia accessibile pubblicamente o che disponga di un nome di dominio completo (FQDN).

### <a name="other"></a>Altro
- La replica dei dati in ingresso è supportata solo nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare la replica dei dati in ingresso](howto-data-in-replication.md).
