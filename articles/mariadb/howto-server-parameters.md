---
title: Configurare i parametri del server-portale di Azure database di Azure per MariaDB
description: Questo articolo illustra come configurare i parametri del server MariaDB in Database di Azure per MariaDB usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 9d0b6865c7fb5b59f379568d15bd9b96883202e9
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626429"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-portal"></a>Configurare i parametri del server nel database di Azure per MariaDB usando il portale di Azure

Database di Azure per MariaDB supporta la configurazione di alcuni parametri del server. Questo articolo illustra come configurare questi parametri usando il portale di Azure. Non tutti i parametri di server possono essere modificati.

>[!Note]
> I parametri del server possono essere aggiornati globalmente a livello di server, usare l'interfaccia della riga di comando di [Azure](./howto-configure-server-parameters-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md)o [portale di Azure](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Configurare i parametri del server

1. Accedere al portale di Azure, quindi individuare il Database di Azure per il server MariaDB.
2. Nella sezione **IMPOSTAZIONI**, fare clic su **Parametri del server** per aprire la pagina dei parametri del server del Database di Azure per il server MariaDB.
![Pagina Parametri del server del portale di Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Individuare eventuali impostazioni da modificare. Esaminare la colonna **Descrizione** per ottenere informazioni sulle finalità e sui valori consentiti.
![Elenco a discesa dei parametri di tipo enumerato](./media/howto-server-parameters/3-toggle_parameter.png)
4. Fare clic su  **Salva** per salvare le modifiche.
![Salvataggio o rimozione delle modifiche](./media/howto-server-parameters/4-save_parameters.png)
5. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
![Ripristino di tutte le impostazioni predefinite](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>Impostazione dei parametri non elencati

Se il parametro Server che si desidera aggiornare non è elencato nella portale di Azure, è possibile impostare facoltativamente il parametro a livello di connessione utilizzando `init_connect` . In questo modo vengono impostati i parametri del server per ogni client che si connette al server. 

1. Nella sezione **IMPOSTAZIONI**, fare clic su **Parametri del server** per aprire la pagina dei parametri del server del Database di Azure per il server MariaDB.
2. Cerca `init_connect`
3. Aggiungere i parametri del server nel formato: `SET parameter_name=YOUR_DESIRED_VALUE` valore nella colonna valore.

    Ad esempio, è possibile modificare il set di caratteri del server impostando `init_connect` su `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Fare clic su **Salva** per salvare le modifiche.

## <a name="working-with-the-time-zone-parameter"></a>Uso del parametro di fuso orario

### <a name="populating-the-time-zone-tables"></a>Popolare le tabelle di fuso orario

Per popolare le tabelle di fuso orario nel server, è possibile chiamare la stored procedure `mysql.az_load_timezone` da uno strumento come la riga di comando MySQL o MySQL Workbench.

> [!NOTE]
> Se si esegue il comando `mysql.az_load_timezone` da MySQL Workbench, può essere necessario disattivare la modalità di aggiornamento sicuro tramite `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> È necessario riavviare il server per assicurarsi che le tabelle del fuso orario siano popolate correttamente. Per riavviare il server, usare il [portale di Azure](howto-restart-server-portal.md) o l'[interfaccia della riga di comando](howto-restart-server-cli.md).
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

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [parametri del server](concepts-server-parameters.md)
