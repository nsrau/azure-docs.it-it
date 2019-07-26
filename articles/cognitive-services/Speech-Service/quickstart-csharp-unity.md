---
title: 'Guida introduttiva: Riconoscimento vocale, Unity - Servizi Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione di riconoscimento vocale con Unity e Speech SDK per Unity (beta). Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 425cacb22865e64a996c653477120a5f7f410c9a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405946"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Guida introduttiva: Riconoscimento vocale con Speech SDK per Unity (beta)

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Usare questa guida per creare un'applicazione di riconoscimento vocale con [Unity](https://unity3d.com/) e Speech SDK per Unity (beta).
Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
Se non si ha familiarità con Unity, è consigliabile esaminare il [manuale dell'utente di Unity](https://docs.unity3d.com/Manual/UnityManual.html) prima di iniziare lo sviluppo dell'applicazione.

> [!NOTE]
> Speech SDK per Unity è attualmente in versione beta.
> Supporta Windows Desktop (x86 e x64) o la piattaforma UWP (Universal Windows Platform) (x86, x64, ARM/ARM64) e Android (x86, ARM32/64).

## <a name="prerequisites"></a>Prerequisiti

Per completare questo progetto, saranno necessari:

* [Unity 2018.3 o versioni successive](https://store.unity.com/) con [Unity 2019.1 che aggiunge il supporto per la piattaforma UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Per il supporto di ARM64, installare gli [strumenti di compilazione facoltativi per ARM64 e Windows 10 SDK per ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)
* Una chiave di sottoscrizione per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Accesso al microfono del computer

## <a name="create-a-unity-project"></a>Creare un progetto Unity

* Avviare Unity e nella scheda **Projects** (Progetti) selezionare **New** (Nuovo).
* Specificare **csharp-unity** in **Project name** (Nome progetto) e **3D** in **Template** (Modello) e selezionare una posizione.
  Selezionare quindi **Create project** (Crea progetto).
* Dopo un po' di tempo verrà visualizzata la finestra dell'editor di Unity.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Speech SDK per Unity (beta) è incluso in un pacchetto di asset Unity, con estensione unitypackage.
  Scaricarla [qui](https://aka.ms/csspeech/unitypackage).
* Importare Speech SDK selezionando **Assets** (Asset) > **Import Package** (Importa pacchetto) > **Custom Package** (Pacchetto personalizzato).
  Per informazioni dettagliate, vedere la [documentazione di Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* Nel selettore file selezionare il file con estensione unitypackage di Speech SDK scaricato in precedenza.
* Verificare che tutti i file siano selezionati e fare clic sul pulsante **Import** (Importa):

  ![Screenshot dell'editor di Unity durante l'importazione del pacchetto di asset Unity di Speech SDK](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Aggiungere l'interfaccia utente

Si aggiunge alla scena un'interfaccia utente minima, costituita da un pulsante per attivare il riconoscimento vocale e un campo di testo per visualizzare il risultato.

* La [finestra della gerarchia](https://docs.unity3d.com/Manual/Hierarchy.html), che per impostazione predefinita si trova a sinistra, contiene una scena di esempio creata da Unity con il nuovo progetto.
* Fare clic sul pulsante **Create** (Crea) nella parte superiore della finestra della gerarchia e selezionare **UI** (Interfaccia utente) > **Button** (Pulsante).
* Verranno così creati tre oggetti di gioco, visualizzati nella finestra della gerarchia: un oggetto **Button** annidato in un oggetto **Canvas** e un oggetto **EventSystem**.
* [Spostarsi nella visualizzazione della scena](https://docs.unity3d.com/Manual/SceneViewNavigation.html), in modo da poter esaminare il canvas e il pulsante nella [visualizzazione della scena](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Fare clic sull'oggetto **Button** nella finestra della gerarchia per visualizzarne le impostazioni nella [finestra di controllo](https://docs.unity3d.com/Manual/UsingTheInspector.html), che per impostazione predefinita si trova a destra.
* Impostare le proprietà **Pos X** e **Pos Y** su **0**, affinché il pulsante sia posizionato al centro del canvas.
* Fare di nuovo clic sul pulsante **Create** (Crea) nella parte superiore della finestra della gerarchia e selezionare **UI** (Interfaccia utente) > **Text** (Testo).
* Fare clic sull'oggetto **Text** nella finestra della gerarchia per visualizzarne le impostazioni nella [finestra di controllo](https://docs.unity3d.com/Manual/UsingTheInspector.html), che per impostazione predefinita si trova a destra.
* Impostare le proprietà **Pos X** e **Pos Y** su **0** e **120** e le proprietà **Width** (Larghezza) e **Height** (Altezza) su **240** e **120** affinché il campo di testo e il pulsante non si sovrappongano.

Al termine, l'interfaccia utente dovrebbe essere simile allo screenshot seguente:

[![Screenshot dell'interfaccia utente di questa guida introduttiva nell'editor di Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Nella [finestra del progetto](https://docs.unity3d.com/Manual/ProjectView.html), che per impostazione predefinita si trova in basso a sinistra, fare clic sul pulsante **Create** (Crea) e quindi selezionare **C# script** (Script C#). Assegnare il nome `HelloWorld` allo script.

1. Modificare lo script facendo doppio clic su di esso.

   > [!NOTE]
   > È possibile configurare l'editor di codice da avviare in **Edit** (Modifica) > **Preferences** (Preferenze). Vedere il [manuale dell'utente di Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Sostituire tutto il codice con quanto riportato di seguito:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Individuare e sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione dei servizi Voce.

1. Individuare e sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di valutazione gratuita.

1. Salvare le modifiche allo script.

1. Nell'editor di Unity è necessario aggiungere lo script come componente a uno degli oggetti di gioco.

   * Fare clic sull'oggetto **Canvas** nella finestra della gerarchia. L'impostazione verrà visualizzata nella [finestra di controllo](https://docs.unity3d.com/Manual/UsingTheInspector.html), che per impostazione predefinita si trova a destra.
   * Fare clic sul pulsante **Add Component** (Aggiungi componente) nella finestra di controllo e quindi cercare lo script HelloWorld creato in precedenza e aggiungerlo.
   * Si noti che il componente Hello World ha due proprietà non inizializzate, **Output Text** (Testo di output) e **Start Reco Button** (Pulsante di avvio riconoscimento), che corrispondono alle proprietà pubbliche della classe `HelloWorld`.
     Per collegarle, fare clic sul selettore oggetti (icona a forma di piccolo cerchio a destra della proprietà) e scegliere gli oggetti testo e pulsante creati in precedenza.

     > [!NOTE]
     > Il pulsante include anche un oggetto testo annidato. Assicurarsi di non selezionarlo accidentalmente per l'output di testo oppure rinominare uno degli oggetti testo usando il campo Name (Nome) nella finestra di controllo per evitare di confondersi.

## <a name="run-the-application-in-the-unity-editor"></a>Eseguire l'applicazione nell'editor di Unity

* Fare clic sul pulsante **Play** (Riproduci) sulla barra degli strumenti dell'editor di Unity, sotto la barra dei menu.

* Dopo l'avvio dell'app, fare clic sul pulsante e pronunciare una frase o un'espressione in inglese nel microfono del computer. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto come testo, che appare nella finestra.

  [![Screenshot della guida introduttiva in esecuzione nella finestra del gioco in Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Verificare la presenza di messaggi di debug nella [finestra Console](https://docs.unity3d.com/Manual/Console.html).

* Al termine del riconoscimento vocale, fare clic sul pulsante **Play** (Riproduci) sulla barra degli strumenti dell'editor di Unity per arrestare l'app.

## <a name="additional-options-to-run-this-application"></a>Altre opzioni per l'esecuzione dell'applicazione

Questa applicazione può essere distribuita anche in Android, come app autonoma di Windows o applicazione UWP.
Per una descrizione della configurazione di queste destinazioni aggiuntive, vedere il [repository di esempi](https://aka.ms/csspeech/samples) nella cartella quickstart/csharp-unity.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
