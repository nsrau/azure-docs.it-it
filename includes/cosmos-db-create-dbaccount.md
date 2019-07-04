---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 5d57d7e18befba175a5a8a825494ce512644b5a2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179900"
---
1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Crea una risorsa** > **Database** > **Azure Cosmos DB**.
   
   ![Riquadro Database nel portale di Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Nella pagina **Crea account Cosmos DB** immettere le impostazioni base per il nuovo account Azure Cosmos. 
 
    |Impostazione|Valore|DESCRIZIONE |
    |---|---|---|
    |Sottoscrizione|Nome della sottoscrizione|Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos. |
    |Gruppo di risorse|Nome del gruppo di risorse|Selezionare un gruppo di risorse oppure fare clic su **Crea nuovo**, quindi immettere un nome univoco per il nuovo gruppo di risorse. |
    | Nome account|Immettere un nome univoco|Immettere un nome per identificare l'account Azure Cosmos. Poiché all'ID fornito viene aggiunto *documents.azure.com* per creare l'URI, usare un ID univoco.<br><br>L'ID può contenere solo lettere minuscole, numeri e il carattere trattino (-). Deve avere una lunghezza compresa tra 3 e 31 caratteri.|
    | API|Core (SQL)|L'API determina il tipo di account da creare. Azure Cosmos DB offre cinque API: Core (SQL) e MongoDB per dati dei documenti, Gremlin per i dati dei grafici, Tabelle di Azure e Cassandra. Attualmente, è necessario creare un account separato per ogni API. <br><br>Selezionare **Core (SQL)** per creare un database di documenti e una query con la sintassi SQL. <br><br>[Altre informazioni sull'API SQL](../articles/cosmos-db/documentdb-introduction.md).|
    | Località|Selezionare l'area più vicina agli utenti|Selezionare una posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.|
   
   ![Pagina del nuovo account per Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

1. Selezionare **Rivedi e crea**. È possibile ignorare le sezioni **Rete** e **Tag**. 

1. Esaminare le impostazioni dell'account e quindi selezionare **Crea**. La creazione dell'account richiede alcuni minuti. Attendere che la pagina del portale visualizzi **La distribuzione è stata completata**. 

    ![Riquadro Notifiche del portale di Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Selezionare **Vai alla risorsa** per passare alla pagina dell'account Azure Cosmos DB. 

    ![Pagina dell'account Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
