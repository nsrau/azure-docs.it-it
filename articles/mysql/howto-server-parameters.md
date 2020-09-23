---
title: Configurare i parametri del server-portale di Azure-database di Azure per MySQL
description: Questo articolo illustra come configurare i parametri di server MySQL in Database di Azure per MySQL usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/11/2020
ms.openlocfilehash: a37fbee4361d4a87c43a42cae66c425eba1e0877
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887038"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Configurare i parametri del server nel database di Azure per MySQL usando il portale di Azure

Database di Azure per MySQL supporta la configurazione di alcuni parametri di server. Questo articolo illustra come configurare questi parametri usando il portale di Azure. Non tutti i parametri di server possono essere modificati.

## <a name="configure-server-parameters"></a>Configurare i parametri del server

1. Accedere al portale di Azure, quindi individuare il Database di Azure per il server MySQL.
2. Nella sezione **IMPOSTAZIONI** fare clic su **Parametri del server** per aprire la pagina Parametri del server per il server di Database di Azure per MySQL.
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Pagina Parametri del server del portale di Azure":::
3. Individuare eventuali impostazioni da modificare. Esaminare la colonna **Descrizione** per ottenere informazioni sulle finalità e sui valori consentiti.
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Elenco a discesa dei parametri di tipo enumerato":::
4. Fare clic su  **Salva** per salvare le modifiche.
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Salvataggio o rimozione delle modifiche":::
5. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Ripristino di tutte le impostazioni predefinite":::

## <a name="setting-parameters-not-listed"></a>Impostazione dei parametri non elencati

Se il parametro Server che si desidera aggiornare non è elencato nella portale di Azure, è possibile impostare facoltativamente il parametro a livello di connessione utilizzando `init_connect` . In questo modo vengono impostati i parametri del server per ogni client che si connette al server. 

1. Nella sezione **IMPOSTAZIONI** fare clic su **Parametri del server** per aprire la pagina Parametri del server per il server di Database di Azure per MySQL.
2. Cerca `init_connect`
3. Aggiungere i parametri del server nel formato: `SET parameter_name=YOUR_DESIRED_VALUE` valore nella colonna valore.

    Ad esempio, è possibile modificare il set di caratteri del server impostando `init_connect` su `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Fare clic su **Salva** per salvare le modifiche.

>[!Note]
> `init_connect` può essere usato per modificare i parametri che non richiedono privilegi avanzati a livello di sessione. Per verificare se è possibile impostare il parametro con `init_connect`, eseguire il comando `set session parameter_name=YOUR_DESIRED_VALUE;` e se si verifica l'errore **Access denied; you need SUPER privileges(s)** (Accesso negato, sono necessari privilegi avanzati), allora non è possibile impostare il parametro con "init_connect".

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

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Impostare il parametro di fuso orario":::

### <a name="setting-the-session-level-time-zone"></a>Impostazione del fuso orario a livello di sessione

Per impostare il fuso orario a livello di sessione, eseguire il comando `SET time_zone` da uno strumento come la riga di comando MySQL o MySQL Workbench. L'esempio seguente imposta il fuso orario su **US/Pacific**.

```sql
SET time_zone = 'US/Pacific';
```

Per le [funzioni di data e ora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz), vedere la documentazione di MySQL.

## <a name="next-steps"></a>Passaggi successivi

- [Raccolte connessioni per il Database di Azure per MySQL](concepts-connection-libraries.md).
