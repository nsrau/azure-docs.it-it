---
title: Accedere ai log di controllo-Azure CLI-database di Azure per MySQL
description: Questo articolo descrive come configurare e accedere ai log di controllo in database di Azure per MySQL dall'interfaccia della riga di comando di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7ffabae785a589bacf349356079f6046039f9c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541998"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurare e accedere ai log di controllo nell'interfaccia della riga di comando di Azure

È possibile configurare i [log di controllo del database di Azure per MySQL](concepts-audit-logs.md) dall'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa guida dettagliata:

- È necessario un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questo articolo richiede la versione 2,0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

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
