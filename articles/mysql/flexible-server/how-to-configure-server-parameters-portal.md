---
title: Configurare i parametri del server-portale di Azure-server flessibile per database di Azure per MySQL
description: Questo articolo descrive come configurare i parametri del server MySQL nel database di Azure per il server flessibile MySQL usando il portale di Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 9568dfc2cfd678d0ce2dea8475328358906e16d1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92525135"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurare i parametri del server nel database di Azure per MySQL-server flessibile usando il portale di Azure

È possibile gestire la configurazione del server flessibile del database di Azure per MySQL usando i parametri del server. Quando si crea il server, i parametri del server sono configurati con il valore predefinito e consigliato.  

Questo articolo descrive come visualizzare e configurare i parametri del server usando il portale di Azure. Il pannello parametro Server in portale di Azure Mostra sia il parametro Server modificabile che non modificabile. I parametri del server non modificabili sono in grigio.

>[!Note]
> I parametri del server possono essere aggiornati globalmente a livello di server, usare l'interfaccia della riga di comando di [Azure](./how-to-configure-server-parameters-cli.md) o [portale di Azure](./how-to-configure-server-parameters-portal.md).

## <a name="configure-server-parameters"></a>Configurare i parametri del server

1. Accedere al [portale di Azure](https://portal.azure.com), quindi individuare il database di Azure per il server flessibile MySQL.
2. Nella sezione **Impostazioni** fare clic su **parametri del server** per aprire la pagina parametri del server per il server flessibile database di Azure per MySQL.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Pagina parametri del server portale di Azure":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Individuare i parametri del server che è necessario modificare. Esaminare la colonna **Descrizione** per ottenere informazioni sulle finalità e sui valori consentiti.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Pagina parametri del server portale di Azure":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Fare clic su  **Salva** per salvare le modifiche.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Pagina parametri del server portale di Azure":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. I parametri statici sono quelli che richiedono il riavvio del server. Se si modifica il parametro statico, verrà richiesto di **riavviare ora** o di **riavviarlo in un secondo momento**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Pagina parametri del server portale di Azure":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Pagina parametri del server portale di Azure":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Impostazione dei parametri del server non modificabili

Se il parametro Server che si desidera aggiornare non è modificabile, è possibile impostare facoltativamente il parametro a livello di connessione utilizzando `init_connect` . In questo modo vengono impostati i parametri del server per ogni client che si connette al server. 

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
> È necessario riavviare il server per assicurarsi che le tabelle del fuso orario siano popolate correttamente.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Per visualizzare i valori di fuso orario disponibili, eseguire questo comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Impostazione del fuso orario a livello globale

Il fuso orario a livello globale può essere impostato nella pagina **Parametri del server** nel portale di Azure. L'esempio seguente imposta il fuso orario globale sul valore "US/Pacific".

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Pagina parametri del server portale di Azure":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Impostazione del fuso orario a livello di sessione

Per impostare il fuso orario a livello di sessione, eseguire il comando `SET time_zone` da uno strumento come la riga di comando MySQL o MySQL Workbench. L'esempio seguente imposta il fuso orario su **US/Pacific**.

```sql
SET time_zone = 'US/Pacific';
```

Per le [funzioni di data e ora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz), vedere la documentazione di MySQL.

## <a name="next-steps"></a>Passaggi successivi

- Come configurare i [parametri del server nell'interfaccia della riga di comando di Azure](./how-to-configure-server-parameters-cli.md)
