---
title: Esercitazione sulla distribuzione globale in Azure Cosmos DB per l'API Tabella
description: Informazioni sul funzionamento della distribuzione globale negli account dell'API Tabella di Azure Cosmos DB e su come configurare l'elenco preferito di aree
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900190"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurare la distribuzione globale in Azure Cosmos DB usando l'API di tabella

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite l'[API Table](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Connessione a un'area preferita tramite l'API Table

Per sfruttare i vantaggi della [distribuzione globale](distribute-data-globally.md), le applicazioni client devono specificare la località corrente in cui è in esecuzione l'applicazione. Per eseguire questa operazione, è necessario impostare la proprietà `CosmosExecutorConfiguration.CurrentRegion`. La proprietà `CurrentRegion` deve contenere una sola località. Ogni istanza del client può indicare un'area specifica per le letture a bassa latenza. Le aree devono essere denominate usando i relativi [nomi visualizzati](https://msdn.microsoft.com/library/azure/gg441293.aspx) ad esempio "Stati Uniti occidentali". 

Azure Cosmos DB Table API SDK seleziona automaticamente l'endpoint che garantisce la comunicazione ottimale in base alla configurazione degli account e alla disponibilità corrente delle aree. Viene assegnata la priorità all'area più vicina per offrire ai client la latenza migliore. Dopo aver impostato la proprietà `CurrentRegion` corrente, le richieste di lettura e scrittura vengono indirizzate come indicato di seguito:

* **Richieste di lettura:** Tutte le richieste di lettura vengono inviate all'area `CurrentRegion` configurata. In base alla prossimità, l'SDK seleziona automaticamente un'area con replica geografica di fallback per la disponibilità elevata.

* **Richieste di scrittura:** L'SDK invia automaticamente tutte le richieste di scrittura all'area di scrittura corrente. In un account multimaster, l'area corrente gestirà anche le richieste di scrittura. In base alla prossimità, l'SDK seleziona automaticamente un'area con replica geografica di fallback per la disponibilità elevata.

Se non si specifica la proprietà `CurrentRegion`, l'SDK userà l'area di scrittura corrente per tutte le operazioni.

Ad esempio, se un account Azure Cosmos risiede nelle aree "Stati Uniti occidentali" e "Stati Uniti orientali". Se "Stati Uniti occidentali" è l'area di scrittura e l'applicazione risiede nell'area "Stati Uniti orientali". Se la proprietà CurrentRegion non è configurata, tutte le richieste di lettura e scrittura vengono sempre indirizzate all'area "Stati Uniti occidentali". Se la proprietà CurrentRegion è configurata, tutte le richieste di lettura e scrittura vengono gestite dall'area "Stati Uniti orientali".

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite le API Table di Azure Cosmos DB

