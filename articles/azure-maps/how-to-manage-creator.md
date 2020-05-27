---
title: Gestire Creator di Mappe di Azure
description: Questo articolo illustra come gestire Creator di Mappe di Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 579294388dbcf9f785ef41e06505c14b6767565f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677963"
---
# <a name="manage-azure-maps-creator"></a>Gestire Creator di Mappe di Azure

Creator di Mappe di Azure consente di creare dati della pianta di interni privati. Usando l'API di Mappe di Azure e il modulo Piante di interni, è possibile sviluppare applicazioni Web interattive e dinamiche per le piante di interni. Attualmente, Creator è disponibile solo negli Stati Uniti usando il piano tariffario S1.

Questo articolo illustra i passaggi per creare ed eliminare una risorsa Creator in un account di Mappe di Azure.

## <a name="create-creator-resource"></a>Creare una risorsa Creator

1. Accedere al [portale di Azure](https://portal.azure.com)

2. Selezionare il proprio account di Mappe di Azure. Se non è possibile visualizzare il proprio account di Mappe di Azure in **Risorse recenti**, passare al menu del portale di Azure. Selezionare **Tutte le risorse**. Individuare e selezionare il proprio account di Mappe di Azure.

    ![Home page del portale di Mappe di Azure](./media/how-to-manage-creator/select-maps-account.png)

3. Quando si è nella pagina dell'account di Mappe di Azure, passare all'opzione **Panoramica** in **Creator**. Fare clic su **Crea** per creare una risorsa Creator di Mappe di Azure.

    ![Pagina per creare Creator di Mappe di Azure](./media/how-to-manage-creator/creator-blade-settings.png)

4. Immettere il nome e la posizione per la risorsa Creator. Attualmente, Creator è supportato solo negli Stati Uniti. Fare clic su **Rivedi e crea**.

   ![Pagina per immettere le informazioni sull'account Creator](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Rivedere le impostazioni e fare clic su **Crea**.

    ![Pagina per confermare le impostazioni dell'account Creator](./media/how-to-manage-creator/creator-create-dialog.png)

6. Al termine della distribuzione, verrà visualizzata una pagina con un messaggio di esito positivo o negativo.

   ![Pagina dello stato di distribuzione delle risorse](./media/how-to-manage-creator/creator-resource-created.png)

7. Fare clic su **Vai alla risorsa**. La pagina di visualizzazione delle risorse Creator mostra lo stato della risorsa Creator e l'area geografica scelta.

    ![Pagina relativa allo stato di Creator](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Dalla pagina della risorsa Creator è possibile tornare all'account di Mappe di Azure a cui appartiene facendo clic sull'account di Mappe di Azure.

## <a name="delete-creator-resource"></a>Eliminare una risorsa Creator

Per eliminare la risorsa Creator, passare all'account di Mappe di Azure. Selezionare **Panoramica** in **Creator**. Fare clic sul pulsante **Elimina**.

>[!WARNING]
>Quando si elimina la risorsa Creator dell'account di Mappe di Azure, si eliminano anche i set di dati, i set di tessere e i set di stati della funzionalità creati con i servizi Creator.

![Pagina di Creator con il pulsante Elimina](./media/how-to-manage-creator/creator-delete.png)

Fare clic sul pulsante **Elimina** e digitare il nome Creator per confermare l'eliminazione. Una volta eliminata la risorsa, viene visualizzata una pagina di conferma, come nell'immagine seguente:

![Pagina di Creator con la conferma dell'eliminazione](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Authentication

Creator eredita le impostazioni di controllo di accesso (IAM) di Mappe di Azure. Tutte le chiamate API per l'accesso ai dati devono essere inviate con regole di autenticazione e autorizzazione.

I dati di utilizzo di Creator sono incorporati nei grafici di utilizzo e nei log attività di Mappe di Azure.  Per altre informazioni, vedere [Gestire l'autenticazione in Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

## <a name="access-to-creator-services"></a>Accesso ai servizi Creator

I servizi Creator sono accessibili solo dall'interno della località selezionata durante la creazione. Se le chiamate vengono effettuate ai servizi Creator dall'esterno della località selezionata, verrà restituito un messaggio di errore utente. Per effettuare chiamate dall'esterno della località selezionata, l'URL del servizio deve includere il prefisso geografico per le località selezionate. Se, ad esempio, Creator viene creato negli Stati Uniti, tutte le chiamate al servizio di conversione devono essere inviate a `us.atlas.microsoft.com/conversion/convert`.

Inoltre, tutti i dati importati in Creator devono essere caricati nella stessa località geografica della risorsa Creator. Se, ad esempio, il provisioning di Creator viene eseguito negli Stati Uniti, tutti i dati non elaborati devono essere caricati tramite `us.atlas.microsoft.com/mapData/upload`.

## <a name="next-steps"></a>Passaggi successivi

Introduzione a Creator per la creazione di piante di interni:

> [!div class="nextstepaction"]
> [Data Upload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversione dati](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Set di dati](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Set di tessere](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Set di stati della funzionalità](creator-indoor-maps.md#feature-statesets)

Informazioni su come usare Creator per eseguire il rendering delle piante di interni nell'applicazione:

> [!div class="nextstepaction"]
> [Esercitazione di Creator di Mappe di Azure](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Stili dinamici per le piante di interni](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Usare il modulo Piante di interni](how-to-use-indoor-module.md)