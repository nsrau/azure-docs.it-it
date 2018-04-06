---
title: Creare un account API di MongoDB in Azure Cosmos DB
description: Descrive come creare un account API di MongoDB in Azure Cosmos DB nel portale di Azure
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
1. In una nuova finestra accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra fare clic su **Crea una risorsa**, quindi su **Database** e infine in **Azure Cosmos DB** fare clic su **Crea**.
   
   ![Screenshot del portale di Azure in cui sono evidenziati Altri servizi e Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Nel pannello **Nuovo account** specificare **MongoDB** per l'API e immettere le informazioni relative alla configurazione desiderata per l'account Azure Cosmos DB.
 
    ![Screenshot del pannello Nuovo Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * **ID** deve essere un nome univoco da usare per identificare l'account Azure Cosmos DB. Può contenere solo lettere minuscole, numeri, il carattere "-" e deve essere compreso fra 3 e 50 caratteri.
    * **Sottoscrizione** è la sottoscrizione di Azure e viene compilata automaticamente.
    * **Gruppo di risorse** è il nome del gruppo di risorse per l'account Azure Cosmos DB.
    * **Percorso** è la posizione geografica in cui si trova l'istanza di Azure Cosmos DB. Scegliere la località più vicina agli utenti.

4. Fare clic su **Crea** per creare l'account.
5. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.

    ![Notifica di distribuzione avviata](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Al termine della distribuzione aprire il nuovo account dal riquadro Tutte le risorse. 

    ![Account Azure Cosmos DB nel riquadro Tutte le risorse](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
