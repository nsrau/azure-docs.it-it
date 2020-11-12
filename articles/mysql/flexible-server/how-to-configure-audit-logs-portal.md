---
title: Configurare i log di controllo-portale di Azure-database di Azure per MySQL-server flessibile
description: Questo articolo descrive come configurare e accedere ai log di controllo nel database di Azure per il server flessibile MySQL dal portale di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: ebb980aa257fc09c3d6a407febbf60f2d1a26a4e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536473"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurare e accedere ai log di controllo per database di Azure per MySQL-server flessibile usando il portale di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

È possibile configurare le impostazioni di diagnostica e i [log di controllo](concepts-audit-logs.md) del server di database di Azure per MySQL flessibili dal portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per la procedura descritta in questo articolo è necessario disporre di un [server flessibile](quickstart-create-server-portal.md).

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

>[!IMPORTANT]
> Si consiglia di registrare solo i tipi di evento e gli utenti necessari a scopo di controllo, in modo da garantire che le prestazioni del server non siano fortemente interessate.

Abilitare e configurare la registrazione di controllo.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il server flessibile.

1. Nella sezione **Impostazioni** della barra laterale selezionare **parametri server**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="Parametri del server":::

1. Aggiornare il parametro **audit_log_enabled** a on.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Abilitare i log di controllo":::

1. Consente di selezionare i [tipi di evento](concepts-audit-logs.md#configure-audit-logging) da registrare aggiornando il parametro **audit_log_events** .
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="Eventi del registro di controllo":::

1. Aggiungere eventuali utenti MySQL da includere o escludere dalla registrazione aggiornando i parametri **audit_log_exclude_users** e **audit_log_include_users** . Specificare gli utenti fornendo il nome utente MySQL.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Log di controllo escludere gli utenti":::

1. Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="Salva":::

## <a name="set-up-diagnostics"></a>Configurare la diagnostica

I log di controllo sono integrati con le impostazioni di diagnostica di monitoraggio di Azure per consentire l'invio tramite pipe dei log a log di monitoraggio di Azure, Hub eventi o archiviazione di Azure.

1. Nella sezione **monitoraggio** della barra laterale selezionare impostazioni di **diagnostica**.

1. Fare clic su "+ Aggiungi impostazione di diagnostica"  :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Aggiungi impostazione di diagnostica":::

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare quali destinazioni inviare i log di controllo (account di archiviazione, Hub eventi e/o area di lavoro Log Analytics).

1. Selezionare **MySqlAuditLogs** come tipo di log.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Configurare l'impostazione di diagnostica":::

1. Una volta configurati i sink di dati a cui inviare i log di controllo, è possibile fare clic su **Salva**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Salva impostazione di diagnostica":::

1. Per accedere ai log di controllo, esplorarli nei sink di dati configurati. Potrebbero essere necessari fino a 10 minuti per la visualizzazione dei log.

Se i log di controllo sono stati reindirizzati ai log di monitoraggio di Azure (Log Analytics), fare riferimento ad alcune [query di esempio](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) che è possibile usare per l'analisi.  

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [log di controllo](concepts-audit-logs.md)
- Informazioni sui [log di query lente](concepts-slow-query-logs.md)
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->