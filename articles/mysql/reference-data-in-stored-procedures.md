---
title: Stored procedure per la replica dei dati in ingresso in Database di Azure per MySQL
description: Questo articolo presenta tutte le stored procedure usate per la replica dei dati in ingresso.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: fb1a1b31d90df0022e5973de3ae2f55fb4c36701
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665947"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Stored procedure per la replica dei dati in ingresso in Database di Azure per MySQL

La funzione per la replica dei dati in ingresso consente di sincronizzare i dati da un server MySQL, eseguito in locale, in macchine virtuali o servizi di database ospitati da altri provider cloud, nel servizio Database di Azure per MySQL.

Le stored procedure descritte di seguito vengono usate per configurare o rimuovere la replica dei dati in ingresso tra un server master e un server di replica.

|**Nome della stored procedure**|**Parametri di input**|**Parametri di output**|**Nota sull'utilizzo**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Per trasferire i dati con la modalità SSL, passare il contesto del certificato della CA nel parametro master_ssl_ca. </br><br>Per trasferire i dati senza SSL, passare una stringa vuota nel parametro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Avvia la replica.|
|*mysql.az_replication _stop*|N/D|N/D|Arresta la replica.|
|*mysql.az_replication _remove_master*|N/D|N/D|Rimuove la relazione di replica tra il server master e quello di replica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora un errore di replica.|

Per configurare la replica dei dati in ingresso tra un server master e un server di replica in Database di Azure per MySQL, vedere [Come configurare la replica dei dati in ingresso](howto-data-in-replication.md).
