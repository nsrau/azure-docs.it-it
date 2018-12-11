---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: c3cbfda674abaeea1adf35c3ee0d2b5ddf6b2f84
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853330"
---
È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database e una raccolta. 

1. Fare clic su **Esplora dati** > **Nuova raccolta**. 
    
    A destra verrà visualizzata l'area **Aggiungi raccolta**. Per vederla potrebbe essere necessario scorrere verso destra.

    ![Esplora dati nel portale di Azure: pannello Aggiungi raccolta](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Nella pagina **Aggiungi raccolta** immettere le impostazioni per la nuova raccolta.

    Impostazione|Valore consigliato|DESCRIZIONE
    ---|---|---
    ID database|*Attività*|Immettere *Tasks* come nome del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere /, \\, #, ? o spazi finali.
    ID raccolta|*Items*|Immettere *Items* come nome della nuova raccolta. Gli ID delle raccolte prevedono gli stessi requisiti relativi ai caratteri dei nomi di database.
    Effettuare il provisioning della velocità effettiva di database|Lasciare vuoto|Azure Cosmos DB può effettuare il provisioning della velocità effettiva a livello di database (tutte le raccolte in un database di condivideranno la stessa velocità effettiva) o a livello di raccolta. Lasciare vuoto per effettuare il provisioning della velocità effettiva a livello di raccolta per questa specifica raccolta.
    Capacità di archiviazione|*Illimitato*|Scegliere la capacità di archiviazione **Illimitata**. 
    Chiave di partizione|*/category*|Immettere "/category" come chiave di partizione. L'impostazione di una chiave di partizione consente ad Azure Cosmos DB di ridimensionare la raccolta per soddisfare le esigenze di archiviazione e velocità effettiva dell'applicazione. In generale, una scelta appropriata per la chiave di partizione è una con un'ampia gamma di valori distinti, che consenta la distribuzione uniforme dei volumi di archiviazione e richieste per il carico di lavoro. [Altre informazioni sul partizionamento.](../articles/cosmos-db/partitioning-overview.md)
    Velocità effettiva|*400 UR/sec*|Modificare la velocità effettiva in 400 unità richiesta al secondo (UR/sec). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento. 
    
    Oltre alle impostazioni precedenti, è possibile aggiungere **Chiavi univoche** per la raccolta. In questo esempio il campo viene lasciato vuoto. Le chiavi univoche consentono agli sviluppatori di aggiungere un livello di integrità dei dati nel database. Se si definiscono criteri di chiave univoca durante la creazione di una raccolta, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione. Per altre informazioni, vedere l'articolo [Chiavi univoche in Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Fare clic su **OK**.

    In Esplora dati verranno visualizzati il nuovo database e la nuova raccolta.

    ![Esplora dati nel portale di Azure, con il nuovo database e la nuova raccolta](./media/cosmos-db-create-collection/azure-cosmos-db-data-explorer-preview.png)