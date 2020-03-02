---
title: Esempi dell'interfaccia della riga di comando di Azure per l'API MongoDB di Azure Cosmos DB
description: Esempi dell'interfaccia della riga di comando di Azure per l'API MongoDB di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524564"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Esempi dell'interfaccia della riga di comando di Azure per l'API MongoDB di Azure Cosmos DB

La tabella seguente include collegamenti a esempi di script dell'interfaccia della riga di comando di Azure per l'API MongoDB di Azure Cosmos DB. Per tutti i comandi dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili pagine di riferimento in [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](/cli/azure/cosmosdb). Tutti gli esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nel [repository GitHub dell'interfaccia della riga di comando di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Attualmente è possibile creare solo la versione 3.2 (account che usano l'endpoint nel formato `*.documents.azure.com`) degli account API di Azure Cosmos DB per MongoDB usando PowerShell, l'interfaccia della riga di comando e i modelli di Resource Manager. Per creare la versione 3.6 degli account, è necessario usare il portale di Azure.

| |  |
|---|---|
| [Creare un account, un database e una raccolta di Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account, un database e una raccolta di Azure Cosmos DB per l'API MongoDB. |
| [Cambiare il numero di unità elaborate](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Aggiornare il valore di UR/s in un database e una raccolta.|
| [Aggiungere aree o eseguire il failover](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Aggiungere un'area, cambiare la priorità di failover, attivare un failover manuale.|
| [Chiavi dell'account e stringhe di connessione](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Elencare chiavi dell'account, chiavi di sola lettura e stringhe di connessione, rigenerare le chiavi.|
| [Proteggere un account con il firewall IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account Cosmos con il firewall IP configurato.|
| [Proteggere un nuovo account con gli endpoint di servizio](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Creare un account Cosmos e proteggerlo con gli endpoint di servizio.|
| [Proteggere un account esistente con gli endpoint di servizio](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aggiornare un account Cosmos per proteggerlo con gli endpoint di servizio quando alla fine viene configurata la subnet.|
|||
