---
title: Modello ONNX del Servizio visione artificiale personalizzato con Windows ML - Servizi cognitivi | Microsoft Docs
description: Informazioni su come creare un'app UWP di Windows che usa un modello ONNX esportato da Servizi cognitivi.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939433"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Esercitazione: usare un modello ONNX del Servizio visione artificiale personalizzato con Windows ML (anteprima)

Informazioni su come usare un modello ONNX esportato dal Servizio visione artificiale personalizzato con Windows ML (anteprima).

Le informazioni contenute in questo documento illustrano come usare un file ONNX esportato dal Servizio visione artificiale personalizzato con Windows ML. Viene fornito un esempio di applicazione UWP Windows. Un modello con training che possa riconoscere cani e gatti viene fornito con l'esempio. Sono inoltre disponibili i passaggi per usare il modello personalizzato con questo esempio.

> [!div class="checklist"]
> * Informazioni sull'app di esempio
> * Ottenere il codice di esempio
> * Eseguire l'esempio
> * Usare il modello personalizzato

## <a name="prerequisites"></a>prerequisiti

* Un dispositivo Windows 10 con:

    * Una fotocamera.

    * Visual Studio 2017 15.7 o versioni successive con il carico di lavoro di __sviluppo UWP__ abilitato.

    * Modalità di sviluppo abilitata. Per altre informazioni, vedere il documento [Abilitare il dispositivo per lo sviluppo](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

* (Facoltativo) Un file ONNX esportato dal Servizio visione artificiale personalizzato. Per altre informazioni, vedere il documento [Esportare il modello per l'uso con i dispositivi mobili](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

    > [!NOTE]
    > Per usare il modello personalizzato, seguire la procedura descritta nella sezione [Usare il modello personalizzato](#use-your-own-model).

## <a name="about-the-example-app"></a>Informazioni sull'app di esempio

L'applicazione è un'applicazione UWP Windows generica. Usa la fotocamera sul dispositivo Windows 10 per fornire immagini al modello. I tag e i punteggi restituiti dal modello vengono visualizzati sotto l'anteprima video.

* Man mano che i dati vengono generati dalla fotocamera [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) estrae i singoli fotogrammi. I fotogrammi vengono inviati al modello per il punteggio.

* Il modello restituisce i tag su cui ha eseguito il training e un valore float che indica il livello di certezza che l'immagine contenga l'elemento.

### <a name="the-ui"></a>L'interfaccia utente

L'interfaccia utente per l'applicazione di esempio viene creata usando controlli __CaptureElement__ e __TextBlock__. CaptureElement visualizza un'anteprima del video della fotocamera e TextBlock visualizza i risultati restituiti dal modello. 

### <a name="the-model"></a>Il modello

Il modello (`cat-or-dog.onnx`) fornito con l'esempio è stato creato e sottoposto a training mediante il Servizio visione artificiale personalizzato di Servizi cognitivi. Il modello con training è stato esportato come modello ONNX. Per altre informazioni sull'uso di questo servizio, vedere i documenti [How to build a classifier](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) (Come creare un classificatore) ed [Esportare il modello per l'uso con i dispositivi mobili](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

> [!IMPORTANT]
> Il modello fornito con l'esempio è stato sottoposto a training con un set ridotti di immagini di cani e gatti, quindi potrebbe non essere lo strumento migliore al mondo per il loro riconoscimento.

### <a name="the-model-class-file"></a>Il file di classe del modello

Quando si aggiunge un file ONNX a un'applicazione UWP di Windows, viene creato un file con estensione cs. Questo file ha lo stesso nome del file `.onnx` (`cat-or-dog` in questo esempio) e contiene le classi usate per lavorare con il modello da C#. Tuttavia, le entità nella classe generata possono avere nomi come `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. È possibile rinominare in modo sicuro queste voci (fare clic con il pulsante destro e rinominare) con nomi descrittivi.

> [!NOTE]
> L'esempio di codice ha effettuato il refactoring dei nomi delle classi e dei metodi generati come segue:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Accesso alla fotocamera

La scheda __Capabilities__ nel file `Package.appxmanifest`è configurata per consentire l'accesso alla webcam e al microfono.

> [!NOTE]
> Anche se questo esempio non usa l'audio, è stato necessario abilitare il microfono prima di poter accedere alla fotocamera del dispositivo.

L'applicazione tenta di ottenere la fotocamera nella parte posteriore del dispositivo, se disponibile. Usa la classe [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) per avviare l'acquisizione del video dalla fotocamera. [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) consente di acquisire i fotogrammi video e inviarli al modello.

## <a name="get-the-example-code"></a>Ottenere il codice di esempio

L'applicazione di esempio è disponibile all'indirizzo [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Eseguire l'esempio

1. Usare il tasto `F5` per avviare l'applicazione da Visual Studio. Potrebbe essere richiesto di abilitare la modalità di sviluppo. Per altre informazioni, vedere il documento [Abilitare il dispositivo per lo sviluppo](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

2. Se richiesto, consentire all'applicazione di accedere alla fotocamera e al microfono del dispositivo.

3. Puntare la fotocamera verso un cane o un gatto. Il punteggio in merito al fatto che l'immagine contenga un cane o un gatto viene visualizzato sotto l'anteprima dell'applicazione.

    > [!TIP]
    > Se non si dispone di un cane o un gatto, è possibile usare una foto.

## <a name="use-your-own-model"></a>Usare il modello personalizzato

Per usare il modello personalizzato, eseguire la procedura seguente:

> [!IMPORTANT]
> I passaggi descritti in questa sezione consentono di rinominare il modello corrente (cat-or-dog.cs) ed effettuare il refactoring dei nomi delle classi e dei metodi del nuovo modello, al fine di evitare conflitti di denominazione con il modello di esempio.

1. Eseguire il training mediante il Servizio visione artificiale personalizzato. Per informazioni su come eseguire il training di un modello, vedere il documento [How to build a classifier](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) (Come creare un classificatore).

2. Esportare il modello con training come modello ONNX. Per informazioni su come esportare un modello, vedere il documento [Esportare il modello per l'uso con i dispositivi mobili](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

3. In Esplora soluzioni fare doppio clic sul file __cat-or-dog.cs__ e rinominarlo in __cat-or-dog.txt__. La ridenominazione evita conflitti con il nuovo modello.

    > [!TIP]
    > È anche possibile usare nomi diversi per i nomi delle classi nel nuovo modello, ma è più semplice riusare nomi esistenti.

4. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla voce __VisionApp__ e scegliere __Aggiungi__ > __Elemento esistente...__

5. Per generare una classe per il modello, selezionare il file ONNX da importare e scegliere il pulsante __Aggiungi__. In Esplora soluzioni viene aggiunta una nuova classe con lo stesso nome del file ONNX (ma con estensione `.cs`).

6. Aprire il file .cs generato e trovare i nomi degli elementi seguenti:

    > [!IMPORTANT]
    > Usare il file `cat-or-dog.txt` di esempio per riconoscere le classi e funzioni.

    * La classe che definisce l'input del modello. Il nome generato può essere simile a `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Rinominare la classe in __ModelInput__.
    * La classe che definisce l'output del modello. Il nome generato può essere simile a `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Rinominare la classe in __ModelOutput__.
    * La classe che definisce il modello. Il nome generato può essere simile a `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Rinominare la classe in __Model__.
    * Il metodo che crea il modello. Il nome generato può essere simile a `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Rinominare il metodo in __CreateModel__.

7. In Esplora soluzioni spostare il file `.onnx` nella cartella __Risorse__. 

8. Per includere il file ONNX nel pacchetto dell'applicazione, selezionare il file `.onnx` e impostare __Azione di compilazione__ su __Contenuto__ nelle proprietà.

9. Aprire il file __MainPage.xaml.cs__. Trovare la riga seguente e modificare il nome del file nel nuovo file `.onnx`:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Questa modifica carica il nuovo modello in fase di esecuzione.

10. Compilare ed eseguire l'app. Ora esegue il nuovo modello per assegnare punteggi alle immagini.

## <a name="next-steps"></a>Passaggi successivi

Per scoprire altri modi per esportare e usare un modello del Servizio visione artificiale personalizzato, vedere i documenti seguenti:

* [Esportare il modello](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Use exported Tensorflow model in an Android application](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) (Usare il modello Tensorflow esportato in un'applicazione Android)
* [Use exported CoreML model in a Swift iOS application](https://go.microsoft.com/fwlink/?linkid=857726) (Usare il modello CoreML esportato in un'applicazione iOS Swift)
* [Use exported CoreML model in an iOS application with Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) (Usare il modello CoreML esportato in un'applicazione iOS con Xamarin)

Per altre informazioni sull'uso di modelli ONNX con Windows ML, vedere il documento [Integrare un modello nella tua app con Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model).
