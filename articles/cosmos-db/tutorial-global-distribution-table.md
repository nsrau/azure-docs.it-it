---
title: Esercitazione sulla distribuzione globale in Azure Cosmos DB per l'API Tabella
description: Informazioni su come configurare la distribuzione globale in Azure Cosmos DB usando l'API Tabella.
services: cosmos-db
keywords: Distribuzione globale, Tabella
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/13/2017
ms.openlocfilehash: 92ae0c3dc07c6dada80e081c65484e4f6bf4f947
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874873"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurare la distribuzione globale in Azure Cosmos DB usando l'API Tabella

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite l'[API Tabella](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Connessione a un'area preferita tramite l'API Tabella

Per sfruttare la [distribuzione globale](distribute-data-globally.md), le applicazioni client possono specificare un elenco di aree, nell'ordine preferito, da usare per eseguire operazioni sui documenti. Per ottenere questo risultato, impostare la proprietà [TableConnectionPolicy.PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.table.tableconnectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_CosmosDB_Table_TableConnectionPolicy_PreferredLocations). Azure Cosmos DB Table API SDK seleziona l'endpoint che garantisce la comunicazione più agevole in base alla configurazione degli account, alla disponibilità di aree corrente e all'elenco delle preferenze specificato.

PreferredLocations deve contenere un elenco delimitato da virgole di percorsi preferiti (multihoming) per le letture. Ogni istanza del client può specificare un sottoinsieme di queste aree nell'ordine preferito per le letture a bassa latenza. Le aree devono essere denominate usando i [nomi visualizzati](https://msdn.microsoft.com/library/azure/gg441293.aspx), ad esempio, `West US`.

Tutte le letture vengono inviate alla prima area disponibile nell'elenco PreferredLocations. Se la richiesta ha esito negativo, il client trasferisce l'elenco all'area successiva e così via.

L'SDK prova a leggere dalle aree specificate nell'elenco PreferredLocations. Quindi se l'Account di database è disponibile ad esempio in tre aree, ma il client specifica solo due delle aree di non scrittura per PreferredLocations, le letture non verranno distribuite fuori dall'area di scrittura, anche in caso di failover.

L'SDK invia automaticamente tutte le scritture all'area di scrittura corrente.

Se la proprietà PreferredLocations non è impostata, tutte le richieste verranno distribuite dall'area di scrittura corrente.

L'esercitazione è terminata. Per informazioni su come gestire la coerenza dell'account con replica globale, vedere [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md). Per informazioni sul funzionamento della replica di database globale in Azure Cosmos DB, vedere [Distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite le API Tabella di Azure Cosmos DB

