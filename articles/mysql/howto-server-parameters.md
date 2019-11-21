---
title: Come configurare i parametri del server in Database di Azure per MySQL
description: Questo articolo illustra come configurare i parametri di server MySQL in Database di Azure per MySQL usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 6b5d48a1d198b62af853a6334de41bad01b3c98c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232543"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Come configurare i parametri di server in Database di Azure per MySQL usando il portale di Azure

Database di Azure per MySQL supporta la configurazione di alcuni parametri di server. Questo articolo illustra come configurare questi parametri usando il portale di Azure. Non tutti i parametri di server possono essere modificati.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Passare a Parametri del server nel portale di Azure

1. Accedere al portale di Azure, quindi individuare il Database di Azure per il server MySQL.
2. Nella sezione **IMPOSTAZIONI** fare clic su **Parametri del server** per aprire la pagina Parametri del server per il server di Database di Azure per MySQL.
![Pagina Parametri del server del portale di Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Individuare eventuali impostazioni da modificare. Esaminare la colonna **Descrizione** per ottenere informazioni sulle finalità e sui valori consentiti.
![Elenco a discesa dei parametri di tipo enumerato](./media/howto-server-parameters/3-toggle_parameter.png)
4. Fare clic su **Salva** per salvare le modifiche.
![Salvataggio o rimozione delle modifiche](./media/howto-server-parameters/4-save_parameters.png)
5. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
![Ripristino di tutte le impostazioni predefinite](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Elenco di parametri del server configurabili

L'elenco di parametri del server supportati è in continua crescita. Usare la scheda Parametri del server del portale di Azure per ottenere la definizione e configurare i parametri del server in base ai requisiti dell'applicazione.

## <a name="non-configurable-server-parameters"></a>Elenco di parametri del server non configurabili

The InnoDB Buffer Pool size is not configurable and tied to your [pricing tier](concepts-service-tiers.md).

|**Piano tariffario**|**vCore**|**InnoDB Buffer Pool size in MB <br>(servers supporting up to 4 TB storage)**| **InnoDB Buffer Pool size in MB <br>(servers supporting up to 16 TB storage)**|
|:---|---:|---:|---:|
|Basic| 1| 960| |
|Basic| 2| 2560| |
|Scopo generico| 2| 3584| 7168|
|Scopo generico| 4| 7680| 15360|
|Scopo generico| 8| 15360| 30720|
|Scopo generico| 16| 31232| 62464|
|Scopo generico| 32| 62976| 125952|
|Scopo generico| 64| 125952| 251904|
|Ottimizzate per la memoria| 2| 7168| 14336|
|Ottimizzate per la memoria| 4| 15360| 30720|
|Ottimizzate per la memoria| 8| 30720| 61440|
|Ottimizzate per la memoria| 16| 62464| 124928|
|Ottimizzate per la memoria| 32| 125952| 251904|

Questi parametri del server aggiuntivi non sono configurabili nel sistema:

|**Parametro**|**Valore fisso**|
| :------------------------ | :-------- |
|innodb_file_per_table nel livello Basic|DISATTIVA|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Gli altri parametri del server che non sono elencati qui vengono impostati sui valori predefiniti MySQL per le versioni [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

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

Per le [funzioni di data e ora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz), vedere la documentazione di MySQL.

## <a name="next-steps"></a>Passaggi successivi

- [Raccolte connessioni per il Database di Azure per MySQL](concepts-connection-libraries.md).
