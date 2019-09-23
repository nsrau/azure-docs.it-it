---
title: Stored procedure per database di Azure per MySQL
description: Questo articolo presenta le stored procedure specifiche per database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: f01a0bf68e510133058dc0075f27cfcf6241c7a8
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71156176"
---
# <a name="azure-database-for-mysql-stored-procedures"></a>Stored procedure per database di Azure per MySQL

Le stored procedure sono disponibili nel database di Azure per i server MySQL per semplificare la gestione del server MySQL. Ciò include la gestione delle connessioni, delle query e della configurazione di Replica dei dati in ingresso del server.  

## <a name="data-in-replication-stored-procedures"></a>Stored procedure Replica dei dati in ingresso

La funzione per la replica dei dati in ingresso consente di sincronizzare i dati da un server MySQL, eseguito in locale, in macchine virtuali o servizi di database ospitati da altri provider cloud, nel servizio Database di Azure per MySQL.

Le stored procedure descritte di seguito vengono usate per configurare o rimuovere la replica dei dati in ingresso tra un server master e un server di replica.

|**Nome della stored procedure**|**Parametri di input**|**Parametri di output**|**Nota sull'utilizzo**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Per trasferire i dati con la modalità SSL, passare il contesto del certificato della CA nel parametro master_ssl_ca. </br><br>Per trasferire i dati senza SSL, passare una stringa vuota nel parametro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Avvia la replica.|
|*mysql.az_replication _stop*|N/D|N/D|Arresta la replica.|
|*mysql.az_replication _remove_master*|N/D|N/D|Rimuove la relazione di replica tra il server master e quello di replica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora un errore di replica.|

Per configurare Replica dei dati in ingresso tra un master e una replica nel database di Azure per MySQL, vedere [How to configure replica dei dati in ingresso](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Altre stored procedure

Le stored procedure seguenti sono disponibili nel database di Azure per MySQL per gestire il server.

|**Nome della stored procedure**|**Parametri di input**|**Parametri di output**|**Nota sull'utilizzo**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|N/D|Equivale a [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Termina la connessione associata al processlist_id fornito dopo la terminazione di qualsiasi istruzione che la connessione è in esecuzione.|
|*MySQL. AZ _kill_query*|processlist_id|N/D|Equivale a [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Termina l'istruzione attualmente in esecuzione per la connessione. Lascia attiva la connessione.|
|*MySQL. AZ _load_timezone*|N/D|N/D|Carica le tabelle del fuso orario per `time_zone` consentire l'impostazione del parametro sui valori denominati, ad esempio "US/Pacific".|

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come configurare [replica dei dati in ingresso](howto-data-in-replication.md)
- Informazioni su come usare le [tabelle del fuso orario](howto-server-parameters.md#working-with-the-time-zone-parameter)