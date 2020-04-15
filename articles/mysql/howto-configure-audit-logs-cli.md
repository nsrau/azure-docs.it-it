---
title: Accesso ai log di controllo - interfaccia della riga di comando di Azure - Database di Azure per MySQLAccess audit logs - Azure CLI - Azure Database for MySQL
description: Questo articolo descrive come configurare e accedere ai log di controllo nel database di Azure per MySQL dall'interfaccia della riga di comando di Azure.This article describes how to configure and access the audit logs in Azure Database for MySQL from the Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384168"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurare e accedere ai log di controllo nell'interfaccia della riga di comando di AzureConfigure and access audit logs in the Azure CLI

È possibile configurare il database di Azure per i log di controllo MySQL dall'interfaccia della riga di comando di Azure.You can configure the [Azure Database for MySQL audit logs](concepts-audit-logs.md) from the Azure CLI.

> [!IMPORTANT]
> La funzionalità del registro di controllo è attualmente in anteprima.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:

- [Server del Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Abilitare e configurare la registrazione di controllo attenendosi alla procedura seguente:Enable and configure audit logging using the following steps:

1. Attivare i registri di controllo impostando il parametro **audit_logs_enabled** su "ON". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selezionare i [tipi di evento](concepts-audit-logs.md#configure-audit-logging) da registrare aggiornando il parametro **audit_log_events.**
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Aggiungere gli utenti MySQL da escludere dalla registrazione aggiornando il parametro **audit_log_exclude_users.** Specificare gli utenti fornendo il proprio nome utente MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Aggiungere eventuali utenti MySQL specifici da includere per la registrazione aggiornando il parametro **audit_log_include_users.** Specificare gli utenti fornendo il proprio nome utente MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui log di controllo in Database di Azure per MySQLLearn more about [audit logs](concepts-audit-logs.md) in Azure Database for MySQL
- Informazioni su come configurare i log di controllo nel portale di [AzureLearn](howto-configure-audit-logs-portal.md) how to configure audit logs in the Azure portal