---
title: Come configurare i parametri del server in Database di Azure per MySQL
description: Questo articolo illustra come configurare i parametri di server MySQL in Database di Azure per MySQL usando il portale di Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b3510c616d2a9ba66cb83cb998c42e03fdbb0f2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Come configurare i parametri di server in Database di Azure per MySQL usando il portale di Azure

Database di Azure per MySQL supporta la configurazione di alcuni parametri di server. Questo articolo illustra come configurare questi parametri usando il portale di Azure. Non tutti i parametri di server possono essere modificati. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Passare a Parametri del server nel portale di Azure
1. Accedere al portale di Azure, quindi individuare il Database di Azure per il server MySQL.
2. Nella sezione **IMPOSTAZIONI** fare clic su **Parametri del server** per aprire la pagina Parametri del server per il Database di Azure per MySQL.
![Pagina Parametri del server del portale di Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Individuare eventuali impostazioni da modificare. Esaminare la colonna **Descrizione** per ottenere informazioni sulle finalità e sui valori consentiti. 
![Elenco a discesa dei parametri di tipo enumerato](./media/howto-server-parameters/3-toggle_parameter.png)
4. Fare clic su **Salva** per salvare le modifiche.
![Salvataggio o rimozione delle modifiche](./media/howto-server-parameters/4-save_parameters.png)
5. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
![Ripristino di tutte le impostazioni predefinite](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>Elenco di parametri del server configurabili

L'elenco di parametri del server supportati è in continua crescita. Usare la scheda Parametri del server del portale di Azure per ottenere la definizione e configurare i parametri del server in base ai requisiti dell'applicazione. 

## <a name="nonconfigurable-server-parameters"></a>Elenco di parametri del server non configurabili
Pool di buffer InnoDB e Numero max. connessioni non sono configurabili e collegati al [piano tariffario](concepts-service-tiers.md). 

|**Piano tariffario**| **Generazione di calcolo**|**vCore**|**Pool di buffer InnoDB (MB)**| **Numero massimo di connessioni**|
|---|---|---|---|--|
|Basic| Generazione 4| 1| 1024| 50 |
|Basic| Generazione 4| 2| 2560| 100 |
|Basic| Generazione 5| 1| 1024| 50 |
|Basic| Generazione 5| 2| 2560| 100 |
|Utilizzo generico| Generazione 4| 2| 2560| 200|
|Utilizzo generico| Generazione 4| 4| 5120| 400|
|Utilizzo generico| Generazione 4| 8| 10240| 800|
|Utilizzo generico| Generazione 4| 16| 20480| 1600|
|Utilizzo generico| Generazione 4| 32| 40960| 3200|
|Utilizzo generico| Generazione 5| 2| 2560| 200|
|Utilizzo generico| Generazione 5| 4| 5120| 400|
|Utilizzo generico| Generazione 5| 8| 10240| 800|
|Utilizzo generico| Generazione 5| 16| 20480| 1600|
|Utilizzo generico| Generazione 5| 32| 40960| 3200|
|Con ottimizzazione per la memoria| Generazione 5| 2| 7168| 600|
|Con ottimizzazione per la memoria| Generazione 5| 4| 15360| 1250|
|Con ottimizzazione per la memoria| Generazione 5| 8| 30720| 2500|
|Con ottimizzazione per la memoria| Generazione 5| 16| 62464| 5000|
|Con ottimizzazione per la memoria| Generazione 5| 32| 125952| 10000| 

Questi parametri del server aggiuntivi non sono configurabili nel sistema:

|**Parametro**|**Valore fisso**|
| :------------------------ | :-------- |
|innodb_file_per_table nel livello Basic|DISATTIVA|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Gli altri parametri del server che non sono elencati qui vengono impostati sui valori predefiniti MySQL per le versioni [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>Passaggi successivi
- [Raccolte connessioni per il Database di Azure per MySQL](concepts-connection-libraries.md).
