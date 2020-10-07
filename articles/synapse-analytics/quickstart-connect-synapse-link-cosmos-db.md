---
title: 'Avvio rapido: Connettersi a Collegamento ad Azure Synapse per Azure Cosmos DB'
description: Come connettere un database Azure Cosmos DB a un'area di lavoro di Synapse con Collegamento a Synapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: ed717c2423b5336824bccd256bf43654c31a4c8a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91568956"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Avvio rapido: Connettersi a Collegamento ad Azure Synapse per Azure Cosmos DB

Questo articolo descrive come accedere a un database Azure Cosmos DB da Azure Synapse Analytics Studio con Collegamento a Synapse. 

## <a name="prerequisites"></a>Prerequisiti

Prima di connettere un account Azure Cosmos DB all'area di lavoro, è necessario soddisfare alcuni requisiti.

* Un account Azure Cosmos DB esistente oppure uno nuovo da creare seguendo questo [avvio rapido](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)
* Un'area di lavoro di Synapse esistente oppure una nuova da creare seguendo questo [avvio rapido](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Abilitare l'archivio analitico di Azure Cosmos DB

Per eseguire analisi su larga scala in Azure Cosmos DB senza influire sulle prestazioni operative, è consigliabile abilitare Collegamento a Synapse per Azure Cosmos DB. Questa funzione introduce funzionalità HTAP in un contenitore e il supporto predefinito in Azure Synapse. Seguire questa guida di avvio rapido per abilitare Collegamento a Synapse per contenitori Cosmos DB.

## <a name="navigate-to-synapse-studio"></a>Passare a Synapse Studio

Nell'area di lavoro di Synapse selezionare **Avvia Synapse Studio**. Nella home page di Synapse Studio selezionare **Dati per aprire **Data Object Explorer** (Esplora oggetti dati).

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Connettere un database Azure Cosmos DB a un'area di lavoro di Synapse

La connessione di un database Azure Cosmos DB si esegue come servizio collegato. Un servizio collegato di Cosmos DB consente agli utenti di esplorare, leggere e scrivere dati in Azure Cosmos DB da Apache Spark per Azure Synapse Analytics o da SQL.

Da Data Object Explorer (Esplora oggetti dati) è possibile connettere direttamente un database Azure Cosmos DB seguendo questa procedura:

1. Selezionare l'icona ***+*** accanto a Dati
2. Selezionare **Connect to external data** (Connetti a dati esterni)
3. Selezionare l'API a cui connettersi, ovvero SQL o MongoDB
4. Selezionare ***Continua***
5. Assegnare un nome al servizio collegato. Il nome verrà visualizzato in Esplora oggetti e verrà usato dai runtime di Synapse per connettersi al database e ai contenitori. È consigliabile usare un nome descrittivo.
6. Selezionare il **nome dell'account Cosmos DB** e il **nome del database**
7. (Facoltativo) Se non viene specificata un'area, le operazioni dei runtime di Synapse verranno instradate all'area più vicina in cui è abilitato l'archivio analitico. È tuttavia possibile impostare manualmente l'area in cui gli utenti dovranno accedere all'archivio analitico di Cosmos DB. Selezionare **Additional connection properties** (Proprietà di connessione aggiuntive) e quindi **Nuovo**. In **Nome proprietà** scrivere ***PreferredRegions*** e impostare **Valore** sull'area desiderata, ad esempio WestUS2, senza spazi tra parole e numeri
8. Selezionare ***Crea***

I database Azure Cosmos DB sono visibili nella scheda **Collegato** nella sezione Azure Cosmos DB. È possibile distinguere un contenitore Azure Cosmos DB abilitato per HTAP da un contenitore solo OLTP con le icone seguenti:

**Contenitore Synapse**:

![contenitore HTAP](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**Contenitore solo OLTP**:

![contenitore OLTP](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagire rapidamente con le azioni generate dal codice

Quando si fa clic con il pulsante destro del mouse in un contenitore, verrà visualizzato un elenco di movimenti che attiveranno un runtime di Spark o SQL. La scrittura in un contenitore viene eseguita tramite l'archivio transazionale di Azure Cosmos DB e consumerà unità richiesta.  

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle funzionalità supportate tra Synapse e Azure Cosmos DB](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Informazioni su come eseguire query su un archivio analitico con Apache Spark per Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark.md)