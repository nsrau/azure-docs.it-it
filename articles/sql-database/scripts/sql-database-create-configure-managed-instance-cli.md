---
title: "Interfaccia della riga di comando di Azure: Creare un'istanza gestita"
description: Script di esempio dell'interfaccia della riga di comando di Azure per creare un'istanza gestita in Istanza gestita di SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 9d0058fd90590b10a0d5745bce1875db9ad35745
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518895"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Usare l'interfaccia della riga di comando per creare un'istanza in Istanza gestita di SQL di Azure

Questo esempio di script dell'interfaccia della riga di comando di Azure crea un'istanza in Istanza gestita di SQL di Azure in una subnet dedicata con una nuova rete virtuale. Configura anche una tabella di route e un gruppo di sicurezza di rete per la rete virtuale. Al termine dell'esecuzione dello script, l'istanza gestita è accessibile dalla rete virtuale o da un ambiente locale. Vedere [Configurare una macchina virtuale di Azure per la connessione a Istanza gestita di SQL di Azure]../../azure-sql/managed-instance/connect-vm-instance-configure.md) e [Configurare una connessione da punto a sito a Istanza gestita di SQL di Azure da un computer locale](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Per le limitazioni, vedere le [aree supportate](../../azure-sql/managed-instance/resource-limits.md#supported-regions) e i [tipi di sottoscrizione](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types) supportati.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Descrizione |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Comandi per la rete virtuale. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Comandi per la subnet di rete virtuale. |
| [az network route-table](/cli/azure/network/route-table) | Comandi per le tabelle di route di rete. |
| [az sql mi](/cli/azure/sql/mi) | Comandi per Istanza gestita di SQL. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
