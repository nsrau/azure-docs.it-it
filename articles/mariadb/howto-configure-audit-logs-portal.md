---
title: Accedere ai log di controllo-portale di Azure-database di Azure per MariaDB
description: Questo articolo descrive come configurare e accedere ai log di controllo nel database di Azure per MariaDB dalla portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: 30ca62305c325943e17ee574873cfbbbaee97f77
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113038"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configurare e accedere ai log di controllo nel portale di Azure

È possibile configurare i log di controllo e le impostazioni di diagnostica del [database di Azure per MariaDB](concepts-audit-logs.md) dalla portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:

- [Database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

>[!IMPORTANT]
> Si consiglia di registrare solo i tipi di evento e gli utenti necessari a scopo di controllo, in modo da garantire che le prestazioni del server non siano fortemente interessate.

Abilitare e configurare la registrazione di controllo.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il server del Database di Azure per MariaDB.

1. Nella sezione **Impostazioni** della barra laterale selezionare **parametri server**.
    ![Parametri del server](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aggiornare il parametro **audit_log_enabled** a on.
    ![Abilitare i log di controllo](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Consente di selezionare i [tipi di evento](concepts-audit-logs.md#configure-audit-logging) da registrare aggiornando il parametro **audit_log_events** .
    ![Eventi del registro di controllo](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Aggiungere gli utenti di MariaDB da escludere dalla registrazione aggiornando il parametro **audit_log_exclude_users** . Specificare gli utenti fornendo il nome utente MariaDB.
    ![Log di controllo escludere gli utenti](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche.
    ![Salva](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

1. Nella sezione **monitoraggio** della barra laterale selezionare impostazioni di **diagnostica**.

1. Fare clic su "+ Aggiungi impostazione di diagnostica" ![ Aggiungi impostazione di diagnostica](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare i sink di dati per inviare i log di controllo (account di archiviazione, Hub eventi e/o area di lavoro Log Analytics).

1. Selezionare "MySqlAuditLogs" come tipo di log.
![Configurare l'impostazione di diagnostica](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Una volta configurati i sink di dati a cui inviare i log di controllo, è possibile fare clic su **Salva**.
![Salva impostazione di diagnostica](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Per accedere ai log di controllo, esplorarli nei sink di dati configurati. Potrebbero essere necessari fino a 10 minuti per la visualizzazione dei log.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [log di controllo](concepts-audit-logs.md) nel database di Azure per MariaDB
- Informazioni su come configurare i log di controllo nell'interfaccia della riga di comando di [Azure](howto-configure-audit-logs-cli.md)