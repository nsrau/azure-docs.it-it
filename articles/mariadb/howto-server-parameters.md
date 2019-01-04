---
title: Configurazione dei parametri del server in Database di Azure per MariaDB
description: Questo articolo illustra come configurare i parametri del server MariaDB in Database di Azure per MariaDB usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0576941c803dfed6359fe34b67370eaf3f99c7e7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538260"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Questo articolo illustra come configurare i parametri di server del Database di Azure per MariaDB usando il portale di Azure

Database di Azure per MariaDB supporta la configurazione di alcuni parametri del server. Questo articolo illustra come configurare questi parametri usando il portale di Azure. Non tutti i parametri di server possono essere modificati.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Passare a Parametri del server nel portale di Azure

1. Accedere al portale di Azure, quindi individuare il Database di Azure per il server MariaDB.
2. Nella sezione **IMPOSTAZIONI**, fare clic su **Parametri del server** per aprire la pagina dei parametri del server del Database di Azure per il server MariaDB.
![Pagina Parametri del server del portale di Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Individuare eventuali impostazioni da modificare. Esaminare la colonna **Descrizione** per ottenere informazioni sulle finalità e sui valori consentiti.
![Elenco a discesa dei parametri di tipo enumerato](./media/howto-server-parameters/3-toggle_parameter.png)
4. Fare clic su **Salva** per salvare le modifiche.
![Salvataggio o rimozione delle modifiche](./media/howto-server-parameters/4-save_parameters.png)
5. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
![Ripristino di tutte le impostazioni predefinite](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Elenco di parametri del server configurabili

L'elenco di parametri del server supportati è in continua crescita. Usare la scheda Parametri del server del portale di Azure per ottenere la definizione e configurare i parametri del server in base ai requisiti dell'applicazione.

## <a name="non-configurable-server-parameters"></a>Elenco di parametri del server non configurabili

Pool di buffer InnoDB e Numero max. connessioni non sono configurabili e collegati al [piano tariffario](concepts-pricing-tiers.md).

|**Piano tariffario**| **vCore**|**Pool di buffer InnoDB (MB)**| **Numero massimo di connessioni**|
|---|---|---|---|--|
|Basic| 1| 1024| 50|
|Basic| 2| 2560| 100|
|Utilizzo generico| 2| 3584| 300|
|Utilizzo generico| 4| 7680| 625|
|Utilizzo generico| 8| 15360| 1250|
|Utilizzo generico| 16| 31232| 2500|
|Utilizzo generico| 32| 62976| 5000|
|Con ottimizzazione per la memoria| 2| 7168| 600|
|Con ottimizzazione per la memoria| 4| 15360| 1250|
|Con ottimizzazione per la memoria| 8| 30720| 2500|
|Con ottimizzazione per la memoria| 16| 62464| 5000|

Questi parametri del server aggiuntivi non sono configurabili nel sistema:

|**Parametro**|**Valore fisso**|
| :------------------------ | :-------- |
|innodb_file_per_table nel livello Basic|DISATTIVA|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Altri parametri non elencati qui sono impostati sui propri valori predefiniti per [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Uso del parametro di fuso orario

### <a name="populating-the-time-zone-tables"></a>Popolare le tabelle di fuso orario

Per popolare le tabelle di fuso orario nel server, è possibile chiamare la stored procedure `az_load_timezone` da uno strumento come la riga di comando MySQL o MySQL Workbench.

> [!NOTE]
> Se si esegue il comando `az_load_timezone` da MySQL Workbench, può essere necessario disattivare la modalità di aggiornamento sicuro tramite `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Per visualizzare i valori di fuso orario disponibili, eseguire questo comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Impostazione del fuso orario a livello globale

Il fuso orario a livello globale può essere impostato nella pagina **Parametri del server** nel portale di Azure. L'esempio seguente imposta il fuso orario globale sul valore "US/Pacific".

![Impostare il parametro di fuso orario](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Impostazione del fuso orario a livello di sessione

Per impostare il fuso orario a livello di sessione, eseguire il comando `SET time_zone` da uno strumento come la riga di comando MySQL o MySQL Workbench. L'esempio seguente imposta il fuso orario su **US/Pacific**.

```sql
SET time_zone = 'US/Pacific';
```

Vedere la documentazione relativa a MariaDB per le [Funzioni di data e ora](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
