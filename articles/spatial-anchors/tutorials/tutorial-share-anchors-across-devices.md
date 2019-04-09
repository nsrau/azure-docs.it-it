---
title: Esercitazione - Condividere Ancoraggi nello spazio di Azure tra sessioni e dispositivi | Microsoft Docs
description: Questa esercitazione illustra come condividere gli identificatori di Ancoraggi nello spazio di Azure tra dispositivi Android/iOS in ​​Unity con un servizio back-end.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff9868dd7347812eb6ef566288ec364bc89b6955
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629315"
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

## <a name="open-the-sample-project-in-unity"></a>Aprire il progetto di esempio in Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Distribuire il servizio di condivisione degli ancoraggi

Aprire Visual Studio e il progetto nella cartella `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata distribuita un'app Web ASP.NET Core in Azure, quindi è stata configurata e distribuita un'app Unity. Sono stati creati ancoraggi nello spazio con l'app, successivamente condivisi con altri dispositivi tramite l'app Web ASP.NET Core.

Per altre informazioni su come migliorare l'app Web ASP.NET Core in modo che usi Azure Cosmos DB per archiviare gli identificatori di Ancoraggio nello spazio condivisi, continuare con l'esercitazione successiva. Azure Cosmos DB offre persistenza all'app Web ASP.NET Core. In questo modo l'app può creare un ancoraggio oggi, tornare dopo alcuni giorni ed essere ancora in grado di individuarlo usando l'identificatore di ancoraggio archiviato nell'app Web.

> [!div class="nextstepaction"]
> [Esercitazione: Usare Cosmos DB per archiviare gli ancoraggi](./tutorial-use-cosmos-db-to-store-anchors.md)
