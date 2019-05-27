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
ms.openlocfilehash: b84b0a8e09bf739ce62dee167ff751b491765c66
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66154560"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Creare un account di archiviazione per l'host del processore di eventi
L'host del processore di eventi è un agente intelligente che semplifica la ricezione di eventi da Hub eventi gestendo checkpoint persistenti e ricezioni parallele. Per eseguire il checkpoint, l'host del processore di eventi richiede un account di archiviazione. Nell'esempio seguente viene illustrato come creare un account di archiviazione e come ottenere le relative chiavi di accesso:

1. Nel portale di Azure selezionare **Crea una risorsa** nella parte superiore sinistra della schermata.

2. Selezionare **Storage** (Archiviazione) e quindi **Storage account - blob, file, table, queue** (Account di archiviazione: BLOB, File, Tabelle, Code).
   
    ![Selezionare l'account di archiviazione](./media/event-hubs-create-storage/create-storage1.png)

3. Nella pagina **Crea account di archiviazione** eseguire la procedura seguente: 

   1. Immettere il nome dell'account di archiviazione. 
   2. Scegliere una sottoscrizione di Azure che contenga l'hub eventi.
   3. Selezionare il gruppo di risorse che contiene l'hub eventi.
   4. Selezionare una posizione in cui creare la risorsa. 
   5. Quindi fare clic su **Rivedi e crea**.
   
      ![Crea account di archiviazione: pagina](./media/event-hubs-create-storage/create-storage2.png)

4. Nella pagina **Rivedi e crea** rivedere i valori e selezionare **Crea**. 

    ![Rivedere le impostazioni dell'account di archiviazione e creare](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Dopo aver visualizzato il **le distribuzioni sono riuscite** messaggio, seleziona **Vai alla risorsa** nella parte superiore della pagina. È anche possibile aprire la pagina Account di archiviazione selezionando l'account di archiviazione nell'elenco delle risorse.  

    ![Selezionare l'account di archiviazione dalla distribuzione](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. Nella finestra **Informazioni di base** selezionare **BLOB**. 

    ![Selezionare il servizio BLOB](./media/event-hubs-create-storage/select-blobs-service.png)
1. Selezionare **+ Contenitore** nella parte superiore, immettere un **nome** per il contenitore e fare clic su **OK**. 

    ![Creare un contenitore BLOB](./media/event-hubs-create-storage/create-blob-container.png)
1. Selezionare **Chiavi di accesso** nel menu sul lato sinistro e copiare il valore di **key1**. 

    Salvare i valori seguenti nel Blocco note o in un'altra posizione temporanea.
    - Nome dell'account di archiviazione
    - Chiave di accesso per l'account di archiviazione
    - Nome del contenitore
