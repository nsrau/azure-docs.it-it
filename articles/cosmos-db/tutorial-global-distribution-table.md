---
title: Esercitazione sulla distribuzione globale in Azure Cosmos DB per l'API Tabella
description: Informazioni sul funzionamento della distribuzione globale negli account dell'API Tabella di Azure Cosmos DB e su come configurare l'elenco preferito di aree
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: e6cd574d1041908e91ad5e6629403c0e40d11c03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870361"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurare la distribuzione globale in Azure Cosmos DB usando l'API di tabella

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite l'[API Table](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Connessione a un'area preferita tramite l'API Table

Per sfruttare la [distribuzione globale](distribute-data-globally.md), le applicazioni client possono specificare un elenco di aree, nell'ordine preferito, da usare per eseguire operazioni sui documenti. Per ottenere questo risultato, impostare la proprietà [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet). Azure Cosmos DB Table API SDK seleziona l'endpoint che garantisce la comunicazione più agevole in base alla configurazione degli account, alla disponibilità di aree corrente e all'elenco delle preferenze specificato.

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
> * Configurare la distribuzione globale tramite le API Table di Azure Cosmos DB

