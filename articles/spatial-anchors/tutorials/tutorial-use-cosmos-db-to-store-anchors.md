---
title: Esercitazione - Condivisione Ancoraggi nello spazio di Azure tra sessioni e dispositivi con un back-end di Azure Cosmos DB | Microsoft Docs
description: Questa esercitazione illustra come condividere gli identificatori di Ancoraggi nello spazio di Azure tra dispositivi Android/iOS in ​​Unity con un servizio back-end e Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: fc172b5327d72687fea7d13ddb706ecc7ab630b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135377"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Esercitazione: Condivisione di Ancoraggi nello spazio di Azure tra sessioni e dispositivi con un back-end di Azure Cosmos DB

Questa esercitazione illustra come usare [Ancoraggi nello spazio di Azure](../overview.md) per creare ancoraggi durante una sessione e quindi individuarli durante un'altra sessione, nello stesso dispositivo o in uno differente. La seconda sessione potrebbe ad esempio svolgersi in un giorno diverso. Questi stessi ancoraggi potrebbero anche essere individuati da più dispositivi nello stesso luogo e nello stesso momento.

![GIF che illustra la persistenza degli oggetti](./media/persistence.gif)

[Ancoraggi nello spazio di Azure](../overview.md) è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista con oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine si avrà un'app che può essere distribuita in due o più dispositivi. Gli ancoraggi nello spazio creati da un'istanza condivideranno i propri identificatori con le altre usando Azure Cosmos DB.

Si apprenderà come:

> [!div class="checklist"]
> * Distribuire un'app Web ASP.NET Core in Azure che può essere usata per condividere ancoraggi, archiviandoli in Azure Cosmos DB.
> * Configurare la scena AzureSpatialAnchorsLocalSharedDemo all'interno dell'esempio Unity disponibile nelle guide di avvio rapido di Azure per sfruttare l'app Web di condivisione degli ancoraggi.
> * Distribuire un'app in uno o più dispositivi ed eseguirla.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Si noti che in questa esercitazione si useranno Unity e Azure Cosmos DB solo per mostrare un esempio di condivisione degli identificatori di Ancoraggi nello spazio tra dispositivi. È possibile usare altri linguaggi e tecnologie di back-end per conseguire lo stesso obiettivo. Inoltre, l'app Web ASP.NET Core usata in questa esercitazione richiede .NET Core 2.2 SDK. Viene eseguita correttamente in app Web per Windows, ma attualmente non può essere eseguita in app Web per Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copiare il valore di `Connection String` perché sarà necessario in seguito.

## <a name="download-the-unity-sample-project"></a>Scaricare il progetto di esempio di Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Distribuire il servizio di condivisione degli ancoraggi

Aprire Visual Studio e il progetto nella cartella `Sharing\SharingServiceSample`.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Configurare il servizio per l'uso del database Azure Cosmos DB

In **Esplora soluzioni** aprire `SharingService\Startup.cs`.

Individuare la riga `#define INMEMORY_DEMO` nella parte superiore del file e impostarla come commento. Salvare il file.

In **Esplora soluzioni** aprire `SharingService\appsettings.json`.

Individuare la proprietà `StorageConnectionString` e impostare lo stesso valore di `Connection String` annotato nel passaggio [Creare un account di database](#create-a-database-account). Salvare il file.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato Azure Cosmos DB per condividere identificatori di ancoraggi tra dispositivi. Per altre informazioni sull'uso degli ancoraggi nello spazio di Azure in una nuova app HoloLens in Unity, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Avvio di una nuova app Android](./tutorial-new-unity-hololens-app.md)