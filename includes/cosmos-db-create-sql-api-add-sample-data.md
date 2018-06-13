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
ms.openlocfilehash: 92b739424d1d6f0e1eb89d5993718f5c36162204
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31429391"
---
È ora possibile aggiungere dati alla nuova raccolta usando Esplora dati.

1. In Esplora dati il nuovo database viene visualizzato nel riquadro Raccolte. Espandere il database **Tasks**, espandere la raccolta **Items**, fare clic su **Documenti** e quindi su **Nuovo documento**. 

   ![Creare nuovi documenti in Esplora dati nel portale di Azure](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Aggiungere ora un documento alla raccolta con la struttura seguente.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Dopo avere aggiunto il codice JSON alla scheda **Documenti**, fare clic su **Salva**.

    ![Copiare i dati JSON e fare clic su Salva in Esplora dati nel portale di Azure](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Creare e salvare un altro documento inserendo un valore univoco per la proprietà `id` e modificando le altre proprietà come si preferisce. I nuovi documenti possono avere la struttura desiderata, perché Azure Cosmos DB non impone alcuno schema per i dati.