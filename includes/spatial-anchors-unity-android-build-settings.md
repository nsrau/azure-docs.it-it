---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012123"
---
Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

Nella sezione **Platform** (Piattaforma) selezionare **Android**. Impostare **Build System** (Sistema di compilazione) su **Gradle** e selezionare l'opzione **Export Project** (Esporta progetto).

Selezionare **Switch Platform** (Cambia piattaforma) per impostare **Android** come piattaforma. Unity potrebbe chiedere di installare i componenti di supporto di Android se sono mancanti.

![Finestra di impostazioni di compilazione di Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Chiudere la finestra **Build Settings** (Impostazioni compilazione).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Scaricare e importare ARCore SDK per Unity

Scaricare il file `unitypackage` dalle [versioni di ARCore SDK per Unity 1.7](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). Tornare nel progetto Unity, selezionare **Assets** > **Import Package** > **Custom Package** (Asset > Importa pacchetto > Pacchetto personalizzato) e quindi selezionare il file `unitypackage` scaricato in precedenza. Nella finestra di dialogo **Import Unity Package** (Importa pacchetto Unity) assicurarsi che siano selezionati tutti i file e quindi scegliere **Import** (Importa).
