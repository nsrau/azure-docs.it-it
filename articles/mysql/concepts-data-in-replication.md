---
title: 'Replica dei dati: database di Azure per MySQL'
description: Informazioni sull'uso della replica dei dati per la sincronizzazione da un server esterno al servizio database di Azure per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: 99beddba470f73d6eadb448dfe1b77453ce6426d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996220"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Eseguire la replica dei dati in Database di Azure per MySQL

Replica dei dati in ingresso consente di sincronizzare i dati da un server MySQL esterno nel servizio Database di Azure per MySQL. Il server esterno può trovarsi in locale, in macchine virtuali, o essere un servizio di database ospitato da altri provider di servizi cloud. La replica dei dati in ingresso si basa sulla replica nativa di MySQL in base alla posizione di file di log binari (binlog). Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL). 

## <a name="when-to-use-data-in-replication"></a>Quando usare la replica dei dati in ingresso
Gli scenari principali da considerare quando si usa la funzione di replica dei dati in ingresso sono i seguenti:

- **Sincronizzazione ibrida dei dati**: con la replica dei dati in ingresso è possibile mantenere i dati sincronizzati tra i server locali e Database di Azure per MySQL. Questa sincronizzazione è utile per la creazione di applicazioni ibride. Il metodo è particolarmente interessante quando si ha già un server di database locale, ma si vogliono spostare i dati in un'area più vicina agli utenti finali.
- **Sincronizzazione multi-cloud**: per soluzioni cloud complesse, usare la replica dei dati in ingresso per sincronizzare i dati tra Database di Azure per MySQL e diversi provider cloud, inclusi servizi di database e macchine virtuali ospitati nei cloud.
 
Per gli scenari di migrazione, usare il [servizio migrazione del database di Azure](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

### <a name="data-not-replicated"></a>Dati non replicati
Il [*database di sistema MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) nel server di origine non viene replicato. Non vengono replicate le modifiche apportate agli account e alle autorizzazioni nel server di origine. Se si crea un account nel server di origine e questo account deve accedere al server di replica, creare manualmente lo stesso account sul lato server di replica. Per informazioni sulle tabelle contenute nel database di sistema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtro
Per ignorare le tabelle di replica del server di origine (ospitate in locale, in macchine virtuali o in un servizio di database ospitato da altri provider di servizi cloud), il `replicate_wild_ignore_table` parametro è supportato. Facoltativamente, aggiornare questo parametro nel server di replica ospitato in Azure usando il [portale di Azure](howto-server-parameters.md) o l'interfaccia della riga di comando di [Azure](howto-configure-server-parameters-using-cli.md).

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table).

### <a name="requirements"></a>Requisiti
- La versione del server di origine deve essere almeno la versione 5,6 di MySQL. 
- Le versioni del server di origine e di replica devono essere uguali. Ad esempio, in entrambi deve essere installato MySQL versione 5.6 o MySQL versione 5.7.
- Ogni tabella deve avere una chiave primaria.
- Il server di origine deve usare il motore InnoDB di MySQL.
- L'utente deve disporre delle autorizzazioni per configurare la registrazione binaria e creare nuovi utenti nel server di origine.
- Se nel server di origine è abilitato SSL, verificare che il certificato della CA SSL fornito per il dominio sia stato incluso nel `mysql.az_replication_change_master` stored procedure. Fare riferimento agli [esempi](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) seguenti e al `master_ssl_ca` parametro.
- Verificare che l'indirizzo IP del server di origine sia stato aggiunto alle regole del firewall del server di replica di database di Azure per MySQL. Aggiornare le regole firewall usando il [portale di Azure](./howto-manage-firewall-using-portal.md) o l'[interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md).
- Verificare che il computer che ospita il server di origine consenta il traffico in ingresso e in uscita sulla porta 3306.
- Verificare che il server di origine disponga di un **indirizzo IP pubblico**, che il DNS sia accessibile pubblicamente o che disponga di un nome di dominio completo (FQDN).

### <a name="other"></a>Altro
- La replica dei dati in ingresso è supportata solo nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria.
- Gli identificatori di transazione globale (GTID) non sono supportati.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare la replica dei dati](howto-data-in-replication.md)
- Informazioni sulla [replica in Azure con repliche in lettura](concepts-read-replicas.md)
- Informazioni su come [eseguire la migrazione dei dati con tempi di inattività minimi con DMS](howto-migrate-online.md)