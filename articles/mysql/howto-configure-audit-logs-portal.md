---
title: Accedere ai log di controllo - Portale di Azure - Database di Azure per MySQLAccess audit logs - Azure portal - Azure Database for MySQL
description: Questo articolo descrive come configurare e accedere ai log di controllo in Database di Azure per MySQL dal portale di Azure.This article describes how to configure and access the audit logs in Azure Database for MySQL from the Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: fcd436f87b24d8cdc246d2f1d33a49311e72d46b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380294"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Configurare e accedere ai log di controllo per il database di Azure per MySQL nel portale di AzureConfigure and access audit logs for Azure Database for MySQL in the Azure portal

È possibile configurare il database di Azure per i log di controllo MySQL e le impostazioni di diagnostica dal portale di Azure.You can configure the [Azure Database for MySQL audit logs](concepts-audit-logs.md) and diagnostic settings from the Azure portal.

> [!IMPORTANT]
> La funzionalità del registro di controllo è attualmente in anteprima.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:

- [Server del Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Abilitare e configurare la registrazione di controllo.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il server del Database di Azure per MySQL.

1. Nella sezione **Impostazioni** della barra laterale selezionare **Parametri server.**
    ![Parametri del server](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aggiornare il parametro **audit_log_enabled** su ON.
    ![Abilitare i registri di controlloEnable audit logs](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selezionare i [tipi di evento](concepts-audit-logs.md#configure-audit-logging) da registrare aggiornando il parametro **audit_log_events.**
    ![Eventi del registro di controllo](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Aggiungere gli utenti MySQL da escludere dalla registrazione aggiornando il parametro **audit_log_exclude_users.** Specificare gli utenti fornendo il proprio nome utente MySQL.
    ![Il log di controllo esclude gli utenti](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche.
    ![Salva](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

1. Nella sezione **Monitoraggio** della barra laterale selezionare **Impostazioni di diagnostica.**

1. Fare clic su "Aggiungi ![impostazione diagnostica" Aggiungi impostazione diagnostica](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare i sink di dati da inviare ai log di controllo (account di archiviazione, hub eventi e/o area di lavoro di Log Analytics).

1. Selezionare "MySqlAuditLogs" come tipo di log.
![Configurare l'impostazione di diagnostica](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Dopo aver configurato i sink di dati in cui eseguire la gestione dei registri di controllo, è possibile fare clic su **Salva**.
![Salva impostazione diagnostica](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Accedere ai log di controllo esplorandoli nei sink di dati configurati. La visualizzazione dei registri può richiedere fino a 10 minuti.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui log di controllo in Database di Azure per MySQLLearn more about [audit logs](concepts-audit-logs.md) in Azure Database for MySQL
- Informazioni su come configurare i log di controllo nell'interfaccia della riga di comando di [AzureLearn](howto-configure-audit-logs-cli.md) how to configure audit logs in the Azure CLI