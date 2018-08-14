---
title: Creare un hub eventi di Azure | Documentazione Microsoft
description: Creare uno spazio dei nomi di Hub eventi di Azure e un hub eventi usando il Portale di Azure
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: shvija
ms.openlocfilehash: fb2020f7bfc8521e141adb4eefbc227e8123a269
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002621"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

1. Accedere al [portale di Azure][Azure portal] e fare clic su **Crea una risorsa** nella parte superiore sinistra della schermata.
2. Fare clic su **Internet delle cose** e quindi su **Hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. Nel pannello **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.  

4. Dopo aver verificato che il nome dello spazio dei nomi sia disponibile, scegliere il piano tariffario (Basic o Standard). Scegliere anche una sottoscrizione, un gruppo di risorse e una località di Azure in cui creare la risorsa.
 
5. Fare clic su **Crea** per creare lo spazio dei nomi. Per il provisioning completo delle risorse da parte del sistema, potrebbero essere necessari alcuni minuti.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. Nell'elenco di spazi dei nomi del portale fare clic sullo spazio dei nomi appena creato.

7. Fare clic su **Criteri di accesso condivisi** e quindi su **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Fare clic sul pulsante di copia per copiare la stringa di connessione **RootManageSharedAccessKey** negli Appunti. Salvare la stringa di connessione in una posizione temporanea, ad esempio il Blocco note, per usarla in seguito.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

1. Nell'elenco di spazi dei nomi di Hub eventi fare clic sullo spazio dei nomi appena creato.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. Nel pannello dello spazio dei nomi fare clic su **Hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Nella parte superiore del pannello fare clic su **+ Hub eventi**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Digitare un nome per l'hub eventi e quindi fare clic su **Crea**. 

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli Hub eventi, visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Panoramica dell'API di Hub eventi](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/