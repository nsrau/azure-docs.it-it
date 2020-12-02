---
title: 'Esercitazione: Condividere gli ancoraggi tra sessioni e dispositivi'
description: Questa esercitazione illustra come condividere gli identificatori di Ancoraggi nello spazio di Azure tra dispositivi Android/iOS in ​​Unity con un servizio back-end.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3048eb1ca7f9312a43bc1ab5885bf19d4c2e7931
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185388"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Esercitazione: Condividere ancoraggi nello spazio tra sessioni e dispositivi

Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. 

Questa esercitazione illustra come usare gli [ancoraggi nello spazio di Azure](../overview.md) per creare ancoraggi durante una sessione e quindi individuarli nello stesso dispositivo o in uno diverso. Questi stessi ancoraggi possono anche essere individuati da più dispositivi nello stesso luogo e nello stesso momento.

![Animazione che mostra gli ancoraggi nello spazio creati con un dispositivo mobile e usati con un dispositivo diverso nel corso dei giorni.](./media/persistence.gif)


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Distribuire un'app Web ASP.NET Core in Azure che è possibile usare per condividere ancoraggi, quindi archiviare gli ancoraggi in memoria per un periodo di tempo specificato.
> * Configurare la scena AzureSpatialAnchorsLocalSharedDemo all'interno dell'esempio Unity disponibile nelle guide di avvio rapido per sfruttare l'app Web di condivisione degli ancoraggi.
> * Distribuire ed eseguire gli ancoraggi in uno o più dispositivi.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> In questa esercitazione si useranno Unity e l'app Web ASP.NET Core, ma questo approccio si prefigge solo di fornire un esempio di come condividere gli identificatori di ancoraggi nello spazio di Azure con altri dispositivi. È possibile usare altri linguaggi e tecnologie di back-end per conseguire lo stesso obiettivo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Scaricare il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Distribuire il servizio di condivisione degli ancoraggi

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Aprire Visual Studio e quindi aprire il progetto nella cartella *Sharing\SharingServiceSample*.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Prima di distribuire il servizio in Visual Studio Code, è necessario creare un gruppo di risorse e un piano di servizio app.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Passare al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a> e quindi accedere alla propria sottoscrizione di Azure.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Accanto a **Gruppo di risorse** selezionare **Nuovo**.

Assegnare al gruppo di risorse il nome **myResourceGroup** e quindi selezionare **OK**.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Accanto a **Piano di hosting** selezionare **Nuovo**.

Nel riquadro **Configura piano di hosting** usare queste impostazioni:

| Impostazione | Valore consigliato | Descrizione |
|-|-|-|
|Piano di servizio app| MySharingServicePlan | Nome del piano di servizio app |
| Location | Stati Uniti occidentali | Il data center in cui viene ospitata l'app Web |
| Dimensione | Gratuito | Il [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) che determina le funzionalità di hosting |

Selezionare **OK**.

Aprire Visual Studio Code e quindi aprire il progetto nella cartella *Sharing\SharingServiceSample*. 

Per distribuire il servizio di condivisione tramite Visual Studio Code, seguire le istruzioni riportate in <a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">Pubblicare un'app ASP.NET Core in Azure con Visual Studio Code</a>. Iniziare dalla sezione "Aprire il progetto con Visual Studio Code". Non creare un altro progetto ASP.NET come illustrato nel passaggio precedente, perché è già disponibile un progetto da distribuire e pubblicare, ovvero SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Distribuire l'app di esempio

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata distribuita un'app Web ASP.NET Core in Azure, quindi è stata configurata e distribuita un'app Unity. Sono stati creati ancoraggi nello spazio con l'app, successivamente condivisi con altri dispositivi tramite l'app Web ASP.NET Core.

È possibile migliorare l'app Web ASP.NET Core in modo che usi Azure Cosmos DB per rendere permanente l'archiviazione degli identificatori di ancoraggi nello spazio condivisi. Aggiungendo il supporto di Azure Cosmos DB, l'app Web ASP.NET Core può creare un ancoraggio immediatamente. Quindi, usando l'identificatore dell'ancoraggio archiviato nell'app Web, è possibile usare l'app dopo diversi giorni per individuare di nuovo l'ancoraggio.

> [!div class="nextstepaction"]
> [Usare Cosmos DB per archiviare gli ancoraggi](./tutorial-use-cosmos-db-to-store-anchors.md)