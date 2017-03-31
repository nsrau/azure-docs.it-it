---
title: 'Interfaccia della riga di comando di Azure: creare ed effettuare una query su un singolo database SQL | Microsoft Docs'
description: Come creare un server logico di database SQL, una regola del firewall a livello di server e un database usando l&quot;interfaccia della riga di comando di Azure.
keywords: esercitazione sul database sql, creare un database sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8322e46f462b6c940f9808411d99aa1cee0beea5
ms.lasthandoff: 03/15/2017

---

# <a name="create-and-query-a-single-azure-sql-database-with-the-azure-cli"></a>Creare ed effettuare una query su un singolo database SQL di Azure con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida illustra in dettaglio l'uso dell'interfaccia della riga di comando di Azure per distribuire un database SQL di Azure.

Prima di iniziare, verificare che l'interfaccia della riga di comando di Azure sia stata installata. Per altre informazioni, vedere [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli) (Guida all'installazione dell'interfaccia della riga di comando di Azure). 

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Creare un server logico

Creare un server logico con il comando [az sql server create](/cli/azure/sql/server#create). L'esempio seguente crea un server con un nome casuale nel gruppo di risorse con un account di accesso amministratore denominato `ServerAdmin` e la password `ChangeYourAdminPassword1`. Sostituire questi valori predefiniti con quelli desiderati.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Configurare una regola del firewall del server

Creare una regola del firewall a livello di server con il comando [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio SQL Server Management Studio o l'utility SQLCMD, di connettersi a un database SQL tramite il firewall del servizio di database SQL. L'esempio seguente crea una regola del firewall per un intervallo di indirizzi predefiniti, che in questo esempio corrisponde all'intero intervallo possibile di indirizzi IP. Sostituire i valori predefiniti con i valori dell'intervallo di indirizzi IP esterni o di indirizzi IP. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>Creare un database nel server

Creare un database nel server con il comando [az sql db create](/cli/azure/sql/db#create). L'esempio seguente crea un database vuoto denominato `mySampleDatabase`. Sostituire questo valore predefinito con quello desiderato.

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create dalla presente guida introduttiva, eseguire questo comando:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- Per connettersi ed effettuare una query usando SQL Server Management Studio, vedere [Connettersi ed eseguire una query con SSMS](sql-database-connect-query-ssms.md)
- Per connettersi usando Visual Studio, vedere [Connettersi ed eseguire query con Visual Studio](sql-database-connect-query.md).
- Per una panoramica tecnica del database SQL, vedere [Informazioni sul servizio di database SQL](sql-database-technical-overview.md).

