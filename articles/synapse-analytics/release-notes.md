---
title: Note sulla versione
description: Note sulla versione di Azure Synapse Analytics (anteprima per aree di lavoro)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419805"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Note sulla versione di Azure Synapse Analytics (anteprima)

Questo articolo descrive le limitazioni e i problemi relativi ad Azure Synapse Analytics (aree di lavoro). Per informazioni correlate, vedere [Che cos'è Azure Synapse Analytics (aree di lavoro)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (aree di lavoro) 

### <a name="azure-synapse-cli"></a>Interfaccia della riga di comando di Azure Synapse

- Problema e impatto per i clienti: Le aree di lavoro create con l'SDK non possono avviare Synapse Studio

- Soluzione alternativa: Completare i passaggi seguenti: 
  1.    Creare un'area di lavoro eseguendo `az synapse workspace create`2.    Estrarre l'ID identità gestita eseguendo `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`
  3.    Aggiungere l'area di lavoro come ruolo all'account di archiviazione eseguendo ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`
  4.    Aggiungere una regola del firewall eseguendo ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare Synapse Studio](quickstart-synapse-studio.md)
* [Creare un pool SQL](quickstart-create-sql-pool.md)
* [Usare SQL su richiesta](quickstart-sql-on-demand.md)
* [Creare un pool di Apache Spark](quickstart-create-apache-spark-pool.md)