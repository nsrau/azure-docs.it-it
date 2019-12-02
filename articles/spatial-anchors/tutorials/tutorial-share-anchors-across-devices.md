---
title: 'Esercitazione: Condividere gli ancoraggi tra sessioni e dispositivi'
description: Questa esercitazione illustra come condividere gli identificatori di Ancoraggi nello spazio di Azure tra dispositivi Android/iOS in ​​Unity con un servizio back-end.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0029fcf8e9efdea529212a7cca49cc8660c623f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276956"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Esercitazione: Condividere Ancoraggi nello spazio di Azure tra sessioni e dispositivi

Questa esercitazione illustra come usare [Ancoraggi nello spazio di Azure](../overview.md) per creare ancoraggi durante una sessione e quindi individuarli nello stesso dispositivo o in un differente. Questi stessi ancoraggi potrebbero anche essere individuati da più dispositivi nello stesso luogo e nello stesso momento.

![Persistenza](./media/persistence.gif)

Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine si avrà un'app che può essere distribuita in due o più dispositivi. Gli ancoraggi nello spazio di Azure creati da un'istanza possono essere condivisi con le altre.

Si apprenderà come:

> [!div class="checklist"]
> * Distribuire un'app Web ASP.NET Core in Azure che può essere usata per condividere ancoraggi, archiviandoli per un periodo di tempo.
> * Configurare la scena AzureSpatialAnchorsLocalSharedDemo all'interno dell'esempio Unity disponibile negli avvi rapidi per sfruttare l'app Web di condivisione degli ancoraggi.
> * Distribuire ed eseguire in uno o più dispositivi.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Si noti che in questa esercitazione si useranno Unity e l'app Web ASP.NET Core solo per mostrare un esempio di condivisione degli identificatori di Ancoraggi nello spazio di Azure con altri dispositivi. È possibile usare altri linguaggi e tecnologie di back-end per conseguire lo stesso obiettivo. L'app Web ASP.NET Core usata in questa esercitazione ha una dipendenza da .NET Core 2.2 SDK. Funziona correttamente in app Web di Azure per Windows, ma al momento non in app Web di Azure per Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Scaricare il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Distribuire il servizio di condivisione degli ancoraggi

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/VS)

Aprire Visual Studio e il progetto nella cartella `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-codetabvsc"></a>[Visual Studio Code](#tab/VSC)

Prima di distribuire il servizio in VS Code, è necessario creare un gruppo di risorse e un piano di servizio app.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Passare al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a> e accedere alla propria sottoscrizione di Azure.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Accanto a **Gruppo di risorse** selezionare **Nuovo**.

Assegnare al gruppo di risorse il nome **myResourceGroup** e selezionare **OK**.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Accanto a **Piano di hosting** selezionare **Nuovo**.

Nella finestra di dialogo **Configura piano di hosting** usare le impostazioni seguenti:

| Impostazione | Valore consigliato | DESCRIZIONE |
|-|-|-|
|Piano di servizio app| MySharingServicePlan | Nome del piano di servizio app. |
| Location | Stati Uniti occidentali | Data center in cui è ospitata l'app Web. |
| Dimensione | Gratuito | Il [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) che determina le funzionalità di hosting. |

Selezionare **OK**.

Aprire Visual Studio Code e il progetto nella cartella `Sharing\SharingServiceSample`. Seguire <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">questa esercitazione</a> per distribuire il servizio di condivisione tramite Visual Studio Code. È possibile seguire la procedura a partire dalla sezione "Aprire il progetto con Visual Studio Code". Non creare un altro progetto MVC come illustrato nel passaggio precedente poiché il progetto da distribuire e pubblicare, ovvero SharingServiceSample, è già presente.

---

## <a name="deploy-the-sample-app"></a>Distribuire l'app di esempio

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata distribuita un'app Web ASP.NET Core in Azure, quindi è stata configurata e distribuita un'app Unity. Sono stati creati ancoraggi nello spazio con l'app, successivamente condivisi con altri dispositivi tramite l'app Web ASP.NET Core.

È possibile migliorare l'app Web ASP.NET Core in modo che usi Azure Cosmos DB per rendere permanente l'archiviazione degli identificatori di Ancoraggio nello spazio condivisi. Con l'aggiunta del supporto di Azure Cosmos DB, l'app Web ASP.NET Core potrà creare un ancoraggio oggi, tornare dopo alcuni giorni ed essere ancora in grado di individuarlo usando l'identificatore di ancoraggio archiviato nell'app Web.

> [!div class="nextstepaction"]
> [Usare Azure Cosmos DB per archiviare gli ancoraggi](./tutorial-use-cosmos-db-to-store-anchors.md)

