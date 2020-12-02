---
title: "Avvio rapido: Creare un pool SQL Synapse con l'interfaccia della riga di comando di Azure"
description: Creare rapidamente un pool SQL Synapse con una regola del firewall a livello di server usando l'interfaccia della riga di comando di Azure.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: Kevin
ms.custom: azure-synapse
ms.openlocfilehash: 59195bba69bb343e55cfcb7342400d93dcce60c0
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932958"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Avvio rapido: Creare un pool SQL Synapse con l'interfaccia della riga di comando di Azure

Creare un pool SQL Synapse (data warehouse) in Azure Synapse Analytics usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Guida introduttiva

Usare questi comandi per accedere ad Azure e configurare un gruppo di risorse.

1. Se si usa un'installazione locale, eseguire il comando [az login](/cli/azure/reference-index#az_login) per accedere ad Azure:

   ```azurecli
   az login
   ```

1. Se necessario, usare il comando [az account set](/cli/azure/account#az_account_set) per selezionare la sottoscrizione:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Eseguire il comando [az group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Creare un [server SQL logico](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) con il comando [az sql server create](/cli/azure/sql/server#az_sql_server_create):

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   Un server contiene un gruppo di database gestiti come gruppo.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Creare una [regola del firewall a livello di server](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio SQL Server Management Studio o l'utilità SQLCMD, di connettersi a un pool SQL tramite il firewall del servizio pool SQL.

Eseguire il comando [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) per creare una regola del firewall:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

In questo esempio il firewall viene aperto solo per altre risorse di Azure. Per abilitare la connettività esterna, modificare l'indirizzo IP in un indirizzo appropriato per l'ambiente. Per aprire tutti gli indirizzi IP, usare 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo finale.

> [!NOTE]
> Gli endpoint SQL comunicano sulla porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server a meno che il reparto IT non apra la porta 1433.
>

## <a name="create-and-manage-your-sql-pool"></a>Creare e gestire il pool SQL

Creare il pool SQL. Come obiettivo del servizio questo esempio usa DW100c, un punto iniziale a basso costo per il pool SQL.

> [!NOTE]
> È necessaria un'area di lavoro creata in precedenza. Per altre informazioni, vedere [Avvio rapido: Creare un'area di lavoro di Azure Synapse con l'interfaccia della riga di comando di Azure](../quickstart-create-workspace-cli.md).

Usare il comando [az synapse sql pool create](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) per creare il pool SQL:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Per altre informazioni sulle opzioni dei parametri, vedere [az synapse sql pool](/cli/azure/ext/synapse/synapse/sql/pool).

Per vedere i pool SQL, usare il comando [az synapse sql pool list](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list):

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Usare il comando [az synapse sql pool update](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) per aggiornare un pool esistente:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Usare il comando [az synapse sql pool pause](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) per sospendere il pool:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Usare il comando [az synapse sql pool resume](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) per avviare un pool sospeso:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Per rimuovere un pool SQL esistente, usare il comando [az synapse sql pool delete](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete):

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Pulire le risorse

Altre esercitazioni introduttive della raccolta si basano su questa guida introduttiva.

> [!TIP]
> Se si prevede di continuare con le esercitazioni introduttive successive, non eseguire la pulizia delle risorse create in questo avvio rapido. Se non si prevede di continuare, usare il comando [az group delete](/cli/azure/group#az_group_delete) per eliminare tutte le risorse create in questa guida di avvio rapido.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Sono stati creati un pool SQL e una regola del firewall ed è stata stabilita una connessione al pool SQL. Per altre informazioni, continuare con l'articolo [Caricare i dati in un pool SQL](load-data-from-azure-blob-storage-using-polybase.md).
