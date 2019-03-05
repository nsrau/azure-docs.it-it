---
title: Esercitazione - Condivisione tra sessioni e dispositivi con Ancoraggi nello spazio di Azure | Microsoft Docs
description: Questa esercitazione illustra come condividere gli identificatori di Ancoraggi nello spazio di Azure tra dispositivi in ​​Unity con un servizio back-end.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: df7f8aa5b49e3fe17be3b17a6e0f5d8861b26253
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753467"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>Esercitazione: condivisione tra sessioni e dispositivi con Ancoraggi nello spazio di Azure

Questa esercitazione illustra come usare [Ancoraggi nello spazio di Azure](../overview.md) per eseguire queste operazioni:

1. Creare ancoraggi in una sessione e quindi individuarli in un'altra sessione nello stesso dispositivo o in un altro, ad esempio in un giorno diverso.
2. Creare ancoraggi che possono essere individuati da più dispositivi nello stesso luogo e nello stesso momento.

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

## <a name="deploy-your-sharing-anchors-service"></a>Distribuire il servizio di condivisione degli ancoraggi

Aprire Visual Studio e il progetto nella cartella `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata distribuita un'app Web ASP.NET Core in Azure, quindi è stata configurata e distribuita un'app Unity. Sono stati creati ancoraggi nello spazio con l'app, successivamente condivisi con altri dispositivi tramite l'app Web ASP.NET Core. Per altre informazioni su come ottimizzare l'app Web ASP.NET Core in modo che usi Cosmos DB per archiviare gli ancoraggi nello spazio condivisi, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Esercitazione: usare Cosmos DB per archiviare gli ancoraggi](./tutorial-use-cosmos-db-to-store-anchors.md)
