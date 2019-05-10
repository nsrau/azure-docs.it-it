---
title: Esempi di Azure PowerShell per Azure Cosmos DB
description: Esempi di Azure PowerShell - Script per creare e gestire gli account di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069293"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Esempi di Azure PowerShell per Azure Cosmos DB

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per Azure Cosmos DB per l'API Core (SQL).

| |  |
|---|---|
|**Account Azure Cosmos**||
|[Creare un account](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un account Azure Cosmos (API SQL). |
|[Ottenere un account](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le proprietà di un account Azure Cosmos. |
|[Aggiungere un'area](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere un account Azure Cosmos e aggiungere un'area all'elenco di posizioni. |
|[Modificare la priorità di failover](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Modificare la priorità di failover di un account Azure Cosmos con un trigger di failover manuale. |
|[Aggiornare i tag](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare i tag per un account Azure Cosmos. |
|[Ottenere le chiavi dell'account](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le chiavi primarie e secondarie di un account Azure Cosmos. |
|[Rigenerare le chiavi dell'account](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Riscrivere le chiavi primarie e secondarie di un account Azure Cosmos. |
|[Elencare le stringhe di connessione](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le stringhe di connessione primarie e secondarie di un account Azure Cosmos. |
|[Creare un Firewall IP](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un Firewall IP per un account Azure Cosmos. |
|[Eliminare un account Azure Cosmos](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Eliminare un account Azure Cosmos. |
|**Database Azure Cosmos**||
| [Creare un database](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un database all'interno di un account Azure Cosmos.|
| [Creare un database con velocità effettiva condivisa e a livello di database](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un database Azure Cosmos con una velocità effettiva a livello di database condivisa con i propri contenitori.|
| [Elencare tutti i database](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elencare tutti i database in un account Azure Cosmos.|
| [Ottenere un database](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ottenere le proprietà di un database Azure Cosmos.|
|**Contenitori Azure Cosmos**||
| [Creare un contenitore](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un contenitore Azure Cosmos con velocità effettiva dedicata.|
| [Creare un contenitore con velocità effettiva condivisa](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un contenitore Azure Cosmos con velocità effettiva condivisa con altri contenitori nel database.|
| [Creare un contenitore con criterio di indicizzazione](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un contenitore Azure Cosmos con un criterio di indicizzazione personalizzato.|
| [Creare un contenitore senza criterio di indicizzazione](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un contenitore Azure Cosmos con il criterio di indicizzazione disattivato.|
| [Creare un contenitore con chiavi e durata TTL univoche](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un contenitore Azure Cosmos con configurati una durata (TTL) e un vincolo di chiave univoci.|
| [Creare un contenitore con la risoluzione dei conflitti](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Creare un contenitore Azure Cosmos con criterio di risoluzione dei conflitti in cui prevale l'ultima scrittura.|
| [Elencare tutti i contenitori](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elencare tutti i contenitori in un database Azure Cosmos.|
| [Ottenere un contenitore](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ottenere le proprietà di un contenitore in un database Azure Cosmos.|
| [Eliminare un contenitore](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Eliminare un contenitore in un database Azure Cosmos.|
|||