---
title: Script dell&quot;interfaccia della riga di comando di Azure -Creare un database SQL | Documentazione Microsoft
description: 'Esempio di script dell&quot;interfaccia della riga di comando di Azure: creare un database SQL usando l&quot;interfaccia della riga di comando di Azure'
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 4a7343ebc5090dc82f6c7b470c697167536315ca
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-single-sql-database-and-configure-a-firewall-rule-using-the-azure-cli"></a>Creare un singolo database SQL e configurare una regola del firewall tramite l'interfaccia della riga di comando di Azure

Questo script dell'interfaccia della riga di comando di esempio crea un database SQL di Azure e configura una regola del firewall a livello di server. Dopo aver eseguito correttamente lo script, è possibile accedere al database SQL da tutti i servizi di Azure e dall'indirizzo IP configurato. 

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il comando `az login`. 

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure in Windows, vedere [Running the Azure CLI in Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Creare database SQL")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az sql server create](/cli/azure/sql/server#create) | Consente di creare un server logico che ospita il database SQL. |
| [az sql server firewall create](/cli/azure/sql/server/firewall#create) | Consente di creare una regola del firewall per consentire l'accesso a tutti i database SQL presenti sul server dall'intervallo di indirizzi IP immesso. |
| [az sql db create](/cli/azure/sql/db#create) | Consente di creare il database SQL nel server logico. |
| [az group delete](/cli/azure/resource#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../sql-database-cli-samples.md).


