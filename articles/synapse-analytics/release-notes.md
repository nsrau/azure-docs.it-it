---
title: 'Note sulla versione: Azure Synapse Analytics (anteprima delle aree di lavoro)'
description: Note sulla versione di Azure Synapse Analytics (anteprima delle aree di lavoro)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031671"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Note sulla versione di Azure Synapse Analytics (anteprima delle aree di lavoro)

Questo articolo descrive le limitazioni e i problemi relativi ad Azure Synapse Analytics (aree di lavoro). Per informazioni correlate, vedere [Che cos'è Azure Synapse Analytics (aree di lavoro)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

- Problema e impatto per i clienti: Le aree di lavoro create con l'SDK non possono avviare Synapse Studio

- Soluzione alternativa: Completare i passaggi seguenti: 
  1.    Creare un'area di lavoro eseguendo `az synapse workspace create`.
  2.    Estrarre l'ID identità gestita eseguendo `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Aggiungere l'area di lavoro come ruolo all'account di archiviazione eseguendo ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`.
  4.    Aggiungere una regola del firewall eseguendo ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure Synapse](overview-what-is.md)
* [Introduzione](get-started.md)
* [Domande frequenti](overview-faq.md)
