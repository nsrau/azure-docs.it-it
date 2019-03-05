---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56891000"
---
Selezionare **Build** (Compila) per aprire una finestra di dialogo. Selezionare quindi una cartella in cui esportare il progetto Xcode.

Al termine dell'esportazione, verrà visualizzata una cartella contenente il progetto Xcode esportato.

### <a name="open-the-xcode-project"></a>Aprire il progetto Xcode

Nella cartella del progetto Xcode esportato eseguire il comando seguente per installare l'utilità CocoaPods necessaria per il progetto:

```bash
pod install --repo-update
```

Ora è possibile aprire `Unity-iPhone.xcworkspace` per aprire il progetto in Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Se viene visualizzato un errore `library not found for -lPods-Unity-iPhone`, è probabile che sia stato aperto il file `.xcodeproj` invece di `.xcworkspace`. Aprire `.xcworkspace` e riprovare.

Selezionare il nodo **Unity-iPhone** radice per visualizzare le impostazioni del progetto e selezionare la scheda **General** (Generale).

In **Signing** (Firma) selezionare **Automatically manage signing** (Gestisci automaticamente la firma). Nella finestra di dialogo visualizzata selezionare **Enable Automatic** (Abilita automatico) per reimpostare le impostazioni di compilazione.

In **Deployment Info** (Informazioni sulla distribuzione) assicurarsi che l'opzione **Deployment Target** (Destinazione distribuzione) sia impostata su `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Distribuire l'app nel dispositivo iOS

Connettere il dispositivo iOS al Mac e impostare lo **schema attivo** sul dispositivo iOS.

![Selezionare il dispositivo](./media/spatial-anchors-unity/select-device.png)

Selezionare **Build and then run the current scheme** (Compila e quindi esegui lo schema corrente).

![Distribuire ed eseguire](./media/spatial-anchors-unity/deploy-run.png)