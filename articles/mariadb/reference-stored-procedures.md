---
title: Procedure stored di gestione - Database di Azure per MariaDB
description: Informazioni sulle stored procedure nel database di Azure per MariaDB sono utili per configurare la replica dei dati, impostare il fuso orario e interrompere le query.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529991"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Database di Azure per le stored procedure di gestione MariaDBAzure Database for MariaDB management stored procedures

Le stored procedure sono disponibili nel database di Azure per i server MariaDB per facilitare la gestione del server MariaDB. Ciò include la gestione delle connessioni, delle query e dell'impostazione della replica dei dati del server.  

## <a name="data-in-replication-stored-procedures"></a>Stored procedure di replica dei datiData-in Replication stored procedures

La funzione per la replica dei dati in ingresso consente di sincronizzare i dati da un server MariaDB, eseguito in locale, in macchine virtuali o servizi di database ospitati da altri provider cloud, nel servizio Database di Azure per MariaDB.

Le stored procedure descritte di seguito vengono usate per configurare o rimuovere la replica dei dati in ingresso tra un server master e un server di replica.

|**Nome della stored procedure**|**Parametri di input**|**Parametri di output**|**Nota sull'utilizzo**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Per trasferire i dati in modalità SSL, passare il contesto del certificato CA nel parametro master_ssl_ca. </br><br>Per trasferire i dati senza SSL, passare una stringa vuota nel parametro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Avvia la replica.|
|*mysql.az_replication _stop*|N/D|N/D|Arresta la replica.|
|*mysql.az_replication _remove_master*|N/D|N/D|Rimuove la relazione di replica tra il server master e quello di replica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora un errore di replica.|

Per configurare la replica dei dati tra un master e una replica nel database di Azure per MariaDB, vedere [come configurare](howto-data-in-replication.md)la replica dei dati.

## <a name="other-stored-procedures"></a>Altre stored procedure

Le stored procedure seguenti sono disponibili nel database di Azure per MariaDB per gestire il server.

|**Nome della stored procedure**|**Parametri di input**|**Parametri di output**|**Nota sull'utilizzo**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) al comando. Terminerà la connessione associata alla processlist_id fornita dopo la terminazione di qualsiasi istruzione in esecuzione.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) al comando. Terminerà l'istruzione attualmente in esecuzione. Lascia viva la connessione stessa.|
|*mysql.az_load_timezone*|N/D|N/D|Carica le tabelle dei `time_zone` fusi orari per consentire l'impostazione del parametro su valori denominati (ad esempio. "USA/Pacifico").|

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come configurare la [replica dei dati](howto-data-in-replication.md)
- Informazioni sull'utilizzo delle tabelle dei [fuso orari](howto-server-parameters.md#working-with-the-time-zone-parameter)