---
title: Come configurare i parametri di server in Database di Azure per MySQL | Microsoft Docs
description: Questo articolo illustra come configurare i parametri di server disponibili in Database di Azure per MySQL usando il portale di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/19/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 718bbf359253849fbc989c563ffd6d1099f92348
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Come configurare i parametri di server in Database di Azure per MySQL usando il portale di Azure

Database di Azure per MySQL supporta la configurazione di alcuni parametri di server. Questo articolo illustra come configurare questi parametri tramite il portale di Azure ed elenca i parametri supportati, i valori predefiniti e l'intervallo di valori validi. Non tutti i parametri di server possono essere modificati. Sono supportati solo quelli elencati di seguito.

## <a name="navigate-to-server-parameters-blade-on-azure-portal"></a>Passare al pannello Parametri del server nel portale di Azure.

Accedere al portale di Azure e quindi fare clic sul nome del server Database di Azure per MySQL. Nella sezione **IMPOSTAZIONI** fare clic su **Parametri del server** per aprire il pannello Parametri del server per il Database di Azure per MySQL.

![Pannello Parametri del server del portale di Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Elenco di parametri del server configurabili

La tabella seguente contiene un elenco dei parametri del server attualmente supportati. I parametri possono essere configurati in base ai requisiti dell'applicazione.

> [!div class="mx-tableFixed"]
|Nome parametro|Valore predefinito|Range|Descrizione|
|---|---|---|---|
|binlog_group_commit_sync_delay|1000|0, 11-1000000|Controlla quanti microsecondi il commit del registro binario attende prima di sincronizzare i file di registro binario su disco.|
|binlog_group_commit_sync_no_delay_count|0|0-1000000|Numero massimo di transazioni da attendere prima di interrompere il ritardo corrente come specificato da binlog-group-commit-sync-delay.|
|character_set_server|LATIN1|BIG5, UTF8MB4 e così via.|Usare charset_name come set di caratteri del server predefinito.|
|div_precision_increment|4|0-30|Numero di cifre di cui aumentare la scala del risultato di operazioni di divisione.|
|group_concat_max_len|1024|4-16777216|Lunghezza massima consentita del risultato in byte per GROUP_CONCAT().|
|innodb_adaptive_hash_index|ATTIVA|ATTIVA, DISATTIVA|Indica se gli indici hash adattivi di InnoDB sono abilitati o disabilitati.|
|innodb_lock_wait_timeout|50|1-3600|L'intervallo di tempo in secondi in cui una transazione InnoDB attende un blocco di riga prima di rinunciare.|
|interactive_timeout|1800|10-1800|Numero di secondi in cui il server resta in attesa di attività su una connessione interattiva prima di chiudere.|
|log_bin_trust_function_creators|DISATTIVA|ATTIVA, DISATTIVA|Questa variabile si applica quando è abilitata la registrazione binaria. Controlla se gli autori di funzioni archiviate possono essere considerati attendibili nel non creare funzioni archiviate che generano eventi non sicuri che verranno scritti nel registro binario.|
|log_queries_not_using_indexes|DISATTIVA|ATTIVA, DISATTIVA|Registra le query che potrebbero recuperare tutte le righe nel log di query lente.|
|log_slow_admin_statements|DISATTIVA|ATTIVA, DISATTIVA|Includere istruzioni amministrative lente nelle istruzioni scritte nel log di query lente.|
|log_throttle_queries_not_using_indexes|0|0-4294967295|Limita il numero di tali query al minuto che possono essere scritte nel log di query lente.|
|long_query_time|10|1-1E + 100|Se una query richiede più tempo rispetto a questo numero di secondi, il server incrementa la variabile di stato Slow_queries.|
|max_allowed_packet|536870912|1024-1073741824|Dimensioni massime di un pacchetto o qualsiasi stringa intermedia/generata o qualsiasi parametro inviato dalla funzione API C mysql_stmt_send_long_data().|
|min_examined_row_limit|0|0-18446744073709551615|Registra le query che hanno un numero di righe maggiore di quello configurato nel log di query lente. |
|server_id|3293747068|1000-4294967295|ID del server, usato nella replica per assegnare a ogni master e ogni slave un'identità univoca.|
|slave_net_timeout|60|30-3600|Numero di secondi che il master attende per ricevere altri dati prima che lo slave consideri la connessione interrotta, interrompa la lettura e tenti di riconnettersi.|
|slow_query_log|DISATTIVA|ATTIVA, DISATTIVA|Abilitare o disabilitare il log di query lente.|
|sql_mode|0 selezionato|ALLOW_INVALID_DATES, IGNORE_SPACE e così via.|Modalità SQL server attuale.|
|time_zone|SYSTEM|esempi: -8:00, +05:30|Fuso orario del server.|
|wait_timeout|120|60-240|Numero di secondi in cui il server resta in attesa di attività su una connessione non interattiva prima di chiudere.|

## <a name="next-steps"></a>Passaggi successivi
- [Raccolte connessioni per il Database di Azure per MySQL](concepts-connection-libraries.md)

