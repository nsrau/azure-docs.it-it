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
ms.openlocfilehash: cf77eaa07d45222cecf0450fb33fe62e556bcd9e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database e una raccolta. 

1. Fare clic su **Esplora dati** > **Nuova raccolta**. 
    
    A destra verrà visualizzata l'area **Aggiungi raccolta**. Per vederla potrebbe essere necessario scorrere verso destra.

    ![Esplora dati nel portale di Azure: pannello Aggiungi raccolta](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Nella pagina **Aggiungi raccolta** immettere le impostazioni per la nuova raccolta.

    Impostazione|Valore consigliato|DESCRIZIONE
    ---|---|---
    ID database|Attività|Immettere *Tasks* come nome del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere /, \\, #, ? o spazi finali.
    ID raccolta|Items|Immettere *Items* come nome della nuova raccolta. Gli ID delle raccolte prevedono gli stessi requisiti relativi ai caratteri dei nomi di database.
    Capacità di archiviazione| Fissa (10 GB)|Usare il valore predefinito **Fissa (10 GB)**. Questo valore indica la capacità di archiviazione del database.
    Velocità effettiva|400 UR|Modificare la velocità effettiva in 400 unità richiesta al secondo (UR/sec). La capacità di archiviazione deve essere impostata su **Fisso (10 GB)** per impostare la velocità effettiva su 400 UR/sec. Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento. 
    
    Oltre alle impostazioni precedenti, è possibile aggiungere **Chiavi univoche** per la raccolta. In questo esempio il campo viene lasciato vuoto. Le chiavi univoche consentono agli sviluppatori di aggiungere un livello di integrità dei dati nel database. Se si definiscono criteri di chiave univoca durante la creazione di una raccolta, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione. Per altre informazioni, vedere l'articolo [Chiavi univoche in Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Fare clic su **OK**.

    In Esplora dati verranno visualizzati il nuovo database e la nuova raccolta.

    ![Esplora dati nel portale di Azure, con il nuovo database e la nuova raccolta](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)