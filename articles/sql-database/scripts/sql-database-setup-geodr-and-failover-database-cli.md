---
title: 'Esempio di interfaccia della riga di comando: Configurare la replica geografica attiva per un singolo database nel database SQL di Azure'
description: Esempio di script dell'interfaccia della riga di comando di Azure per configurare la replica geografica attiva per un singolo database nel database SQL di Azure ed eseguirne il failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 93afb5b52df7ac3ff638ff5fc180c3c4774880b8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020388"
---
# <a name="use-cli-to-configure-active-geo-replication-for-a-single-database-in-azure-sql-database"></a>Usare l'interfaccia della riga di comando per configurare la replica geografica attiva per un singolo database nel database SQL di Azure

Questo esempio di script dell'interfaccia della riga di comando di Azure configura la replica geografica attiva per un singolo database e ne esegue il failover in una replica secondaria del database.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-single-database.sh "Set up active geo-replication for single database")]

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| | |
|---|---|
| [az sql db replica](/cli/azure/sql/db/replica) | Comandi per la replica di database. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).