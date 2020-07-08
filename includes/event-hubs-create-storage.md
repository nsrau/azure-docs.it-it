---
title: File di inclusione
description: File di inclusione
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75692559"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Creare un account di archiviazione per l'host del processore di eventi
L'host del processore di eventi è un agente intelligente che semplifica la ricezione di eventi da Hub eventi gestendo checkpoint persistenti e ricezioni parallele. Per eseguire il checkpoint, l'host del processore di eventi richiede un account di archiviazione. Nell'esempio seguente viene illustrato come creare un account di archiviazione e come ottenere le relative chiavi di accesso:

1. Nel menu del portale di Azure selezionare **Crea una risorsa**.

    ![Voce di menu Crea una risorsa, portale di Microsoft Azure](./media/event-hubs-create-storage/create-resource.png)

2. Selezionare **Archiviazione** > **Account di archiviazione**.
   
    ![Selezionare Account di archiviazione, portale di Microsoft Azure](./media/event-hubs-create-storage/select-storage-account.png)

3. Nella pagina **Crea account di archiviazione** eseguire la procedura seguente: 

   1. Immettere un valore in **Nome account di archiviazione**.
   2. Scegliere una sottoscrizione di Azure contenente l'hub eventi in **Sottoscrizione**.
   3. Scegliere o creare il **gruppo di risorse** in cui è incluso l'hub eventi.
   4. Selezionare la **località** in cui creare la risorsa. 
   5. Selezionare **Rivedi e crea**.
   
        ![Rivedi e crea, Crea account di archiviazione, portale di Microsoft Azure](./media/event-hubs-create-storage/review-create.png)

4. Nella pagina **Rivedi e crea** rivedere i valori e selezionare **Crea**. 

    ![Rivedere le impostazioni dell'account di archiviazione e creare, portale di Microsoft Azure](./media/event-hubs-create-storage/create-storage-account.png)
5. Dopo la visualizzazione del messaggio **Le distribuzioni sono riuscite** nelle notifiche, selezionare **Vai alla risorsa** per aprire la pagina dell'account di archiviazione. In alternativa, è possibile espandere **Dettagli distribuzione** e quindi selezionare la nuova risorsa nell'elenco di risorse.  

    ![Vai alla risorsa, distribuzione dell'account di archiviazione, portale di Microsoft Azure](./media/event-hubs-create-storage/go-to-resource.png) 
6. Selezionare **Contenitori**.

    ![Selezionare il servizio per contenitori BLOB, account di archiviazione, portale di Microsoft Azure](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Selezionare **+ Contenitore** in alto, immettere un **nome** per il contenitore e selezionare **OK**. 

    ![Creare un nuovo contenitore BLOB, account di archiviazione, portale di Microsoft Azure](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Scegliere **Chiavi di accesso** nel menu della pagina **Account di archiviazione** e copiare il valore di **key1**.

    Salvare i valori seguenti nel Blocco note o in un'altra posizione temporanea.
    - Nome dell'account di archiviazione
    - Chiave di accesso per l'account di archiviazione
    - Nome del contenitore
