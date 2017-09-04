---
title: Esercitazione sulla distribuzione globale in Azure Cosmos DB per l'API Table | Documentazione Microsoft
description: Informazioni su come configurare la distribuzione globale in Azure Cosmos DB usando l'API Table.
services: cosmos-db
keywords: Distribuzione globale, Table
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 63c9e530a4982e2e6e478fea56e015fc77851e1d
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Procedura di configurazione della distribuzione globale in Azure Cosmos DB usando l'API Table

Questo articolo illustra come usare il portale di Azure per configurare la distribuzione globale in Azure Cosmos DB e quindi connettersi tramite l'API Table (anteprima).

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite l'[API Table](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Connessione a un'area preferita tramite l'API Table

Per sfruttare la [distribuzione globale](distribute-data-globally.md), le applicazioni client possono specificare un elenco di aree, nell'ordine preferito, da usare per eseguire operazioni sui documenti. Questa operazione può essere eseguita impostando il valore di configurazione `TablePreferredLocations` nella configurazione dell'applicazione per l'anteprima di Azure Storage SDK. A seconda della configurazione dell'account Azure Cosmos DB, della disponibilità corrente delle aree e dell'elenco delle preferenze specificato, l'SDK sceglierà l'endpoint ottimale per eseguire le operazioni di scrittura e lettura.

`TablePreferredLocations` deve contenere un elenco delimitato da virgole di percorsi preferiti (multihoming) per le letture. Ogni istanza del client può specificare un sottoinsieme di queste aree nell'ordine preferito per le letture a bassa latenza. Le aree devono essere denominate usando i [nomi visualizzati](https://msdn.microsoft.com/library/azure/gg441293.aspx), ad esempio, `West US`.

Tutte le letture verranno inviate alla prima area disponibile nell'elenco `TablePreferredLocations`. Se la richiesta ha esito negativo, il client trasferisce l'elenco all'area successiva e così via.

Gli SDK tenteranno solo di leggere dalle aree specificate nell'elenco `TablePreferredLocations`. Quindi se l'account di database è disponibile ad esempio in tre aree, ma il client specifica solo due delle aree di non scrittura per `TablePreferredLocations`, le letture non verranno distribuite fuori dall'area di scrittura, anche in caso di failover.

L'SDK invia automaticamente tutte le scritture all'area di scrittura corrente.

Se la proprietà `TablePreferredLocations` non è impostata, tutte le richieste verranno distribuite dall'area di scrittura corrente.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

L'esercitazione è terminata. Per informazioni su come gestire la coerenza dell'account con replica globale, vedere [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md). Per informazioni sul funzionamento della replica di database globale in Azure Cosmos DB, vedere [Distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite le API di DocumentDB

È ora possibile passare all'esercitazione successiva per imparare a sviluppare in locale usando l'emulatore locale di Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Sviluppare in locale con l'emulatore](local-emulator.md)
