---
title: Esercitazione sulla distribuzione globale in Azure Cosmos DB per l'API Graph | Documentazione Microsoft
description: Informazioni su come configurare la distribuzione globale in Azure Cosmos DB mediante l'API Graph.
services: cosmos-db
keywords: distribuzione globale, graph, gremlin
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 1806bde383f04747f1f0fef46e5cf4d38de1e939
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Procedura di configurazione della distribuzione globale in Azure Cosmos DB mediante l'API Graph

In questo articolo è illustrato come utilizzare il portale di Azure per la distribuzione globale di Azure Cosmos DB del programma di installazione e quindi connettersi usando l'API Graph.

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurazione distribuzione globale mediante il [sulle API Graph](graph-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Connessione a un'area preferita tramite l'API Graph con .NET SDK

L'API Graph viene esposto come una libreria di estensioni sopra l'API di SQL.

Per sfruttare la [distribuzione globale](distribute-data-globally.md), le applicazioni client possono specificare un elenco di aree, nell'ordine preferito, da usare per eseguire operazioni sui documenti. Questa operazione può essere eseguita impostando il criterio di connessione. A seconda della configurazione dell'account Azure Cosmos DB, della disponibilità corrente delle aree e dell'elenco delle preferenze specificato, l'SDK sceglierà l'endpoint ottimale per eseguire le operazioni di scrittura e lettura.

L'elenco delle preferenze viene specificato nella fase di inizializzazione di una connessione usando gli SDK. Gli SDK accettano il parametro facoltativo "PreferredLocations", ovvero un elenco ordinato di aree di Azure.

* **Scrive**: il SDK invia automaticamente tutte le scritture all'area di scrittura corrente.
* **Legge**: tutte le letture vengono inviate per la prima area disponibile nell'elenco PreferredLocations. Se la richiesta non riesce, il client non riesce in basso nell'elenco per l'area successiva e così via. il SDK di tenta di leggere le aree specificate nella PreferredLocations. In tal caso, ad esempio, se l'account di database Cosmos è disponibile in tre aree, ma il client specifica solo due delle aree di scrittura per PreferredLocations, quindi non letture sono servite all'esterno dell'area di scrittura, anche in caso di failover.

L'applicazione può verificare l'endpoint di scrittura e lettura corrente scelto dall'SDK controllando due proprietà, WriteEndpoint e ReadEndpoint, disponibili nella versione dell'SDK 1.8 e nelle versioni successive. Se la proprietà PreferredLocations non è impostata, tutte le richieste vengono gestite dall'area di scrittura corrente.

### <a name="using-the-sdk"></a>Uso dell'SDK

Ad esempio, in .NET SDK il parametro `ConnectionPolicy` per il costruttore `DocumentClient` dispone di una proprietà denominata `PreferredLocations`. È possibile impostare questa proprietà su un elenco di nomi di aree. I nomi visualizzati per le [aree di Azure][regions] possono essere specificati come parte di `PreferredLocations`.

> [!NOTE]
> Gli URL per gli endpoint non devono essere considerati come costanti di lunga durata. Il servizio può aggiornare gli URL in qualsiasi momento. L'SDK gestisce la modifica in modo automatico.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

L'esercitazione è terminata. Per informazioni su come gestire la coerenza dell'account con replica globale, vedere [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md). Per informazioni sul funzionamento della replica di database globale in Azure Cosmos DB, vedere [Distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale mediante le API di SQL

È ora possibile passare all'esercitazione successiva per imparare a sviluppare in locale usando l'emulatore locale di Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Sviluppare in locale con l'emulatore](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

