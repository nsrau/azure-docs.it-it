---
title: Configurare e accedere ai log di controllo per Database di Azure per MySQL nel portale di Azure
description: Questo articolo descrive come configurare e accedere ai log di controllo nel Database di Azure per MySQL dal portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: a2f44b52c6d34adb1bebf666ff8453b17f7778a5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448476"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configurare e accedere ai log di controllo nel portale di Azure

È possibile configurare il [Database di Azure per i log di controllo di MySQL](concepts-audit-logs.md) e le impostazioni di diagnostica dal portale di Azure.

> [!IMPORTANT]
> Funzionalità di log di controllo è attualmente in anteprima.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:

- [Server del Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Abilitare e configurare la registrazione di controllo.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il server del Database di Azure per MySQL.

1. Sotto il **le impostazioni** sezione della barra laterale selezionare **i parametri del Server**.
    ![Parametri del server](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aggiorna il **audit_log_enabled** parametro su ON.
    ![Abilitare i log di controllo](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selezionare il [tipi di evento](concepts-audit-logs.md#configure-audit-logging) da registrare, aggiornando il **audit_log_events** parametro.
    ![Eventi di log di controllo](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Aggiungere qualsiasi utente di MySQL da escludere dalla registrazione aggiornando il **audit_log_exclude_users** parametro. Specificare gli utenti, fornendo il proprio nome utente di MySQL.
    ![Utenti di escludere log di controllo](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche.
    ![Salva](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

1. Sotto il **Monitoring** sezione della barra laterale selezionare **le impostazioni di diagnostica**.

1. Fare clic su "+ Aggiungi impostazione di diagnostica" ![Aggiungi impostazione di diagnostica](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Specificare un nome di impostazione di diagnostica.

1. Specificare quali dati di sink per inviare i log di controllo (account di archiviazione, hub eventi e/o dell'area di lavoro di Log Analitica).

1. Selezionare "MySqlAuditLogs" come tipo di log.
![Configurare l'impostazione di diagnostica](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Dopo aver configurato il sink di dati per inviare tramite pipe i log di controllo per, è possibile fare clic su **salvare**.
![Salvare l'impostazione di diagnostica](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. I log di controllo per accedere, consultandoli in sink di dati che è stato configurato. Potrebbero occorrere fino a 10 minuti perché vengano visualizzati i log.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [log di controllo](concepts-audit-logs.md) nel Database di Azure per MySQL.