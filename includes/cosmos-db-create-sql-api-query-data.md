---
title: includere file
description: includere file
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85115209"
---
È possibile usare le query in Esplora dati per recuperare e filtrare i dati.

1. Nella parte superiore della scheda **Elementi** di Esplora dati esaminare la query predefinita `SELECT * FROM c`. Questa query recupera e visualizza tutti i documenti presenti nel contenitore ordinati in base all'ID. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="La query predefinita in Esplora dati è SELECT * FROM c":::
   
1. Per modificare la query, selezionare **Modifica filtro**, sostituire la query predefinita con `ORDER BY c._ts DESC`, quindi selezionare **Applica filtro**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Cambiare la query predefinita aggiungendo ORDER BY c._ts DESC e facendo clic su Applica filtro":::

   La query modificata elenca i documenti in ordine decrescente in base al timestamp, quindi il secondo documento creato appare ora in cima all'elenco. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Query cambiata in ORDER BY c._ts DESC e facendo clic su Applica filtro":::

Se si ha familiarità con la sintassi SQL, è possibile immettere qualsiasi [query SQL](../articles/cosmos-db/sql-api-sql-query.md) supportata nella casella del predicato della query. È anche possibile usare Esplora dati per creare stored procedure, funzioni definite dall'utente e trigger per eseguire la logica di business sul lato server. 

Esplora dati consente di accedere facilmente dal portale di Azure a tutte le funzionalità di accesso ai dati a livello di codice disponibili nelle API. Si usa il portale anche per ridimensionare la velocità effettiva, ottenere le chiavi e le stringhe di connessione ed esaminare le metriche e i contratti di servizio per l'account Azure Cosmos DB. 

