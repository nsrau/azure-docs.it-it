---
title: Installare gli ancoraggi spaziali di Azure per Unity
description: Configurare un progetto Unity per l'uso di ancoraggi spaziali di Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: da983719dc66656aa28cab4aea0bae558c2a7162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530418"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurazione di ancoraggi spaziali di Azure in un progetto Unity

Questa guida illustra come iniziare a usare Azure Spatial Anchors SDK nel progetto Unity.

## <a name="requirements"></a>Requisiti

Gli ancoraggi spaziali di Azure attualmente supportano Unity 2019,4 (LTS) con le configurazioni seguenti.

* Unity 2019,4 con AR Foundation 3,1 è supportato negli ancoraggi spaziali di Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Configurazione di un progetto

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Aggiungere i pacchetti di gestione pacchetti Unity al progetto](#tab/UPMPackage)

Gli ancoraggi spaziali di Azure per Unity vengono attualmente distribuiti usando i pacchetti di gestione pacchetti Unity (UPM). Questi pacchetti sono disponibili nel [Registro NPM](https://bintray.com/microsoft/AzureMixedReality-NPM). Per altre informazioni sull'uso di registri di pacchetti con ambito in un progetto Unity, vedere la documentazione ufficiale di Unity [qui](https://docs.unity3d.com/Manual/upm-scoped.html).

#### <a name="add-the-registry-to-your-unity-project"></a>Aggiungere il registro al progetto Unity

1. In Esplora file passare alla cartella `Packages` del progetto Unity. Aprire il file manifesto del progetto `manifest.json` in un editor di testo.
2. Nella parte iniziale del file, allo stesso livello della sezione `dependencies`, aggiungere la voce seguente per includere il registro di ancoraggi nello spazio di Azure nel progetto. La voce `scopedRegistries` indica a Unity dove cercare i pacchetti di Azure Spatial Anchors SDK.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Aggiungere i pacchetti SDK al progetto Unity

| Piattaforma | Nome del pacchetto                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. Spatial-Anchors-SDK. Android |
| iOS      | com. Microsoft. Azure. Spatial-Anchors-SDK. iOS     |
| HoloLens | com. Microsoft. Azure. Spatial-Anchors-SDK. Windows |

1. Per ogni piattaforma (Android/iOS/HoloLens) che si desidera supportare nel progetto, aggiungere una voce con il nome del pacchetto e la versione del pacchetto alla `dependencies` sezione nel manifesto del progetto. Per un esempio, vedere di seguito.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Salvare e chiudere il file `manifest.json`. Quando si torna a Unity, Unity rileva automaticamente la modifica del manifesto del progetto e recupera i pacchetti specificati. È possibile espandere la cartella `Packages` nella visualizzazione del progetto per verificare che i pacchetti corretti siano stati importati.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Solo Android: configurare il file mainTemplate. Gradle

1. Andare a **Modifica** > **Impostazioni progetto** > **Lettore**.
2. Nel **pannello Inspector** per **le impostazioni del lettore**selezionare l'icona **Android** .
3. Nella sezione **compilazione** selezionare la casella di controllo **modello principale personalizzato Gradle** per generare un modello Gradle personalizzato in `Assets\Plugins\Android\mainTemplate.gradle` .
4. Apri il file `mainTemplate.gradle` in un editor di testo.
5. Nella `dependencies` sezione incollare le dipendenze seguenti:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Al termine, la `dependencies` sezione dovrebbe avere un aspetto simile al seguente:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Importare il pacchetto di asset](#tab/UnityAssetPackage)

> [!WARNING]
> La distribuzione del pacchetto di asset Unity di Azure Spatial Anchors SDK è stata deprecata nella versione 2.5.0 e non è più disponibile a partire da 2.6.0.

1. Scaricare il `AzureSpatialAnchors.unitypackage` file per la versione che si vuole usare come destinazione dalle [versioni di GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Seguire le istruzioni riportate [qui](https://docs.unity3d.com/Manual/AssetPackagesImport.html) per importare il pacchetto di asset Unity nel progetto.

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: creare e individuare ancoraggi in Unity](./create-locate-anchors-unity.md)
