---
title: Configurare e accedere ai log di controllo-database di Azure per MySQL
description: Questo articolo descrive come configurare e accedere ai log di controllo nel database di Azure per MySQL dalla portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 2a2d2a697f0e41fb296c61c01909a814678f8277
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350399"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Configurare e accedere ai log di controllo per database di Azure per MySQL nel portale di Azure

È possibile configurare i log di controllo e le impostazioni di diagnostica del [database di Azure per MySQL](concepts-audit-logs.md) dal portale di Azure.

> [!IMPORTANT]
> La funzionalità del log di controllo è attualmente in anteprima.

## <a name="prerequisites"></a>prerequisiti

Per proseguire con questa guida è necessario:

- [Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Abilitare e configurare la registrazione di controllo.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il server del Database di Azure per MySQL.

1. Nella sezione **Impostazioni** della barra laterale selezionare **parametri server**.
    ![Parametri del server](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aggiornare il parametro **audit_log_enabled** a on.
    ![abilitare i log di controllo](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Consente di selezionare i [tipi di evento](concepts-audit-logs.md#configure-audit-logging) da registrare aggiornando il parametro **audit_log_events** .
    ![eventi del log di controllo](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Aggiungere gli utenti MySQL da escludere dalla registrazione aggiornando il parametro **audit_log_exclude_users** . Specificare gli utenti fornendo il nome utente MySQL.
    ![log di controllo escludere gli utenti](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche.
    ![Salva](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

1. Nella sezione **monitoraggio** della barra laterale selezionare impostazioni di **diagnostica**.

1. Fare clic su "+ Aggiungi impostazione di diagnostica" ![Aggiungi impostazione di diagnostica](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare i sink di dati per inviare i log di controllo (account di archiviazione, Hub eventi e/o area di lavoro Log Analytics).

1. Selezionare "MySqlAuditLogs" come tipo di log.
![configurare l'impostazione di diagnostica](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Una volta configurati i sink di dati a cui inviare i log di controllo, è possibile fare clic su **Salva**.
![salvare l'impostazione di diagnostica](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Per accedere ai log di controllo, esplorarli nei sink di dati configurati. Potrebbero essere necessari fino a 10 minuti per la visualizzazione dei log.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [log di controllo](concepts-audit-logs.md) nel database di Azure per MySQL.