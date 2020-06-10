---
title: "Interfaccia della riga di comando di Azure: Aggiungere l'istanza gestita a un gruppo di failover"
description: Script di esempio dell'interfaccia della riga di comando di Azure per creare un istanza di Istanza gestita di SQL di Azure, aggiungerla a un gruppo di failover e testare il failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 1f6c178c1f8813300d3473ad3b788ecd7e7b573e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84115388"
---
# <a name="use-cli-to-add-an-azure-sql-managed-instance-to-a-failover-group"></a>Usare l'interfaccia della riga di comando per aggiungere un'istanza di Istanza gestita di SQL di Azure a un gruppo di failover

Questo esempio di interfaccia della riga di comando di Azure crea due istanze gestite, le aggiunge a un gruppo di failover e quindi testa il failover dall'istanza gestita primaria all'istanza gestita secondaria.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Script di esempio

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate. Sarà necessario rimuovere due volte il gruppo di risorse. Quando si rimuove il gruppo di risorse la prima volta, verranno rimossi l'istanza gestita e i cluster virtuali, ma l'operazione non verrà completata e verrà visualizzato il messaggio di errore `az group delete : Long running operation failed with status 'Conflict'.`. Eseguire una seconda volta il comando az group delete per rimuovere tutte le risorse residue e il gruppo di risorse.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Comandi per la rete virtuale.  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Comandi per la subnet di rete virtuale. |
| [az network nsg](/cli/azure/network/nsg) | Comandi per il gruppo di sicurezza di rete. |
| [az network nsg rule](/cli/azure/network/nsg/rule)| Comandi per le regole di sicurezza di rete. |
| [az network route-table](/cli/azure/network/route-table) | Comandi per le tabelle di route. |
| [az sql mi](/cli/azure/sql/mi) | Comandi per Istanza gestita di SQL. |
| [az network public-ip](/cli/azure/network/public-ip) | Comandi per gli indirizzi IP pubblici di rete. |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Comandi per il gateway di rete virtuale |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | Comandi per il gruppo di failover di Istanza gestita di SQL. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
