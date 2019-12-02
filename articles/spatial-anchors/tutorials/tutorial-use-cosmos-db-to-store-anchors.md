---
title: 'Esercitazione: Condividere gli ancoraggi con Azure Cosmos DB'
description: Questa esercitazione illustra come condividere gli identificatori di Ancoraggi nello spazio di Azure tra dispositivi Android/iOS in ​​Unity con un servizio back-end e Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57bf9c9752cf2203ffcac08c213373fd71a79148
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276871"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Esercitazione: Condivisione di Ancoraggi nello spazio di Azure tra sessioni e dispositivi con un back-end di Azure Cosmos DB

Questa esercitazione è una continuazione della [condivisione di Ancoraggi nello spazio di Azure tra sessioni e dispositivi](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md). Illustrerà il processo di aggiunta di altre funzionalità che consentiranno ad Azure Cosmos DB di fungere da risorsa di archiviazione back-end durante la condivisione di Ancoraggi nello spazio di Azure tra sessioni e dispositivi.

![GIF che illustra la persistenza degli oggetti](./media/persistence.gif)

Si noti che in questa esercitazione si useranno Unity e Azure Cosmos DB solo per mostrare un esempio di condivisione degli identificatori di Ancoraggi nello spazio tra dispositivi. È possibile usare altri linguaggi e tecnologie di back-end per conseguire lo stesso obiettivo. Inoltre, l'app Web ASP.NET Core usata in questa esercitazione richiede .NET Core 2.2 SDK. Viene eseguita correttamente in app Web per Windows, ma attualmente non può essere eseguita in app Web per Linux.

## <a name="create-a-database-account"></a>Creare un account di database

Aggiungere un database Azure Cosmos DB al gruppo di risorse creato in precedenza.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copiare il valore di `Connection String` perché sarà necessario in seguito.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Apportare modifiche minime ai file SharingService

In **Esplora soluzioni** aprire `SharingService\Startup.cs`.

Individuare la riga `#define INMEMORY_DEMO` nella parte superiore del file e impostarla come commento. Salvare il file.

In **Esplora soluzioni** aprire `SharingService\appsettings.json`.

Individuare la proprietà `StorageConnectionString` e impostare lo stesso valore di `Connection String` annotato nel passaggio [Creare un account di database](#create-a-database-account). Salvare il file.

È possibile pubblicare di nuovo il servizio di condivisione ed eseguire l'app di esempio.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato Azure Cosmos DB per condividere identificatori di ancoraggi tra dispositivi. Per altre informazioni sull'uso degli ancoraggi nello spazio di Azure in una nuova app HoloLens in Unity, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Avvio di una nuova app HoloLens in Unity](./tutorial-new-unity-hololens-app.md)
