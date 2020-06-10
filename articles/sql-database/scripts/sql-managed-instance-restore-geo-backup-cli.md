---
title: 'Esempio di interfaccia della riga di comando: Ripristinare un backup geografico - Database SQL di Azure'
description: Script di esempio dell'interfaccia della riga di comando di Azure per ripristinare un database SQL di Azure in Istanza gestita da un backup con ridondanza geografica.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 07/03/2019
ms.openlocfilehash: c6ae8d79ee40729f47a906561d6c8ae586765f5d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84019844"
---
# <a name="use-cli-to-restore-a-managed-instance-database-to-another-geo-region"></a>Usare l'interfaccia della riga di comando per ripristinare un database in Istanza gestita in un'altra area geografica

Questo esempio di script dell'interfaccia della riga di comando di Azure ripristina un database SQL di Azure in Istanza gestita da un'area geografica remota (ripristino geografico).  

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="prerequisites"></a>Prerequisiti

Una coppia esistente di istanze gestite. Vedere [Usare l'interfaccia della riga di comando di Azure per creare un'istanza di Istanza gestita di SQL di Azure](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Eseguire lo script

```azurepowershell-interactive
#!/bin/bash
$instance = "<instanceId>" # add instance here
$targetInstance = "<targetInstanceId>" # add target instance here
$resource = "<resourceId>" # add resource here

$randomIdentifier = $(Get-Random)
$managedDatabase = "managedDatabase-$randomIdentifier"

echo "Creating $($managedDatabase) on $($instance)..."
az sql midb create -g $resource --mi $instance -n $managedDatabase

echo "Restoring $($managedDatabase) to $($targetInstance)..."
az sql midb restore -g $resource --mi $instance -n $managedDatabase --dest-name $targetInstance --time "2018-05-20T05:34:22"
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| | |
|---|---|
| [az sql midb](/cli/azure/sql/midb) | Comandi per i database in istanza gestita. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
