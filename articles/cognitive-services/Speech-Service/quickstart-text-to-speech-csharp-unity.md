---
title: 'Guida introduttiva: Sintesi vocale, Unity - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione di sintesi vocale con Unity e Speech SDK per Unity. Al termine, la sintesi vocale dal testo verrà eseguita in tempo reale nell'altoparlante del dispositivo.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: b4d329d9d3c2a259fb90b0278c54ba8590590995
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675491"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Guida introduttiva: Sintesi vocale con Speech SDK per Unity

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-csharp-unity.md).

Usare questa guida per creare un'applicazione di sintesi vocale con [Unity](https://unity3d.com/) e Speech SDK di Servizi cognitivi di Azure per Unity.
Al termine, la sintesi vocale dal testo verrà riprodotta in tempo reale dall'altoparlante del dispositivo.
Se non si ha familiarità con Unity, esaminare il [manuale dell'utente di Unity](https://docs.unity3d.com/Manual/UnityManual.html) prima di iniziare lo sviluppo dell'applicazione.

> [!NOTE]
> Unity supporta Windows Desktop (x86 e x64) o la piattaforma UWP (Universal Windows Platform) (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (simulatore x64, ARM32 e ARM64).

## <a name="prerequisites"></a>Prerequisiti

Per completare questo progetto, saranno necessari:

* [Unity 2018.3 o versioni successive](https://store.unity.com/) con [Unity 2019.1 che aggiunge il supporto per la piattaforma UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). È accettabile anche Visual Studio 2017 versione 15.9 o successive.
* Per il supporto di Windows ARM64, installare gli [strumenti di compilazione facoltativi per ARM64 e Windows 10 SDK per ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
* Una chiave di sottoscrizione per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-unity-project"></a>Creare un progetto Unity

* Avviare Unity e nella scheda **Projects** (Progetti) selezionare **New** (Nuovo).
* Specificare **csharp-unity** in **Project name** (Nome progetto) e **3D** in **Template** (Modello), quindi selezionare una posizione.
  Selezionare quindi **Create project** (Crea progetto).
* Dopo un po' di tempo verrà visualizzata la finestra dell'editor di Unity.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Speech SDK per Unity (beta) è incluso in un pacchetto di asset Unity, con estensione unitypackage. Scaricarlo da [questo sito Web](https://aka.ms/csspeech/unitypackage).
* Importare Speech SDK selezionando **Assets** (Asset) > **Import Package** (Importa pacchetto) > **Custom Package** (Pacchetto personalizzato). Per altre informazioni, vedere la [documentazione di Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* Nella selezione file selezionare il file con estensione unitypackage di Speech SDK scaricato.
* Verificare che tutti i file siano selezionati e quindi selezionare **Import** (Importa).

  ![Screenshot dell'editor di Unity durante l'importazione del pacchetto di asset Unity di Speech SDK](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-a-ui"></a>Aggiungere un'interfaccia utente

Aggiungere alla scena un'interfaccia utente minimalista, costituita da un campo di input in cui immettere il testo per la sintesi vocale, un pulsante per attivarla e un campo di testo per visualizzare il risultato.

* La [finestra della gerarchia](https://docs.unity3d.com/Manual/Hierarchy.html), che per impostazione predefinita si trova a sinistra, contiene una scena di esempio creata da Unity con il nuovo progetto.
* Selezionare il pulsante **Create** (Crea) nella parte superiore della finestra **Hierarchy** (Gerarchia) e quindi **UI** > **Input Field** (Interfaccia utente > Campo di input).
* Verranno così creati tre oggetti di gioco, visualizzati nella finestra **Hierarchy** (Gerarchia): un oggetto **Input Field** (Campo di input) annidato in un oggetto **Canvas** e un oggetto **EventSystem**.
* [Spostarsi nella visualizzazione della scena](https://docs.unity3d.com/Manual/SceneViewNavigation.html), in modo da poter esaminare il canvas e il campo di input nella [visualizzazione della scena](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Fare clic sull'oggetto **Input Field** (Campo di input) nella finestra **Hierarchy** (Gerarchia) per visualizzarne le impostazioni nella [finestra di controllo](https://docs.unity3d.com/Manual/UsingTheInspector.html), che per impostazione predefinita si trova a destra.
* Impostare le proprietà **Pos X** e **Pos Y** su **0**, affinché il campo di input sia posizionato al centro del canvas.
* Selezionare di nuovo il pulsante **Create** (Crea) nella parte superiore della finestra **Hierarchy** (Gerarchia). Selezionare **UI** > **Button** (Interfaccia utente > Pulsante) per creare un pulsante.
* Fare clic sull'oggetto **Button** (Pulsante) nella finestra **Hierarchy** (Gerarchia) per visualizzarne le impostazioni nella [finestra di controllo](https://docs.unity3d.com/Manual/UsingTheInspector.html), che per impostazione predefinita si trova a destra.
* Impostare le proprietà **Pos X** e **Pos Y** su **0** e **-48**. Impostare le proprietà **Width** (Larghezza) e **Height** (Altezza) su **160** e **30** affinché il pulsante e il campo di input non si sovrappongano.
* Selezionare di nuovo il pulsante **Create** (Crea) nella parte superiore della finestra **Hierarchy** (Gerarchia). Selezionare **UI** > **Text** (Interfaccia utente > Testo) per creare un campo di testo.
* Fare clic sull'oggetto **Text** nella finestra **Hierarchy** (Gerarchia) per visualizzarne le impostazioni nella [finestra di controllo](https://docs.unity3d.com/Manual/UsingTheInspector.html), che per impostazione predefinita si trova a destra.
* Impostare le proprietà **Pos X** e **Pos Y** su **0** e **80**. Impostare le proprietà **Width** (Larghezza) e **Height** (Altezza) su **320** e **80** affinché il campo di testo e il campo di input non si sovrappongano.
* Selezionare di nuovo il pulsante **Create** (Crea) nella parte superiore della finestra **Hierarchy** (Gerarchia). Selezionare **Audio** > **Audio Source** (Origine audio) per creare un'origine audio.

Al termine, l'interfaccia utente dovrebbe essere simile allo screenshot seguente:

[![Screenshot dell'interfaccia utente di questa guida introduttiva nell'editor di Unity](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Nella [finestra del progetto](https://docs.unity3d.com/Manual/ProjectView.html), che per impostazione predefinita si trova in basso a sinistra, fare clic sul pulsante **Create** (Crea) e quindi **C# script** (Script C#). Assegnare il nome `HelloWorld` allo script.

1. Modificare lo script facendo doppio clic su di esso.

   > [!NOTE]
   > Per configurare quale editor di codice viene avviato, selezionare **Edit** > **Preferences** (Modifica > Preferenze). Per altre informazioni, vedere il [manuale dell'utente di Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Sostituire tutto il codice con quanto riportato di seguito:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Individuare e sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione dei servizi Voce.

1. Individuare e sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di valutazione gratuita.

1. Salvare le modifiche allo script.

1. Tornare all'editor di Unity e aggiungere lo script come componente in uno degli oggetti di gioco.

   * Selezionare l'oggetto **Canvas** nella finestra **Hierarchy** (Gerarchia) per aprire l'impostazione nella [finestra di controllo](https://docs.unity3d.com/Manual/UsingTheInspector.html), che per impostazione predefinita si trova a destra.
   * Nella **finestra di controllo** selezionare il pulsante **Add Component** (Aggiungi componente). Quindi cercare lo script `HelloWorld` creato in precedenza e aggiungerlo.
   * Il componente Hello World ha quattro proprietà non inizializzate, **Output Text** (Testo di output), **Input Field** (Campo di input), **Speak Button** (Pulsante di pronuncia) e **Audio Source** (Origine audio), che corrispondono alle proprietà pubbliche della classe `HelloWorld`.
     Per collegarle tra loro, selezionare il selettore oggetti, ossia l'icona del cerchietto a destra della proprietà. Selezionare gli oggetti testo e pulsante creati in precedenza.

     > [!NOTE]
     > Il campo di input e il pulsante includono anche un oggetto testo annidato. Assicurarsi di non selezionare per sbaglio quello per l'output di testo. In alternativa, è possibile rinominare gli oggetti testo che usano il campo **Name** (Nome) della **finestra di controllo** per evitare confusione.

## <a name="run-the-application-in-the-unity-editor"></a>Eseguire l'applicazione nell'editor di Unity

* Selezionare il pulsante **Play** (Riproduci) sulla barra degli strumenti dell'editor di Unity, sotto la barra dei menu.
* Dopo aver avviato l'app, immettere alcune parole di testo nel campo di input e selezionare il pulsante. Il testo viene trasmesso al servizio Voce e sintetizzato in voce, che viene riprodotta dall'altoparlante.

  [![Screenshot dell'esecuzione dell'avvio rapido nella finestra del gioco in Unity](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Verificare la presenza di messaggi di debug nella [finestra Console](https://docs.unity3d.com/Manual/Console.html).
* Al termine della sintesi vocale, fare clic sul pulsante **Play** (Riproduci) sulla barra degli strumenti dell'editor di Unity per arrestare l'app.

## <a name="additional-options-to-run-this-application"></a>Altre opzioni per l'esecuzione dell'applicazione

Questa applicazione può essere distribuita anche in Android, come app autonoma di Windows o applicazione UWP.
Per una descrizione della configurazione di queste destinazioni aggiuntive, vedere il [repository di esempi](https://aka.ms/csspeech/samples) nella cartella quickstart/csharp-unity.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare i carattere voce](how-to-customize-voice-font.md)
- [Registrare esempi vocali](record-custom-voice-samples.md)
