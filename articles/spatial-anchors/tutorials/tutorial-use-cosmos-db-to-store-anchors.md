---
title: Esercitazione - Condivisione tra sessioni e dispositivi con Ancoraggi nello spazio di Azure e un back-end di Azure Cosmos DB | Microsoft Docs
description: Questa esercitazione illustra come condividere gli identificatori di Ancoraggi nello spazio di Azure tra dispositivi Android/iOS in ​​Unity con un servizio back-end e Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0e7011b9778221869940b137a2b87239f2d8db9b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286398"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Esercitazione: Condivisione tra sessioni e dispositivi con Ancoraggi nello spazio di Azure e un back-end di Azure Cosmos DB

Questa esercitazione illustra come usare [Ancoraggi nello spazio di Azure](../overview.md) per eseguire queste operazioni:

- Creare ancoraggi in una sessione e quindi individuarli in un'altra sessione nello stesso dispositivo o in un altro. La seconda sessione potrebbe ad esempio svolgersi in un giorno diverso.
- Creare ancoraggi che possono essere individuati da più dispositivi nello stesso luogo e nello stesso momento.

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

## <a name="open-the-sample-project-in-unity"></a>Aprire il progetto di esempio in Unity

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

In questa esercitazione è stato usato Azure Cosmos DB per condividere identificatori di ancoraggi tra dispositivi. Per altre informazioni sulla libreria di Ancoraggi nello spazio di Azure, passare alla guida che illustra come creare e individuare ancoraggi.

> [!div class="nextstepaction"]
> [Creare e individuare ancoraggi usando Ancoraggi nello spazio di Azure](../create-locate-anchors-overview.md)
