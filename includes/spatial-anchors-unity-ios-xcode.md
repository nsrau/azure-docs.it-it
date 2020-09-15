---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536262"
---
Selezionare **Compila**. Nella finestra di dialogo visualizzata selezionare una cartella in cui esportare il progetto Xcode.

Al termine dell'esportazione, verrà visualizzata una cartella contenente il progetto Xcode esportato.

> [!NOTE]
> Se viene visualizzata una finestra in cui si chiede di specificare se sostituire o accodare, è consigliabile selezionare **Accoda** perché è più veloce. Selezionare **Sostituisci** solo se si cambiano gli asset della scena, ad esempio se si aggiungono, rimuovono o cambiano le relazioni padre/figlio oppure se si aggiungono, rimuovono o cambiano le proprietà. Se si stanno solo apportando modifiche al codice sorgente, l'operazione **Accoda**dovrebbe essere sufficiente.

## <a name="open-the-xcode-project"></a>Aprire il progetto Xcode

Ora è possibile aprire `Unity-iPhone.xcodeproj` in Xcode. È possibile avviare Xcode e aprire il progetto esportato `Unity-iPhone.xcodeproj` oppure avviare il progetto in Xcode eseguendo il comando seguente dalla posizione in cui è stato esportato il progetto:

```bash
open ./Unity-iPhone.xcodeproj
```

Selezionare il nodo **Unity-iPhone** radice per visualizzare le impostazioni del progetto e selezionare la scheda **General** (Generale).

In **Signing** (Accesso) assicurarsi che sia abilitata l'opzione **Automatically manage signing** (Gestisci automaticamente l'accesso). In caso contrario, abilitarla e quindi selezionare **Enable Automatic** (Abilita automatico) nella finestra di dialogo visualizzata per reimpostare le impostazioni di compilazione.

In **Deployment Info** (Informazioni sulla distribuzione) assicurarsi che l'opzione **Deployment Target** (Destinazione distribuzione) sia impostata su `11.0`.

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuire l'app nel dispositivo iOS

Connettere il dispositivo iOS al Mac e impostare lo **schema attivo** sul dispositivo iOS.

![Selezionare il dispositivo](./media/spatial-anchors-unity/select-device.png)

Selezionare **Build and then run the current scheme** (Compila e quindi esegui lo schema corrente).

![Distribuire ed eseguire](./media/spatial-anchors-unity/deploy-run.png)
