---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 33c932c36cd6de730d3768d596a214c442d74ae1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632824"
---
Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

Nella sezione **Platform** (Piattaforma) selezionare **Android**. Impostare **Build System** (Sistema di compilazione) su **Gradle** e selezionare l'opzione **Export Project** (Esporta progetto).

Selezionare **Switch Platform** (Cambia piattaforma) per impostare **Android** come piattaforma. Unity potrebbe chiedere di installare i componenti di supporto di Android se sono mancanti.

![Finestra di impostazioni di compilazione di Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Chiudere la finestra **Build Settings** (Impostazioni compilazione).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Scaricare e importare ARCore SDK per Unity

Scaricare il file `unitypackage` dalle [versioni di ARCore SDK per Unity 1.5](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Tornare nel progetto Unity, selezionare **Assets** > **Import Package** > **Custom Package** (Asset > Importa pacchetto > Pacchetto personalizzato) e quindi selezionare il file `unitypackage` scaricato in precedenza. Nella finestra di dialogo **Import Unity Package** (Importa pacchetto Unity) assicurarsi che siano selezionati tutti i file e quindi scegliere **Import** (Importa).
