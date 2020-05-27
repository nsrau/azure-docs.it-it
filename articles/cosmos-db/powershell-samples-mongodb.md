---
title: Esempi di Azure PowerShell per Azure Cosmos DB - API MongoDB
description: Ottenere gli esempi di Azure PowerShell per eseguire varie attività comuni negli account dell'API di Azure Cosmos DB per MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: c8e0a7a60a3512d19a1dfdfdb07b20e523ce7b92
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649706"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Esempi di Azure PowerShell per l'API MongoDB di Azure Cosmos DB

La tabella seguente contiene collegamenti a script di Azure PowerShell di esempio per Azure Cosmos DB relativamente all'API MongoDB.

> [!NOTE]
> Attualmente è possibile creare solo la versione 3.2 (account che usano l'endpoint nel formato `*.documents.azure.com`) degli account API di Azure Cosmos DB per MongoDB usando PowerShell, l'interfaccia della riga di comando e i modelli di Resource Manager. Per creare la versione 3.6 degli account, è necessario usare il portale di Azure.

> [!NOTE]
> Gli esempi usano i cmdlet di gestione di [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Verificare regolarmente se sono disponibili aggiornamenti per `Az.CosmosDB`.

| | |
|---|---|
|[Creare un account, un database e una raccolta](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account, un database e una raccolta Azure Cosmos DB. |
|[Elencare o ottenere database o raccolte](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elencare o ottenere un database o una raccolta. |
|[Ottenere le unità richiesta al secondo](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le unità richiesta al secondo per un database o una raccolta. |
|[Aggiornare le unità richiesta al secondo](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare le unità richiesta al secondo per un database o una raccolta. |
|[Aggiornare un account o aggiungere un'area](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiungere un'area in un account Cosmos. Si può anche usare per apportare modifiche ad altre proprietà dell'account, ma devono essere separate da quelle apportate alle aree. |
|[Cambiare la priorità di failover o attivare un failover](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cambiare la priorità di failover a livello di area di un account Azure Cosmos o attivare un failover manuale. |
|[Chiavi dell'account o stringhe di connessione](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le chiavi primarie e secondarie e le stringhe di connessione oppure rigenerare una chiave di un account Azure Cosmos. |
|[Creare un account Cosmos con il firewall IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account Azure Cosmos con il firewall IP abilitato. |
|||
