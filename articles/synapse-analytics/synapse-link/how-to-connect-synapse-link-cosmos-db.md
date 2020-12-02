---
title: Connettersi a Collegamento ad Azure Synapse (anteprima) per Azure Cosmos DB
description: Come connettere un database Azure Cosmos DB a un'area di lavoro di Azure Synapse con Collegamento ad Azure Synapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 0e4bcc13a2eaddbf394d8468b29eeff475119e2d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459029"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Connettersi a Collegamento ad Azure Synapse per Azure Cosmos DB

Questo articolo descrive come accedere a un database Azure Cosmos DB da Azure Synapse Analytics Studio con Collegamento ad Azure Synapse.

## <a name="prerequisites"></a>Prerequisiti

Prima di connettere un database Azure Cosmos DB all'area di lavoro, è necessario quanto segue:

* Un database Azure Cosmos DB esistente oppure creare un nuovo account seguendo i passaggi descritti in [Avvio rapido: Gestire un account di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account).
* Un'area di lavoro di Azure Synapse oppure crearne una nuova seguendo i passaggi descritti in [Avvio rapido: Creare un'area di lavoro di Synapse](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace).

> [!IMPORTANT]
> Collegamento ad Azure Synapse per Azure Cosmos DB è attualmente supportato per le aree di lavoro in cui non è abilitata una rete virtuale gestita.

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Abilitare Collegamento a Synapse in un account di database Azure Cosmos DB

Per eseguire analisi su larga scala in Azure Cosmos DB senza effetti negativi sulle prestazioni operative, è consigliabile abilitare Collegamento a Synapse per Azure Cosmos DB. Collegamento a Synapse introduce funzionalità HTAP in un contenitore e il supporto predefinito in Azure Synapse.

## <a name="go-to-synapse-studio"></a>Passare a Synapse Studio

In un'area di lavoro di Azure Synapse selezionare **Avvia Synapse Studio**. Nella home page di Synapse Studio selezionare **Data** (Dati) per aprire Data Object Explorer (Esplora oggetti dati).

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Connettere un database Azure Cosmos DB a un'area di lavoro di Azure Synapse

La connessione di un database Azure Cosmos DB si esegue come servizio collegato. Con un servizio collegato di Azure Cosmos DB è possibile esplorare, leggere e scrivere dati in Azure Cosmos DB da Apache Spark per Azure Synapse Analytics o da SQL.

Da Data Object Explorer (Esplora oggetti dati) è possibile connettersi direttamente a un database Azure Cosmos DB seguendo questa procedura:

1. Selezionare l'icona **+** accanto a **Data** (Dati).
1. Selezionare **Connect to external data** (Connetti a dati esterni).
1. Selezionare l'API a cui ci si vuole connettere, ad esempio l'**API SQL** o l'**API per MongoDB**.
1. Selezionare **Continua**.
1. Assegnare un nome descrittivo al servizio collegato. Il nome verrà visualizzato in Data Object Explorer (Esplora oggetti dati) e verrà usato dai runtime di Azure Synapse per connettersi al database e ai contenitori.
1. Selezionare il **nome dell'account Azure Cosmos DB** e il **nome del database**.
1. (Facoltativo) Se non viene specificata un'area, le operazioni dei runtime di Azure Synapse verranno instradate all'area più vicina in cui è abilitato l'archivio analitico. È anche possibile impostare manualmente l'area che gli utenti dovranno usare per accedere all'archivio analitico di Azure Cosmos DB. Selezionare **Additional connection properties** (Proprietà di connessione aggiuntive) e quindi **New** (Nuovo). In **Property Name** (Nome proprietà) immettere **PreferredRegions**. Impostare **Value** (Valore) sull'area desiderata, ad esempio **WestUS2** (senza spazi tra le parole e il numero).
1. Selezionare **Crea**.

I database Azure Cosmos DB sono visualizzati nella scheda **Collegato** nella sezione **Azure Cosmos DB**. Con Azure Cosmos DB è possibile distinguere un contenitore abilitato per HTAP da un contenitore solo OLTP tramite le icone seguenti:

**Contenitore solo OLTP**:

![Visualizzazione che mostra l'icona del contenitore OLTP.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Contenitore abilitato per HTAP**:

![Visualizzazione che mostra l'icona del contenitore HTAP.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagire rapidamente con le azioni generate dal codice

Facendo clic con il pulsante destro del mouse in un contenitore, viene visualizzato un elenco di movimenti che attiveranno un runtime Spark o SQL. La scrittura in un contenitore viene eseguita tramite l'archivio transazionale di Azure Cosmos DB e consumerà unità richiesta.  

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle funzionalità supportate tra Azure Synapse e Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Informazioni su come eseguire query sull'archivio analitico con Spark](./how-to-query-analytical-store-spark.md)
