---
title: "Guida introduttiva: Creare un'app per Android"
description: In questa guida introduttiva si apprenderà come creare un'app Android usando Ancoraggi nello spazio.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7c62ded627ab6f6ead5c03ef8cf298b4ee622265
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277109"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app Android con Ancoraggi nello spazio di Azure

Questa guida introduttiva illustra come creare un'app Android usando [Ancoraggi nello spazio di Azure](../overview.md) in Java o C++/NDK. Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine, si avrà un'app ARCore per Android in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Distribuire ed eseguire l'app in un dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

- Un computer Windows o macOS con <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
  - Se in esecuzione su Windows, sarà necessario anche <a href="https://git-scm.com/download/win" target="_blank">Git per Windows</a>.
  - Se in esecuzione su macOS, installare Git tramite HomeBrew. Immettere il comando seguente in una singola riga del terminale: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Quindi eseguire `brew install git`.
  - Per creare l'esempio in NDK, sarà anche necessario installare gli strumenti NDK e CMake 3.6 SDK o versione successiva in Android Studio.
- Un dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">abilitato per lo sviluppo</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">idoneo per ARCore</a>.
  - Potrebbero essere necessari driver di dispositivo aggiuntivi per la comunicazione del computer con il dispositivo Android. Vedere [qui](https://developer.android.com/studio/run/device.html) per altre informazioni e istruzioni.
- L'app deve avere come destinazione ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Scaricare `arcore_c_api.h` da [qui](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) e inserirlo in `Android\NDK\libraries\include`.

Dall'interno del repository appena clonato, inizializzare i moduli secondari eseguendo il comando seguente:

```console
git submodule update --init --recursive
```

---

Aprire Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Selezionare **Open an existing Android Studio project** (Apri un progetto di Android Studio esistente) e selezionare il progetto disponibile in `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Selezionare **Open an existing Android Studio project** (Apri un progetto di Android Studio esistente) e selezionare il progetto disponibile in `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Il passaggio successivo consiste nel configurare l'app in modo da usare l'identificatore e la chiave dell'account. Questi dati sono stati copiati in un editor di testo durante la [configurazione della risorsa Ancoraggi nello spazio](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Aprire `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Individuare il campo `SpatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `SpatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Aprire `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Individuare il campo `SpatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `SpatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

---

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
