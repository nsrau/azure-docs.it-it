---
title: 'Esercitazione: Esempio di IoT Visual Alerts'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si userà Visione personalizzata con un dispositivo IoT per riconoscere e segnalare gli stati visivi rilevati dal feed video di una fotocamera.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: pafarley
ms.openlocfilehash: d146c264ebc2d36f0842f464f4547520546fd363
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888282"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Esercitazione: Usare Visione personalizzata con un dispositivo IoT per segnalare gli stati visivi

Questa app di esempio illustra come usare Visione personalizzata per eseguire il training di un dispositivo dotato di una fotocamera per rilevare gli stati visivi. È possibile eseguire questo scenario di rilevamento su un dispositivo IoT usando un modello ONNX esportato dal servizio Visione personalizzata.

Uno stato visivo descrive il contenuto di un'immagine, ovvero una stanza vuota o una stanza con persone, una strada vuota o una strada con un camion e così via. Nell'immagine seguente è possibile osservare che l'app rileva quando una banana o una mela viene posizionata davanti alla fotocamera.

![Animazione di un'interfaccia utente che assegna un'etichetta alla frutta posizionata davanti alla fotocamera](./media/iot-visual-alerts-tutorial/scoring.gif)

In questa esercitazione verranno illustrate le attività seguenti:
> [!div class="checklist"]
> * Configurare l'app di esempio in modo da usare le risorse di Visione personalizzata e dell'hub IoT.
> * Usare l'app per eseguire il training del progetto di Visione personalizzata.
> * Usare l'app per assegnare punteggi alle nuove immagini in tempo reale e inviare i risultati ad Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Il progetto deve consistere in una classificazione di immagini di tipo **Compact**, perché successivamente il modello verrà esportato in ONNX.
* Sarà inoltre necessario [creare una risorsa dell'hub IoT](https://ms.portal.azure.com/#create/Microsoft.IotHub) in Azure.
* [Visual Studio 2015 o versioni successive](https://www.visualstudio.com/downloads/)
* Facoltativamente, un dispositivo IoT che esegue Windows 10 IoT Core versione 17763 o successiva. È anche possibile eseguire l'app direttamente dal PC.
   * Per Raspberry Pi 2 e 3, è possibile configurare Windows 10 direttamente dall'app Dashboard IoT. Per altri dispositivi, ad esempio DrangonBoard, è necessario eseguire il flashing del sistema operativo usando il [metodo eMMC](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Per informazioni sulla configurazione di un nuovo dispositivo, vedere [Configurazione del dispositivo](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) nella documentazione di Windows IoT.

## <a name="about-the-visual-alerts-app"></a>Informazioni sull'app Visual Alerts

L'app IoT Visual Alerts viene eseguita a ciclo continuo e passa attraverso quattro stati diversi, in base alle esigenze:

* **No model** (Nessun modello): l'app non è in stato operativo. L'app va continuamente in sospensione per un secondo e controlla la fotocamera.
* **Capturing Training Images** (Acquisizione delle immagini di training): in questo stato, l'app acquisisce un'immagine e la carica come immagine di training nel progetto di destinazione di Visione personalizzata. L'app va quindi in sospensione per 500 ms e ripete l'operazione fino a quando non viene acquisito il numero di immagini preimpostato, quindi attiva il training del modello di Visione personalizzata.
* **Waiting For Trained Model** (In attesa del modello sottoposto a training): in questo stato, l'app chiama l'API Visione personalizzata ogni secondo per verificare se il progetto di destinazione contiene un'iterazione sottoposta a training. Quando ne trova una, scarica il modello ONNX corrispondente in un file locale e passa allo stato **Scoring** (Assegnazione di punteggio).
* **Scoring** (Assegnazione di punteggio): in questo stato, l'app usa Windows ML per valutare un singolo fotogramma della fotocamera rispetto al modello ONNX locale. La classificazione dell'immagine risultante viene visualizzata sullo schermo e inviata come messaggio all'hub IoT. L'app quindi va in sospensione per un secondo prima di assegnare un punteggio a una nuova immagine.

## <a name="understand-the-code-structure"></a>Informazioni sulla struttura del codice

I file seguenti gestiscono la modalità di funzionamento principale dell'app.

| File | DESCRIZIONE |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Questo file definisce l'interfaccia utente XAML. Ospita il controllo della fotocamera Web e contiene le etichette usate per gli aggiornamenti dello stato.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Questo codice controlla il comportamento dell'interfaccia utente XAML. Contiene il codice di elaborazione degli stati.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Questa classe è un wrapper che gestisce l'integrazione con il servizio Visione personalizzata.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Questa classe è un wrapper che gestisce l'integrazione con Windows ML per il caricamento del modello ONNX e l'assegnazione di punteggi rispetto a tale modello.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Questa classe è un wrapper che gestisce l'integrazione con l'hub IoT per caricare i risultati dei punteggi in Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Configurare l'app Visual Alerts

Seguire questa procedura per consentire l'esecuzione dell'app IoT Visual Alerts sul proprio PC o dispositivo IoT.

1. Clonare o scaricare l'[esempio di IoTVisualAlerts](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) su GitHub.
1. Aprire la soluzione _IoTVisualAlerts.sln_ in Visual Studio.
1. Integrare il progetto di Visione personalizzata:
    1. Nello script _CustomVision\CustomVisionServiceWrapper.cs_ aggiornare la variabile `ApiKey` con la propria chiave di training.
    1. Aggiornare quindi la variabile `Endpoint` con l'URL dell'endpoint associato alla chiave.
    1. Aggiornare la variabile `targetCVSProjectGuid` con l'ID corrispondente del progetto di Visione personalizzata che si vuole usare. 
1. Configurare la risorsa dell'hub IoT:
    1. Nello script _IoTHub\IotHubWrapper.cs_ aggiornare la variabile `s_connectionString` con la stringa di connessione appropriata per il dispositivo usato. 
    1. Nel portale di Azure caricare l'istanza dell'hub IoT, fare clic su **Dispositivi IoT** in **Strumenti di esplorazione**, selezionare il proprio dispositivo di destinazione (o crearne uno, se necessario) e trovare la stringa di connessione in **Stringa di connessione primaria**. La stringa conterrà il nome dell'hub IoT, l'ID del dispositivo e la chiave di accesso condiviso, nel formato seguente: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`.

## <a name="run-the-app"></a>Esecuzione dell'app

Se si esegue l'app sul PC, selezionare **Computer locale** come dispositivo di destinazione in Visual Studio e impostare **x64** o **x86** come piattaforma di destinazione. Premere quindi F5 per eseguire il programma. L'app verrà avviata e visualizzerà il feed live della fotocamera e un messaggio di stato.

Se si distribuisce l'app in un dispositivo IoT con un processore ARM, è necessario selezionare **ARM** come piattaforma di destinazione e **Computer remoto** come dispositivo di destinazione. Specificare l'indirizzo IP del dispositivo quando richiesto (deve trovarsi nella stessa rete del PC). È possibile ottenere l'indirizzo IP dall'app predefinita di Windows IoT non appena si avvia il dispositivo e si connette il dispositivo alla rete. Premere F5 per eseguire il programma.

Quando viene eseguita per la prima volta, l'app non avrà alcuna conoscenza degli stati visivi. Verrà visualizzato un messaggio di stato per segnalare che non è disponibile alcun modello. 

## <a name="capture-training-images"></a>Acquisire le immagini di training

Per configurare un modello, è necessario impostare l'app sullo stato **Capturing Training Images** (Acquisizione delle immagini di training). Eseguire uno dei passaggi seguenti:
* Se si esegue l'app sul PC, usare il pulsante nell'angolo in alto a destra dell'interfaccia utente.
* Se si esegue l'app su un dispositivo IoT, chiamare il metodo `EnterLearningMode` sul dispositivo tramite l'hub IoT. Il metodo può essere chiamato tramite la voce corrispondente al dispositivo nel menu dell'hub IoT nel portale di Azure oppure con uno strumento, ad esempio [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
 
Quando l'app passa allo stato **Capturing Training Images** (Acquisizione delle immagini di training), acquisisce circa due immagini al secondo fino a raggiungere il numero di immagini preimpostato. Per impostazione predefinita, vengono acquisite 30 immagini, ma è possibile impostare questo parametro passando il numero desiderato come argomento al metodo `EnterLearningMode` dell'hub IoT. 

Mentre l'app acquisisce immagini, è necessario esporre la fotocamera ai tipi di stati visivi da rilevare, ad esempio una stanza vuota, una stanza con persone, una scrivania vuota, una scrivania con un camion giocattolo e così via.

## <a name="train-the-custom-vision-model"></a>Eseguire il training del modello di Visione personalizzata

Dopo aver completato il processo di acquisizione, l'app caricherà le immagini e passerà allo stato **Waiting For Trained Model** (In attesa del modello sottoposto a training). A questo punto, è necessario accedere al [portale di Visione personalizzata](https://www.customvision.ai/) e creare un modello basato sulle nuove immagini di training. L'animazione seguente mostra un esempio di questo processo.

![Animazione: assegnazione di tag a più immagini di banane](./media/iot-visual-alerts-tutorial/labeling.gif)

Per ripetere questo processo con il proprio scenario:

1. Accedere al [portale di Visione personalizzata](http://customvision.ai).
1. Trovare il progetto di destinazione, che a questo punto dovrebbe avere tutte le immagini di training caricate dall'app.
1. Per ogni stato visivo che si vuole identificare, selezionare le immagini appropriate e applicare manualmente il tag.
    * Se, ad esempio, l'obiettivo è distinguere tra una stanza vuota e una stanza con persone, è consigliabile contrassegnare cinque o più immagini con persone come una nuova classe, **People**, e altre cinque o più immagini senza persone con il tag **Negative**. In questo modo si aiuterà il modello a distinguere tra i due diversi stati.
    * Sempre a titolo di esempio, se si vuole identificare approssimativamente quanto è pieno uno scaffale, è possibile usare tag specifici come **EmptyShelf**, **PartiallyFullShelf** e **FullShelf**.
1. Al termine, selezionare il pulsante **Train** (Avvia training).
1. Al termine del training, l'app rileverà che è disponibile un'iterazione sottoposta a training. Avvierà quindi il processo di esportazione del modello sottoposto a training in ONNX e di download del modello nel dispositivo.

## <a name="use-the-trained-model"></a>Usare il modello sottoposto a training

Dopo aver scaricato il modello sottoposto a training, l'app passerà allo stato **Scoring** (Assegnazione di punteggio) e inizierà ad assegnare punteggi alle immagini della fotocamera in modalità a ciclo continuo.

Per ogni immagine acquisita, l'app visualizzerà il tag più rilevante sullo schermo. Se non è in grado di riconoscere lo stato visivo, restituirà il messaggio **No Matches** (Nessuna corrispondenza). L'app invia anche questi messaggi all'hub IoT e, se viene rilevata una classe, il messaggio includerà l'etichetta, il punteggio di attendibilità e una proprietà denominata `detectedClassAlert`, che può essere usata dai client dell'hub IoT interessati all'inoltro rapido di messaggi in base a proprietà.

L'esempio usa inoltre una [libreria Sense HAT](https://github.com/emmellsoft/RPi.SenseHat) per rilevare quando l'app viene eseguita in un dispositivo Raspberry Pi con un'unità Sense HAT, in modo da poter usare questa unità come visualizzazione dell'output, impostando tutte le luci su rosso ogni volta che viene rilevata una classe e sullo stato disattivato quando non viene rilevata alcuna corrispondenza.

## <a name="reuse-the-app"></a>Riutilizzare l'app

Se si vuole ripristinare lo stato originale dell'app, è possibile fare clic sul pulsante nell'angolo in alto a destro dell'interfaccia utente oppure richiamare il metodo `DeleteCurrentModel` tramite l'hub IoT.

In qualsiasi momento, è possibile ripetere il passaggio del caricamento di immagini di training facendo clic sul pulsante in alto a destra dell'interfaccia utente o chiamando il metodo `EnterLearningMode` una seconda volta.

Se si esegue l'app su un dispositivo ed è necessario recuperare nuovamente l'indirizzo IP, ad esempio per stabilire una connessione remota tramite il [client remoto di Windows IoT](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab), è possibile chiamare il metodo `GetIpAddress` tramite l'hub IoT.

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il progetto di Visione personalizzata se non si vuole più gestirlo. Nel [sito Web di Visione personalizzata](https://customvision.ai) passare a **Projects** (Progetti) e selezionare l'icona del cestino sotto il nuovo progetto.

![Screenshot del riquadro My New Project con l'icona del Cestino](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata ed eseguita un'applicazione che rileva le informazioni sullo stato visivo in un dispositivo IoT e invia i risultati all'hub IoT. A questo punto è possibile esaminare più in dettaglio il codice sorgente o apportare una delle modifiche suggerite di seguito.

> [!div class="nextstepaction"]
> [Esempio di IoTVisualAlerts (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Aggiungere un metodo dell'hub IoT per consentire all'app di passare direttamente allo stato **Waiting For Trained Model** (In attesa del modello sottoposto a training). In questo modo, è possibile eseguire il training del modello con immagini che non vengono acquisite dal dispositivo stesso e quindi eseguire il push del nuovo modello nel dispositivo quando necessario.
* Seguire l'esercitazione [Visualizzare i dati dei sensori in tempo reale](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) per creare un dashboard di Power BI per visualizzare gli avvisi dell'hub IoT inviati dall'esempio.
* Seguire l'esercitazione [Monitoraggio remoto di IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) per creare un'app per la logica che risponda agli avvisi dell'hub IoT quando vengono rilevati determinati stati visivi.
