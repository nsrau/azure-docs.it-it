---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020158"
---
È ora possibile aggiungere dati al nuovo contenitore usando Esplora dati.

1. In **Esplora dati** espandere il database **Tasks** e quindi il contenitore **Elementi**. Selezionare **Elementi** e quindi **Nuovo elemento**.

   ![Creare nuovi documenti in Esplora dati nel portale di Azure](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Aggiungere ora un documento al contenitore con la struttura seguente.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Dopo avere aggiunto il codice JSON alla scheda **Documenti**, selezionare **Salva**.

    ![Copiare i dati JSON e selezionare Salva in Esplora dati nel portale di Azure](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Creare e salvare un altro documento inserendo un valore univoco per la proprietà `id` e modificando le altre proprietà come si preferisce. I nuovi documenti possono avere la struttura desiderata, perché Azure Cosmos DB non impone alcuno schema per i dati.