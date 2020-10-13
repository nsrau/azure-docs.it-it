---
title: Esempio di interfaccia della riga di comando-Abilita BYOK Transparent Data Encryption-Istanza gestita Azure
description: Informazioni su come configurare un'istanza gestita di SQL di Azure per iniziare a usare Transparent Data Encryption (TDE) per BYOK per la crittografia dei dati inattivi tramite PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto
ms.date: 11/05/2019
ms.openlocfilehash: 2b948161633569d629dfb048a7d7dee6a9946f43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323688"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gestire Transparent Data Encryption in un'istanza gestita usando una chiave personalizzata di Azure Key Vault

Questo esempio di script dell'interfaccia della riga di comando di Azure configura Transparent Data Encryption (Transparent Data Encryption) con chiave gestita dal cliente per Istanza gestita SQL di Azure, usando una chiave da Azure Key Vault. Questo scenario viene spesso definito Bring Your Own Key scenario per Transparent Data Encryption. Per altre informazioni su Transparent Data Encryption con chiave gestita dal cliente, vedere Transparent Data [encryption Bring your own key to Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md).

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="prerequisites"></a>Prerequisiti

Per un Istanza gestita esistente, vedere usare l'interfaccia della riga [di comando di Azure per creare un istanza gestita SQL di Azure](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Description |
|---|---|
| [az sql db](/cli/azure/sql/db) | Comandi per il database. |
| [az sql failover-group](/cli/azure/sql/failover-group) | Comandi per il gruppo di failover. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
