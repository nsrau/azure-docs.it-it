---
title: "Guida introduttiva: Creare un'app Android con Ancoraggi nello spazio di Azure | Microsoft Docs"
description: In questa guida introduttiva si apprenderà come creare un'app Android usando Ancoraggi nello spazio.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64e5e78f93488b2c375c617e8857c84ba2171f00
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822396"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Avvio rapido: Creare un'app Android con Ancoraggi nello spazio di Azure

Questa guida introduttiva illustra come creare un'app Android usando [Ancoraggi nello spazio di Azure](../overview.md) in Java o C++/NDK. Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine, si avrà un'app ARCore per Android in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Distribuire ed eseguire l'app in un dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

- Un computer Windows o macOS con <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a> installato.
  - Per creare l'esempio in NDK, sarà anche necessario installare gli strumenti NDK e CMake 3.6 SDK in Android Studio.
- Un dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">abilitato per lo sviluppo</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">idoneo per ARCore</a>.
- L'app deve essere destinata ad ARCore 1.5 (il supporto per ARCore 1.6+ verrà aggiunto prossimamente)

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Se si crea l'esempio in NDK Android, sarà necessario scaricare `arcore_c_api.h` da [qui](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.5.0/libraries/include/arcore_c_api.h) e inserirlo in `Android\NDK\libraries\include`.

Aprire Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Selezionare **Open an existing Android Studio project** (Apri un progetto di Android Studio esistente) e selezionare il progetto disponibile in `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Selezionare **Open an existing Android Studio project** (Apri un progetto di Android Studio esistente) e selezionare il progetto disponibile in `Android/NDK/`.

***

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Il passaggio successivo consiste nell'usare l'identificatore e la chiave dell'account registrati in precedenza durante la configurazione della risorsa di Ancoraggi nello spazio per configurare l'app.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Aprire `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`.

Individuare il campo `SpatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `SpatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Aprire `Android/NDK/app/src/main/cpp/spatial_services_application.cc`.

Individuare il campo `SpatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `SpatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

***

## <a name="deploy-the-app-to-your-android-device"></a>Distribuire l'app nel dispositivo Android

Accendere il dispositivo Android, eseguire l'accesso e connetterlo al PC con un cavo USB.

Selezionare **Run** (Esegui) sulla barra degli strumenti di Android Studio.

![Distribuzione ed esecuzione in Android Studio](./media/get-started-android/android-studio-deploy-run.png)

Selezionare il dispositivo Android nella finestra di dialogo **Select Deployment Target** (Seleziona destinazione distribuzione) e scegliere **OK** per eseguire l'app nel dispositivo Android.

Seguire le istruzioni nell'app per posizionare e richiamare un ancoraggio.

Arrestare l'app selezionando **Stop** (Arresta) sulla barra degli strumenti di Android Studio.

![Arresto dell'app in Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)
