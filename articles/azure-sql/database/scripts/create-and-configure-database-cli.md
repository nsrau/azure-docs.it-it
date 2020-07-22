---
title: 'Interfaccia della riga di comando di Azure: Creare un database singolo'
description: Usare questo esempio di script dell'interfaccia della riga di comando di Azure per creare un database singolo.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: cb8850adaf638aca3ed69c3939916e132f66062d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504564"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Usare l'interfaccia della riga di comando di Azure per creare un database singolo e configurare una regola del firewall

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Questo esempio di script dell'interfaccia della riga di comando di Azure crea un database singolo nel database SQL di Azure e configura una regola del firewall a livello di server. Dopo aver eseguito correttamente lo script, è possibile accedere al database da tutti i servizi di Azure e dall'indirizzo IP configurato.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Descrizione |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Comandi per il server |
| [az sql server firewall](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Comandi per il firewall del server. |
| [az sql db](/cli/azure/sql/db#az-sql-db-create) | Comandi per il database. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../az-cli-script-samples-content-guide.md).
