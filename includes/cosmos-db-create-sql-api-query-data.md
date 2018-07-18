---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: e6e70da1f939547cdb589ae7bcb6ed1f6148f22e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733770"
---
È ora possibile usare le query in Esplora dati per recuperare e filtrare i dati.

1. Per impostazione predefinita, la query è impostata su `SELECT * FROM c`. La query predefinita recupera e visualizza tutti i documenti nella raccolta. 

    ![La query predefinita in Esplora dati è 'SELECT * FROM c'](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

2. Restare nella scheda **Documenti** e modificare la query facendo clic sul pulsante **Modifica filtro**, aggiungendo `ORDER BY c._ts DESC` nella casella del predicato della query e quindi facendo clic su **Applica filtro**.

    ![Modificare la query predefinita aggiungendo ORDER BY c._ts DESC e facendo clic su Applica filtro.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

La query modificata elenca i documenti in ordine decrescente in base al timestamp, pertanto il secondo documento creato appare ora in cima all'elenco. Se si ha familiarità con la sintassi SQL, è possibile immettere in questa casella una qualsiasi delle [query SQL](../articles/cosmos-db/sql-api-sql-query.md) supportate. 

Con questa azione si completa il lavoro in Esplora dati. Prima di passare all'uso del codice, si noti che è possibile usare Esplora dati anche per creare stored procedure, UDF e trigger per eseguire la logica di business lato server, nonché scalare la velocità effettiva. Esplora dati espone tutti i tipi di accesso ai dati a livello di codice predefiniti disponibili nelle API, ma consente anche di accedere facilmente ai dati nel portale di Azure.