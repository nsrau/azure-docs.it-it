---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933488"
---
Selezionare **Compila**. Nella finestra di dialogo visualizzata selezionare una cartella in cui esportare il progetto Xcode.

Al termine dell'esportazione, verrà visualizzata una cartella contenente il progetto Xcode esportato.

> [!NOTE]
> Se viene visualizzata una finestra in cui si chiede di specificare se sostituire o accodare, è consigliabile selezionare **Accoda** perché è più veloce. Selezionare **Sostituisci** solo se si cambiano gli asset della scena, ad esempio se si aggiungono, rimuovono o cambiano le relazioni padre/figlio oppure se si aggiungono, rimuovono o cambiano le proprietà. Se si stanno solo apportando modifiche al codice sorgente, l'operazione **Accoda**dovrebbe essere sufficiente.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Convertire il progetto Xcode in xcworkspace contenente i riferimenti ad Ancoraggi nello spazio di Azure

Nella cartella del progetto Xcode esportato eseguire il comando seguente nel terminale per installare l'utilità CocoaPods necessaria per il progetto:

```bash
pod install --repo-update
```

Ora è possibile aprire `Unity-iPhone.xcworkspace` per aprire il progetto in Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Vedere i passaggi relativi alla risoluzione dei problemi [qui](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) se si verificano problemi di CocoaPod dopo l'aggiornamento a macOS Catalina (10.15).

Selezionare il nodo **Unity-iPhone** radice per visualizzare le impostazioni del progetto e selezionare la scheda **General** (Generale).

In **Signing** (Accesso) assicurarsi che sia abilitata l'opzione **Automatically manage signing** (Gestisci automaticamente l'accesso). In caso contrario, abilitarla e quindi selezionare **Enable Automatic** (Abilita automatico) nella finestra di dialogo visualizzata per reimpostare le impostazioni di compilazione.

In **Deployment Info** (Informazioni sulla distribuzione) assicurarsi che l'opzione **Deployment Target** (Destinazione distribuzione) sia impostata su `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Distribuire l'app nel dispositivo iOS

Connettere il dispositivo iOS al Mac e impostare lo **schema attivo** sul dispositivo iOS.

![Selezionare il dispositivo](./media/spatial-anchors-unity/select-device.png)

Selezionare **Build and then run the current scheme** (Compila e quindi esegui lo schema corrente).

![Distribuire ed eseguire](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Se viene visualizzato un errore `library not found for -lPods-Unity-iPhone`, è probabile che sia stato aperto il file `.xcodeproj` invece di `.xcworkspace`.
