---
title: Accedere ai log di controllo-portale di Azure-database di Azure per MySQL
description: Questo articolo descrive come configurare e accedere ai log di controllo nel database di Azure per MySQL dalla portale di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: a73a3c77310c0e19792758c0586975e14cfaebf8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541658"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Configurare e accedere ai log di controllo per database di Azure per MySQL nel portale di Azure

È possibile configurare i log di controllo e le impostazioni di diagnostica del [database di Azure per MySQL](concepts-audit-logs.md) dal portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:

- [Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

>[!IMPORTANT]
> Si consiglia di registrare solo i tipi di evento e gli utenti necessari a scopo di controllo, in modo da garantire che le prestazioni del server non siano fortemente interessate.

Abilitare e configurare la registrazione di controllo.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il server del Database di Azure per MySQL.

1. Nella sezione **Impostazioni** della barra laterale selezionare **parametri server**.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Parametri del server":::

1. Aggiornare il parametro **audit_log_enabled** a on.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Abilitare i log di controllo":::

1. Consente di selezionare i [tipi di evento](concepts-audit-logs.md#configure-audit-logging) da registrare aggiornando il parametro **audit_log_events** .
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Eventi del registro di controllo":::

1. Aggiungere eventuali utenti MySQL da includere o escludere dalla registrazione aggiornando i parametri **audit_log_exclude_users** e **audit_log_include_users** . Specificare gli utenti fornendo il nome utente MySQL.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Log di controllo escludere gli utenti":::

1. Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Salva":::

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

1. Nella sezione **monitoraggio** della barra laterale selezionare impostazioni di **diagnostica**.

1. Fare clic su "+ Aggiungi impostazione di diagnostica" :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Aggiungi impostazione di diagnostica":::

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare i sink di dati per inviare i log di controllo (account di archiviazione, Hub eventi e/o area di lavoro Log Analytics).

1. Selezionare "MySqlAuditLogs" come tipo di log.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Configurare l'impostazione di diagnostica":::

1. Una volta configurati i sink di dati a cui inviare i log di controllo, è possibile fare clic su **Salva**.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Salva impostazione di diagnostica":::

1. Per accedere ai log di controllo, esplorarli nei sink di dati configurati. Potrebbero essere necessari fino a 10 minuti per la visualizzazione dei log.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [log di controllo](concepts-audit-logs.md) nel database di Azure per MySQL
- Informazioni su come configurare i log di controllo nell'interfaccia della riga di comando di [Azure](howto-configure-audit-logs-cli.md)