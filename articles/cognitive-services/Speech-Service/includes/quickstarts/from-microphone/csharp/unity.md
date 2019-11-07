---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, C# (Unity) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: babc6b1c0b6207d404091db3117f95f6d71c53ac
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505556"
---
> [!NOTE]
> Speech SDK per Unity supporta Windows Desktop (x86 e x64) o la piattaforma UWP (Universal Windows Platform) (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (simulatore x64, ARM32 e ARM64).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=unity) se non è già stato fatto. Ecco come procedere.

## <a name="create-a-unity-project"></a>Creare un progetto Unity

1. Aprire Unity. Se si usa Unity per la prima volta, viene visualizzata la finestra **Unity Hub** (Hub Unity) *<version number>* . È anche possibile aprire Unity Hub direttamente per accedere a questa finestra.

   [![Finestra Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selezionare **Nuovo**. Viene visualizzata la finestra **Create a new project with Unity** (Crea un nuovo progetto con Unity) *<version number>* .

   [![Creare un nuovo progetto in Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. In **Project Name** (Nome progetto) immettere **csharp-unity**.
1. In **Templates** (Modelli) selezionare **3D**, se non è già selezionato.
1. In **Location** (Percorso) selezionare o creare una cartella in cui salvare il progetto.
1. Selezionare **Create** (Crea).

Dopo qualche istante verrà visualizzata la finestra dell'editor di Unity.



## <a name="add-ui"></a>Aggiungere l'interfaccia utente

A questo punto verrà aggiunta un'interfaccia utente minima, costituita da un pulsante per attivare il riconoscimento vocale e un campo di testo per visualizzare il risultato. La [finestra **Hierarchy**](https://docs.unity3d.com/Manual/Hierarchy.html) (Gerarchia) contiene una scena di esempio creata da Unity con il nuovo progetto.

1. Nella parte superiore della finestra **Hierarchy** (Gerarchia) selezionare **Create** > **UI** > **Button** (Crea > Interfaccia utente > Pulsante).

   Questa azione crea tre oggetti di gioco, visualizzati nella finestra **Hierarchy** (Gerarchia): un oggetto **Button**, un oggetto **Canvas** che contiene il pulsante e un oggetto **EventSystem**.

   [![Ambiente dell'editor di Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Spostarsi nella visualizzazione **Scene**](https://docs.unity3d.com/Manual/SceneViewNavigation.html) (Scena), in modo da poter esaminare il canvas e il pulsante nella [visualizzazione **Scene**](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Nella [finestra **Inspector**](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Controllo), sul lato destro per impostazione predefinita, impostare le proprietà **Pos X** e **Pos Y** su **0**, in modo che il pulsante sia posizionato al centro del canvas.

1. Nella finestra **Hierarchy** (Gerarchia) selezionare **Create** > **UI** > **Text** (Crea > Interfaccia utente > Testo) per creare un oggetto **Text**.

1. Nella finestra **Inspector** (Controllo) impostare le proprietà **Pos X** e **Pos Y** su **0** e **120**, e le proprietà **Width** (Larghezza) e **Height** (Altezza) su **240** e **120**. Questi valori assicurano che il campo di testo e il pulsante non si sovrappongano.

Al termine, la visualizzazione **Scene** dovrebbe essere simile allo screenshot seguente:

[![Visualizzazione Scene nell'editor di Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

Per aggiungere il codice di script di esempio per il progetto Unity, seguire questa procedura:

1. Nella [finestra Project](https://docs.unity3d.com/Manual/ProjectView.html) (Progetto) selezionare **Create** > **C# script** (Crea > Script C#) per aggiungere un nuovo script C#.

   [![Finestra Project nell'editor di Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Assegnare il nome `HelloWorld` allo script.

1. Fare doppio clic su `HelloWorld` per modificare lo script appena creato.

   > [!NOTE]
   > Per configurare l'editor di codice che verrà usato da Unity per la modifica, selezionare **Edit** > **Preferences** (Modifica > Preferenze) e quindi passare alle preferenze per **External Tools** (Strumenti esterni). Per altre informazioni, vedere il [manuale dell'utente di Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Sostituire lo script esistente con il codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Trovare e sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione dei servizi Voce.

1. Trovare e sostituire la stringa `YourServiceRegion` con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di valutazione gratuita.

1. Salvare le modifiche allo script.

A questo punto, tornare all'editor di Unity e aggiungere lo script come componente a uno degli oggetti di gioco:

1. Nella finestra **Hierarchy** (Gerarchia) selezionare l'oggetto **Canvas**.

1. Nella finestra **Inspector** (Controllo) selezionare il pulsante **Add Component** (Aggiungi componente).

   [![Finestra Inspector nell'editor di Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Nell'elenco a discesa cercare lo script `HelloWorld` creato in precedenza e aggiungerlo. Nella finestra **Inspector** (Controllo) viene visualizzata una sezione **Hello World (Script)** che contiene due proprietà non inizializzate, **Output Text** (Testo di output) e **Start Reco Button** (Pulsante Avvia registrazione). Queste proprietà dei componenti Unity corrispondono a proprietà pubbliche della classe `HelloWorld`.

1. Selezionare il selettore oggetti della proprietà **Start Reco Button** (icona a forma di piccolo cerchio a destra della proprietà) e scegliere l'oggetto **Button** creato in precedenza.

1. Selezionare il selettore oggetti della proprietà **Output Text** e scegliere l'oggetto **Text** creato in precedenza.

   > [!NOTE]
   > Il pulsante include anche un oggetto testo annidato. Prestare attenzione a non selezionarlo accidentalmente per l'output di testo oppure rinominare uno degli oggetti testo usando il campo **Name** (Nome) nella finestra **Inspector** (Controllo) per evitare di confondersi.

## <a name="run-the-application-in-the-unity-editor"></a>Eseguire l'applicazione nell'editor di Unity

A questo punto è possibile eseguire l'applicazione nell'editor di Unity.

1. Sulla barra degli strumenti dell'editor di Unity, sotto la barra dei menu, selezionare il pulsante **Play** (Riproduci), un triangolo rivolto verso destra.

1. Passare alla [visualizzazione **Game**](https://docs.unity3d.com/Manual/GameView.html) (Gioco) e attendere che l'oggetto **Text** visualizzi **Click button to recognize speech** (Fare clic sul pulsante per riconoscere la voce). L'oggetto visualizza **New Text** (Nuovo testo) quando l'applicazione non è stata avviata o non è pronta per rispondere.

1. Selezionare il pulsante e pronunciare una frase o un'espressione in inglese nel microfono del computer. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto come testo, che compare nella visualizzazione **Game** (Gioco).

   [![Visualizzazione Game nell'editor di Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Verificare la presenza di messaggi di debug nella [finestra **Console**](https://docs.unity3d.com/Manual/Console.html). Se la finestra **Console** non è visualizzata, passare alla barra dei menu e selezionare **Window** > **General** > **Console** (Finestra > Generale > Console) per visualizzarla.

1. Al termine del riconoscimento vocale, selezionare il pulsante **Play** (Riproduci) sulla barra degli strumenti dell'editor di Unity per arrestare l'applicazione.

## <a name="additional-options-to-run-this-application"></a>Altre opzioni per l'esecuzione dell'applicazione

Questa applicazione può essere distribuita anche come app Android, come app autonoma di Windows o come applicazione UWP.
Per altre informazioni, vedere il [repository degli esempi](https://aka.ms/csspeech/samples). La cartella `quickstart/csharp-unity` descrive la configurazione per queste destinazioni aggiuntive.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
