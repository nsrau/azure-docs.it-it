---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752725"
---
Aprire Unity e il progetto nella cartella `Unity`.

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** -> **Build Settings** (File -> Impostazioni compilazione).

Nella sezione **Platform** (Piattaforma) selezionare **Android**. Quindi impostare **Build System** (Sistema di compilazione) su **Gradle** e selezionare l'opzione **Export Project** (Esporta progetto).

Selezionare **Switch Platform** (Cambia piattaforma) per impostare **Android** come piattaforma. Unity potrebbe chiedere di installare i componenti di supporto di Android se sono mancanti.

![Impostazioni di compilazione di Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Chiudere la finestra **Build Settings** (Impostazioni compilazione).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Scaricare e importare ARCore SDK per Unity

Scaricare il file `unitypackage` dalle [versioni di ARCore SDK per Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Tornare nel progetto Unity, selezionare **Assets** -> **Import Package** -> **Custom Package** (Asset > Importa pacchetto > Pacchetto personalizzato) e quindi selezionare il file `unitypackage` scaricato in precedenza. Nella finestra di dialogo **Import Unity Package** (Importa pacchetto Unity) assicurarsi che siano selezionati tutti i file e quindi scegliere **Import** (Importa).
