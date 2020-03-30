---
title: Replica dei dati - Database di Azure per MySQLData-in replication - Azure Database for MySQL
description: Informazioni sull'uso della replica dei dati per la sincronizzazione da un server esterno nel servizio Database di Azure per MySQL.Learn about using data-in replication to synchronize from an external server into the Azure Database for MySQL service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533233"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Eseguire la replica dei dati in Database di Azure per MySQL

Replica dei dati in ingresso consente di sincronizzare i dati da un server MySQL esterno nel servizio Database di Azure per MySQL. Il server esterno può trovarsi in locale, in macchine virtuali, o essere un servizio di database ospitato da altri provider di servizi cloud. La replica dei dati in ingresso si basa sulla replica nativa di MySQL in base alla posizione di file di log binari (binlog). Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL). 

## <a name="when-to-use-data-in-replication"></a>Quando usare la replica dei dati in ingresso
Gli scenari principali da considerare quando si usa la funzione di replica dei dati in ingresso sono i seguenti:

- **Sincronizzazione ibrida dei dati**: con la replica dei dati in ingresso è possibile mantenere i dati sincronizzati tra i server locali e Database di Azure per MySQL. Questa sincronizzazione è utile per la creazione di applicazioni ibride. Il metodo è particolarmente interessante quando si ha già un server di database locale, ma si vogliono spostare i dati in un'area più vicina agli utenti finali.
- **Sincronizzazione multi-cloud**: per soluzioni cloud complesse, usare la replica dei dati in ingresso per sincronizzare i dati tra Database di Azure per MySQL e diversi provider cloud, inclusi servizi di database e macchine virtuali ospitati nei cloud.
 
Per gli scenari di migrazione, usare il servizio di migrazione del database (DMS) di [Azure.For](https://azure.microsoft.com/services/database-migration/)migration scenarios, use the Azure Database Migration Service (DMS).

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

### <a name="data-not-replicated"></a>Dati non replicati
Il [*database di sistema mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) sul server master non viene replicato. Le modifiche agli account e alle autorizzazioni sul server master non vengono replicate. Se si crea un account sul server master e questo deve accedere al server di replica, creare manualmente lo stesso account sul lato del server di replica. Per informazioni sulle tabelle contenute nel database di sistema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="requirements"></a>Requisiti
- Nel server master deve essere installata almeno la versione 5.6 di MySQL. 
- Le versioni del server master e del server di replica devono essere identiche. Ad esempio, in entrambi deve essere installato MySQL versione 5.6 o MySQL versione 5.7.
- Ogni tabella deve avere una chiave primaria.
- Il server master deve usare il motore InnoDB di MySQL.
- L'utente deve disporre delle autorizzazioni necessarie per configurare la registrazione binaria e creare nuovi utenti sul server master.
- Se nel server master è abilitato SSL, verificare che il `mysql.az_replication_change_master` certificato DELLA CA SSL fornito per il dominio sia stato incluso nella stored procedure. Fare riferimento [examples](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) agli esempi `master_ssl_ca` seguenti e al parametro.
- Verificare che l'indirizzo IP del server master sia stato aggiunto alle regole firewall del server di replica di Database di Azure per MySQL. Aggiornare le regole firewall usando il [portale di Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Verificare che il computer che ospita il server master consenta sia il traffico in ingresso che in uscita sulla porta 3306.
- Verificare che il server master disponga di un **indirizzo IP pubblico,** accessibile pubblicamente o un nome di dominio completo (FQDN).

### <a name="other"></a>Altri
- La replica dei dati in ingresso è supportata solo nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria.
- Gli identificatori di transazione globale (GTID) non sono supportati.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare](howto-data-in-replication.md) la replica dei dati
- Informazioni sulla [replica in Azure con repliche in lettura](concepts-read-replicas.md)
- Informazioni su come eseguire la migrazione dei dati con tempi di [inattività minimi tramite DMSLearn](howto-migrate-online.md) about how to migrate data with minimal downtime using DMS