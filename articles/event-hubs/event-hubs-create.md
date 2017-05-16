---
title: Creare un hub eventi di Azure | Documentazione Microsoft
description: Creare uno spazio dei nomi di Hub eventi di Azure e un hub eventi usando il Portale di Azure
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: jotaub
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 2ef14fccda5c38ebdbf9d64b4766b45503755af0
ms.contentlocale: it-it
ms.lasthandoff: 05/04/2017

---

# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
1. Accedere al [portale di Azure][Azure portal] e fare clic su **Nuovo** nella parte superiore sinistra della schermata.
1. Fare clic su **Internet delle cose** e quindi su **Hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. Nel pannello **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. Dopo aver verificato che il nome dello spazio dei nomi sia disponibile, scegliere il piano tariffario (Basic o Standard). Scegliere anche una sottoscrizione, un gruppo di risorse e una località di Azure in cui creare la risorsa. 
1. Fare clic su **Crea** per creare lo spazio dei nomi.
1. Nell'elenco degli spazi dei nomi di Hub eventi fare clic sullo spazio dei nomi appena creato.      
   
    ![](./media/event-hubs-create/create-event-hub2.png)
1. Nel pannello dello spazio dei nomi fare clic su **Hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

1. Nella parte superiore del pannello fare clic su **Aggiungi hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Digitare un nome per l'hub eventi e quindi fare clic su **Crea**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)
1. Nell'elenco degli hub eventi fare clic sul nome dell'hub eventi appena creato. 
    
    ![](./media/event-hubs-create/create-event-hub6.png)
1. Nel pannello dello spazio dei nomi (non in quello dello specifico hub eventi) fare clic su **Criteri di accesso condivisi** e quindi su **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)
1. Fare clic sul pulsante di copia per copiare la stringa di connessione **RootManageSharedAccessKey** negli Appunti. Salvare questa stringa di connessione per usarla più avanti nell'esercitazione.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli Hub eventi, visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Panoramica dell'API di Hub eventi](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/
