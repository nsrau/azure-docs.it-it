---
title: Stored procedure di gestione-database di Azure per MySQL
description: Informazioni sulle stored procedure in database di Azure per MySQL sono utili per configurare la replica dei dati, impostare il fuso orario e terminare le query.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7a1aa061bb8c8be3a676e0e5bb690b2a9749b6c8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536133"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Stored procedure per la gestione di database di Azure per MySQL

Le stored procedure sono disponibili nel database di Azure per i server MySQL per semplificare la gestione del server MySQL. Ciò include la gestione delle connessioni, delle query e della configurazione di Replica dei dati in ingresso del server.  

## <a name="data-in-replication-stored-procedures"></a>Stored procedure Replica dei dati in ingresso

La funzione per la replica dei dati in ingresso consente di sincronizzare i dati da un server MySQL, eseguito in locale, in macchine virtuali o servizi di database ospitati da altri provider cloud, nel servizio Database di Azure per MySQL.

Le stored procedure seguenti vengono utilizzate per configurare o rimuovere Replica dei dati in ingresso tra un'origine e una replica.

|**Nome della stored procedure**|**Parametri di input**|**Parametri di output**|**Nota sull'utilizzo**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Per trasferire i dati con la modalità SSL, passare il contesto del certificato della CA nel parametro master_ssl_ca. </br><br>Per trasferire i dati senza SSL, passare una stringa vuota nel parametro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Avvia la replica.|
|*mysql.az_replication _stop*|N/D|N/D|Arresta la replica.|
|*mysql.az_replication _remove_master*|N/D|N/D|Rimuove la relazione di replica tra l'origine e la replica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora un errore di replica.|

Per configurare Replica dei dati in ingresso tra un'origine e una replica nel database di Azure per MySQL, vedere [How to configure replica dei dati in ingresso](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Altre stored procedure

Le stored procedure seguenti sono disponibili nel database di Azure per MySQL per gestire il server.

|**Nome della stored procedure**|**Parametri di input**|**Parametri di output**|**Nota sull'utilizzo**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivale a [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Termina la connessione associata al processlist_id fornito dopo la terminazione di qualsiasi istruzione che la connessione è in esecuzione.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivale a [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Termina l'istruzione attualmente in esecuzione per la connessione. Lascia attiva la connessione.|
|*mysql.az_load_timezone*|N/D|N/D|Carica le tabelle del fuso orario per consentire l' `time_zone` impostazione del parametro sui valori denominati, ad esempio "US/Pacific".|

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come configurare [replica dei dati in ingresso](howto-data-in-replication.md)
- Informazioni su come usare le [tabelle del fuso orario](howto-server-parameters.md#working-with-the-time-zone-parameter)
