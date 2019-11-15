---
title: Avvio rapido di Azure - Creare un hub eventi tramite il portale di Azure
description: Questa guida introduttiva illustra come creare un hub eventi di Azure con il portale di Azure e inviare e ricevere eventi usando .NET Standard SDK.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 209bdb919a505e55bdfb8868d556fa1ad08db2fe
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717957"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Guida introduttiva: Creare un hub eventi con il portale di Azure
Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

In questa guida introduttiva viene creato un hub eventi usando il [portale di Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, assicurarsi di disporre di quanto segue:

- Sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- [Visual Studio 2019](https://www.visualstudio.com/vs) o versioni successive.
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è una raccolta logica per le risorse di Azure. Tutte le risorse vengono distribuite e gestite in un gruppo di risorse. Per creare un gruppo di risorse:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro di spostamento a sinistra fare clic su **Gruppi di risorse**. Fare quindi clic su **Aggiungi**.

   ![Gruppi di risorse: pulsante Aggiungi](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Per **Sottoscrizione** selezionare il nome della sottoscrizione di Azure in cui si vuole creare il gruppo di risorse.
3. Digitare un **nome univoco per il gruppo di risorse**. Il sistema verifica immediatamente se il nome è disponibile nella sottoscrizione di Azure attualmente selezionata.
4. Selezionare un'**area** per il gruppo di risorse.
5. Selezionare **Rivedi e crea**.

   ![Gruppo di risorse: Crea](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Nel riquadro **Rivedi e crea** selezionare **Crea**. 

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Uno spazio dei nomi di Hub eventi specifica un contenitore di ambito univoco, a cui fa riferimento il nome di dominio completo, in cui si crea uno o più hub eventi. Per creare uno spazio dei nomi nel gruppo di risorse usando il portale, eseguire le azioni seguenti:

1. Nel portale di Azure fare clic su **Crea una risorsa** nella parte superiore sinistra della schermata.
2. Selezionare **Tutti i servizi** nel menu a sinistra e scegliere la **stella (`*`)** accanto a **Hub eventi** nella categoria **Analytics**. Verificare che **Hub eventi** venga aggiunto a **PREFERITI** nel menu di spostamento a sinistra. 
    
   ![Cercare Hub eventi](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Selezionare **Hub eventi** sotto **PREFERITI** nel menu di spostamento a sinistra e scegliere **Aggiungi** sulla barra degli strumenti.

   ![Pulsante Aggiungi](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Nella pagina **Crea spazio dei nomi** seguire questa procedura:
    1. Immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.
    2. Scegliere il piano tariffario (Basic o Standard).
    3. Selezionare la **sottoscrizione** in cui creare lo spazio dei nomi.
    4. Selezionare una **località** per lo spazio dei nomi.
    5. Selezionare **Create** (Crea). Per il provisioning completo delle risorse da parte del sistema, potrebbero essere necessari alcuni minuti.

       ![Creare uno spazio dei nomi dell'hub eventi](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Aggiornare la pagina **Hub eventi** per visualizzare lo spazio dei nomi dell'hub eventi. È possibile controllare lo stato della creazione dell'hub eventi negli avvisi. 

    ![Creare uno spazio dei nomi dell'hub eventi](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Selezionare lo spazio dei nomi. Verrà visualizzata la home page per lo **spazio dei nomi Hub eventi** nel portale. 

   ![Home page dello spazio dei nomi](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Creare un hub eventi

Per creare un hub eventi all'interno dello spazio dei nomi, eseguire le azioni seguenti:

1. Nella pagina dello spazio dei nomi degli hub eventi selezionare **Hub eventi** nel menu a sinistra.
1. Nella parte superiore della finestra fare clic su **+ Hub eventi**.
   
    ![Aggiungi hub eventi: pulsante](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Digitare un nome per l'hub eventi e quindi fare clic su **Crea**.
   
    ![Creare un hub eventi](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. È possibile controllare lo stato della creazione dell'hub eventi negli avvisi. Dopo aver creato l'hub eventi, è possibile visualizzarlo nell'elenco degli hub eventi come illustrato nell'immagine seguente:

    ![Hub eventi creato](./media/event-hubs-quickstart-portal/event-hub-created.png)

Congratulazioni! È stato usato il portale per creare uno spazio dei nomi di Hub eventi e un hub eventi nello spazio dei nomi. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un gruppo di risorse, uno spazio dei nomi Hub eventi e un hub eventi. Per istruzioni dettagliate relative all'invio o alla ricezione di eventi da un hub eventi, vedere le esercitazioni per l'**invio e la ricezione di eventi**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (solo invio)](event-hubs-c-getstarted-send.md)
- [Apache Storm (solo ricezione)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
