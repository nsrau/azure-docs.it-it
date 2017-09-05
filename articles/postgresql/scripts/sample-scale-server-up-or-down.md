---
title: Script dell'interfaccia della riga di comando - Scalare un database di Azure per PostgreSQL | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Scalare il database di Azure per il server PostgreSQL a un diverso livello di prestazioni dopo le query sulle metriche.
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 05/31/2017
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: b847abb336cce5dd5516469dca58002d3ba265f0
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorare e scalare un singolo server PostgreSQL tramite l'interfaccia della riga di comando di Azure
Questo esempio di script dell'interfaccia della riga di comando di Azure scala un singolo database di Azure per il server PostgreSQL a un diverso livello di prestazioni dopo le query sulle metriche. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per personalizzare nome utente e password amministratore. Sostituire l'ID sottoscrizione usato nei comandi az monitor con il proprio ID sottoscrizione. [!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL")] (Creare e ridimensionare un database di Azure per PostgreSQL).

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Eliminare il gruppo di risorse.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az postgres server create](/cli/azure/postgres/server#create) | Crea un server PostgreSQL che ospita i database. |
| [az monitor metrics list](/cli/azure/monitor/metrics#list) | Elencare il valore metrico per le risorse. |
| [az group delete](/cli/azure/group#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/overview)
- Provare a eseguire altri script: [esempi dell'interfaccia della riga di comando di Azure per il database di Azure per PostgreSQL](../sample-scripts-azure-cli.md)
- Altre informazioni sul ridimensionamento: [Livelli di servizio](../concepts-service-tiers.md) e [Unità di calcolo e unità di archiviazione](../concepts-compute-unit-and-storage.md)

