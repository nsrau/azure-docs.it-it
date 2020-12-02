---
title: 'Risoluzione dei problemi: le aree di lavoro create da SDK non possono avviare sinapsi Studio'
description: Procedura per risolvere le aree di lavoro create da SDK non è in grado di avviare sinapsi Studio
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466431"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Risolvere i problemi relativi alle aree di lavoro di Azure sinapsi Analytics create con SDK

Questo articolo illustra i passaggi per la risoluzione dei problemi per avviare sinapsi studio da un'area di lavoro sinapsi creata con un Software Development Kit (SDK).


## <a name="prerequisites"></a>Prerequisiti

- Area di lavoro sinapsi creata con SDK

## <a name="workaround"></a>Soluzione alternativa

Per avviare sinapsi studio dall'area di lavoro creata da SDK, attenersi alla procedura seguente: 
  1.    Creare un'area di lavoro eseguendo `az synapse workspace create`.
  2.    Estrarre l'ID identità gestita eseguendo `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Concedere il ruolo Collaboratore dati BLOB di archiviazione all'account di archiviazione identità gestita eseguendo ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Aggiungere una regola del firewall eseguendo ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure Synapse](../overview-what-is.md)
* [Introduzione](../get-started.md)
