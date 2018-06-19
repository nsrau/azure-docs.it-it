---
title: Esempi di Azure PowerShell per Azure Cosmos DB | Microsoft Docs
description: Esempi di Azure PowerShell - Script per creare e gestire gli account di Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 71e75b4b2bb60ad2c1ae1a468991fe1f5520136c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700367"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Esempi di Azure PowerShell per Azure Cosmos DB

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per Azure Cosmos DB. Al momento, è possibile gestire l'account Azure Cosmos DB tramite PowerShell, mentre altre risorse, come i database e le raccolte, non possono essere gestite tramite PowerShell.

| |  |
|---|---|
|**Creare un account di Azure Cosmos DB**||
|[Creare un account API SQL](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un singolo account API di Azure Cosmos DB da usare con l'API SQL. |
|[Creare un account di API MongoDB](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un singolo account di Azure Cosmos DB da usare con l'API MongoDB. |
|[Creare un account API Gremlin](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un singolo account di Azure Cosmos DB da usare con l'API Gremlin. |
|**Scalare Azure Cosmos DB**||
|[Replicare l'account di Azure Cosmos DB in più aree e configurare le priorità di failover](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Replica a livello globale i dati dell'account in più aree con una priorità di failover specificata.|
|**Proteggere Azure Cosmos DB**||
| [Ottenere le chiavi dell'account](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ottiene le chiavi di scrittura master primarie e secondarie e le chiavi di sola lettura primarie e secondarie per l'account.|
| [Ottenere la stringa di connessione di MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ottiene la stringa di connessione per connettere l'app MongoDB all'account di Azure Cosmos DB.|
|[Rigenerare le chiavi dell'account](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Rigenera la chiave master o di sola lettura per l'account.|
|[Creare un firewall](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un criterio di controllo di accesso IP in ingresso per limitare l'accesso all'account da un set di macchine e/o servizi cloud approvati.|
|**Disponibilità elevata, ripristino di emergenza, backup e ripristino**||
|[Configurare i criteri di failover](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Imposta la priorità di failover di ogni area in cui l'account viene replicato.|
|||
