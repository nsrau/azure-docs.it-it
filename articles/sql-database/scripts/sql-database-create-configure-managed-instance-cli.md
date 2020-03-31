---
title: "Esempio di interfaccia della riga di comando: Creare un'istanza gestita nel database SQL di Azure"
description: Script di esempio dell'interfaccia della riga di comando di Azure per creare un'istanza gestita nel database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067437"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Usare l'interfaccia della riga di comando per creare un'istanza gestita di database SQL di Azure

Questo esempio di script dell'interfaccia della riga di comando di Azure crea un'istanza gestita di database SQL di Azure in una subnet dedicata all'interno di una nuova rete virtuale. Configura anche una tabella di route e un gruppo di sicurezza di rete per la rete virtuale. Al termine dell'esecuzione dello script, l'istanza gestita è accessibile dalla rete virtuale o da un ambiente locale. Vedere [Configurare una macchina virtuale di Azure per la connessione a un'istanza gestita di database SQL di Azure](../sql-database-managed-instance-configure-vm.md) e [Configurare una connessione da punto a sito a un'istanza gestita di database SQL di Azure da un computer locale](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Per le limitazioni, vedere le [aree supportate](../sql-database-managed-instance-resource-limits.md#supported-regions) e i [tipi di sottoscrizione](../sql-database-managed-instance-resource-limits.md#supported-subscription-types) supportati.

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

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Comandi per la rete virtuale. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Comandi per la subnet di rete virtuale. |
| [az network route-table](/cli/azure/network/route-table) | Comandi per le tabelle di route di rete. |
| [az sql mi](/cli/azure/sql/mi) | Comandi per le istanze gestite. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../sql-database-cli-samples.md).
