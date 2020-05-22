---
title: Connettersi a Collegamento ad Azure Synapse per Azure Cosmos DB
description: Come connettere un database Azure Cosmos DB a un'area di lavoro di Synapse con Collegamento ad Azure Synapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: d72d1feda4c267dfa3d5c0dea6928f1b7541b26d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599011"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Connettersi a Collegamento ad Azure Synapse per Azure Cosmos DB

Questo articolo descrive come accedere a un database Azure Cosmos DB da Azure Synapse Analytics Studio con Collegamento ad Azure Synapse.

## <a name="prerequisites"></a>Prerequisiti

Prima di connettere un database Azure Cosmos DB all'area di lavoro, è necessario soddisfare questi requisiti:

* Un database Azure Cosmos DB esistente oppure uno nuovo da creare seguendo questo [avvio rapido](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)
* Un'area di lavoro di Synapse esistente oppure una nuova da creare seguendo questo [avvio rapido](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Abilitare l'archivio analitico di Azure Cosmos DB

Per eseguire analisi su larga scala in Azure Cosmos DB senza influire sulle prestazioni operative, è consigliabile abilitare Collegamento a Synapse per Azure Cosmos DB. Collegamento a Synapse introduce funzionalità HTAP in un contenitore e il supporto predefinito in Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Passare a Synapse Studio

Nell'area di lavoro di Synapse selezionare **Avvia Synapse Studio**. Nella home page di Synapse Studio selezionare **Dati per aprire **Data Object Explorer** (Esplora oggetti dati).

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Connettere un database Azure Cosmos DB a un'area di lavoro di Synapse

La connessione di un database Azure Cosmos DB si esegue come servizio collegato. Un servizio collegato di Azure Cosmos DB consente agli utenti di esplorare, leggere e scrivere dati in Azure Cosmos DB da Apache Spark per Azure Synapse Analytics o da SQL.

Da Data Object Explorer (Esplora oggetti dati) è possibile connettersi direttamente a un database Azure Cosmos DB seguendo questa procedura:

1. Selezionare l'icona ***+*** accanto a Dati
2. Selezionare **Connect to external data** (Connetti a dati esterni)
3. Selezionare l'API a cui connettersi, ovvero API SQL o API per MongoDB
4. Selezionare ***Continua***
5. Assegnare un nome al servizio collegato. Il nome verrà visualizzato in Esplora oggetti e verrà usato dai runtime di Synapse per connettersi al database e ai contenitori. È consigliabile usare un nome descrittivo.
6. Selezionare il **nome dell'account Azure Cosmos DB** e il **nome del database**
7. (Facoltativo) Se non viene specificata un'area, le operazioni dei runtime di Synapse verranno instradate all'area più vicina in cui è abilitato l'archivio analitico. È tuttavia possibile impostare manualmente l'area in cui gli utenti dovranno accedere all'archivio analitico di Azure Cosmos DB. Selezionare **Additional connection properties** (Proprietà di connessione aggiuntive) e quindi **Nuovo**. In **Nome proprietà** scrivere ***PreferredRegions*** e impostare **Valore** sull'area desiderata, ad esempio WestUS2, senza spazi tra parole e numeri
8. Selezionare ***Crea***

I database Azure Cosmos DB sono visibili nella scheda **Collegato** nella sezione Azure Cosmos DB. Con Azure Cosmos DB, è possibile distinguere un contenitore abilitato per HTAP da un contenitore solo OLTP tramite le icone seguenti:

**Contenitore solo OLTP**:

![contenitore OLTP](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Contenitore abilitato per HTAP**:

![contenitore HTAP](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagire rapidamente con le azioni generate dal codice

Quando si fa clic con il pulsante destro del mouse in un contenitore, verrà visualizzato un elenco di movimenti che attiveranno un runtime Spark o SQL. La scrittura in un contenitore viene eseguita tramite l'archivio transazionale di Azure Cosmos DB e consumerà unità richiesta.  

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle funzionalità supportate tra Synapse e Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Informazioni su come eseguire query sull'archivio analitico con Spark](./how-to-query-analytical-store-spark.md)