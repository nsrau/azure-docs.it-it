---
title: Creare un hub eventi di Azure | Documentazione Microsoft
description: Creare uno spazio dei nomi di Hub eventi di Azure e un hub eventi usando il Portale di Azure
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: e444c4505d4744c95e08c4ef0d33566356785c81
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
1. Accedere al [portale di Azure][Azure portal] e fare clic su **Crea una risorsa** nella parte superiore sinistra della schermata.
1. Fare clic su **Internet delle cose** e quindi su **Hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. Nel pannello **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. Dopo aver verificato che il nome dello spazio dei nomi sia disponibile, scegliere il piano tariffario (Basic o Standard). Scegliere anche una sottoscrizione, un gruppo di risorse e una località di Azure in cui creare la risorsa. 
1. Fare clic su **Crea** per creare lo spazio dei nomi. Per il provisioning completo delle risorse da parte del sistema, potrebbero essere necessari alcuni minuti.
2. Nell'elenco di spazi dei nomi del portale fare clic sullo spazio dei nomi appena creato.
2. Fare clic su **Criteri di accesso condivisi** e quindi su **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

3. Fare clic sul pulsante di copia per copiare la stringa di connessione **RootManageSharedAccessKey** negli Appunti. Salvare la stringa di connessione in una posizione temporanea, ad esempio il Blocco note, per usarla in seguito.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

1. Nell'elenco di spazi dei nomi di Hub eventi fare clic sullo spazio dei nomi appena creato.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. Nel pannello dello spazio dei nomi fare clic su **Hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

1. Nella parte superiore del pannello fare clic su **Aggiungi hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Digitare un nome per l'hub eventi e quindi fare clic su **Crea**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli Hub eventi, visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Panoramica dell'API di Hub eventi](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/