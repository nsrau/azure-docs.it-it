---
title: Accedere ai log di controllo-Azure CLI-database di Azure per MySQL
description: Questo articolo descrive come configurare e accedere ai log di controllo in database di Azure per MySQL dall'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 568f49565c6e6d8062f8869566cf3879b7c97eaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503328"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurare e accedere ai log di controllo nell'interfaccia della riga di comando di Azure

È possibile configurare i [log di controllo del database di Azure per MySQL](concepts-audit-logs.md) dall'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:

- [Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

>[!IMPORTANT]
> Si consiglia di registrare solo i tipi di evento e gli utenti necessari a scopo di controllo, in modo da garantire che le prestazioni del server non siano fortemente interessate.

Abilitare e configurare la registrazione di controllo usando la procedura seguente:

1. Attivare i log di controllo impostando il parametro **audit_logs_enabled** su "on". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Consente di selezionare i [tipi di evento](concepts-audit-logs.md#configure-audit-logging) da registrare aggiornando il parametro **audit_log_events** .
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Aggiungere gli utenti MySQL da escludere dalla registrazione aggiornando il parametro **audit_log_exclude_users** . Specificare gli utenti fornendo il nome utente MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Aggiungere gli utenti specifici di MySQL da includere per la registrazione aggiornando il parametro **audit_log_include_users** . Specificare gli utenti fornendo il nome utente MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [log di controllo](concepts-audit-logs.md) nel database di Azure per MySQL
- Informazioni su come configurare i log di controllo nel [portale di Azure](howto-configure-audit-logs-portal.md)
