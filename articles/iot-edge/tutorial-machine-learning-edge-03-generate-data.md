---
title: 'Esercitazione: Generare dati di dispositivi simulati - Machine Learning in Azure IoT Edge'
description: 'Esercitazione: Creare dispositivi virtuali che generano dati di telemetria simulati da usare in seguito per eseguire il training di un modello di Machine Learning.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 51d93e5b83d203f3fa99b69cc5f2877bbfdb6fb1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112860"
---
# <a name="tutorial-generate-simulated-device-data"></a>Esercitazione: Generare dati di dispositivi simulati

> [!NOTE]
> Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Se l'articolo è stato aperto direttamente, è consigliabile iniziare con il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie per risultati ottimali.

In questo articolo vengono usati dati di training di Machine Learning per simulare un dispositivo che invia dati di telemetria all'hub IoT. Come indicato nell'introduzione, in questa esercitazione completa viene usato il [set di dati di simulazione della riduzione delle prestazioni del motore Turbofan](https://c3.nasa.gov/dashlink/resources/139/) per simulare i dati di un set di motori di aero per il training e i test.

Il file readme.txt associato indica che:

* I dati sono costituiti da più serie temporali multivariate
* Ogni set di dati è diviso in sottoinsiemi di training e di test
* Ogni serie temporale riguarda un motore diverso
* Ogni motore parte con gradi diversi di usura iniziale e alcune variazioni di produzione

Per questa esercitazione viene usato il sottoinsieme di dati di training di un singolo set di dati (FD003).

Nella realtà, ogni motore sarebbe un dispositivo IoT indipendente. Presupponendo che non si abbia a disposizione un insieme di motori Turbofan connessi a Internet, verrà sviluppato un sostituto software per questi dispositivi.

Il simulatore è un programma in C# che usa le API dell'hub IoT per registrare i dispositivi virtuali a livello di codice con l'hub IoT. Vengono quindi letti i dati per ogni dispositivo dal sottoinsieme fornito dalla NASA e vengono inviati all'hub IoT tramite un dispositivo IoT simulato. Tutto il codice di questa parte dell'esercitazione è disponibile nella directory DeviceHarness del repository.

Il progetto DeviceHarness è un progetto .NET Core scritto in C# e costituito da quattro classi:

* **Program:** il punto di ingresso per l'esecuzione, che gestisce l'input dell'utente e il coordinamento complessivo.
* **TrainingFileManager:** legge e analizza il file di dati selezionato.
* **CycleData:** rappresenta una singola riga di dati di un file convertito in formato messaggio.
* **TurbofanDevice:** crea un dispositivo IoT che corrisponde a un singolo dispositivo (serie temporale) nei dati e trasmette i dati all'hub IoT tramite il dispositivo IoT.

Per completare le attività descritte in questo articolo, dovrebbero essere necessari circa 20 minuti.

Nella realtà, le operazioni equivalenti di questo passaggio verranno probabilmente eseguite da sviluppatori di dispositivi e sviluppatori cloud.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configurare Visual Studio Code e creare il progetto DeviceHarness

1. Aprire una sessione Desktop remoto nella macchina virtuale, come illustrato nell'articolo precedente.

1. Aprire Visual Studio Code.

1. In Visual Studio Code selezionare **File** > **Apri cartella**.

1. Nella casella di testo **Cartella** immettere `C:\source\IoTEdgeAndMlSample\DeviceHarness` e fare clic sul pulsante **Seleziona cartella**.

   Se vengono visualizzati errori OmniSharp nella finestra di output, sarà necessario disinstallare l'estensione C#, chiudere e riaprire VS Code, installare l'estensione C# e quindi ricaricare la finestra.

1. Poiché le estensioni vengono usate per la prima volta in questo computer, alcune verranno aggiornate e installeranno le relative dipendenze. È possibile che venga chiesto di aggiornare l'estensione. In questo caso, selezionare **Ricarica finestra**.

1. Verrà chiesto di aggiungere gli asset necessari per DeviceHarness. Selezionare **Sì** per aggiungerli.

   * Possono essere necessari alcuni secondi prima che venga visualizzata la notifica.
   * Se non viene visualizzata, fare clic sull'icona della campana nell'angolo in basso a destra.

   ![Popup dell'estensione di VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selezionare **Ripristina** per ripristinare le dipendenze del pacchetto.

   ![Richiesta di ripristino di VS Code](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Per verificare se l'ambiente è configurato correttamente, attivare una compilazione premendo `Ctrl + Shift + B` oppure selezionando **Terminale** > **Esegui attività di compilazione**.

1. Verrà chiesto di selezionare l'attività di compilazione da eseguire. Selezionare **Compila**.

1. La compilazione viene eseguita e genera un messaggio di operazione riuscita.

   ![Messaggio di output di compilazione riuscita](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. È possibile impostare questa compilazione come attività di compilazione predefinita selezionando **Terminale** > **Configure Default Build Task** (Configura attività di compilazione predefinita) e scegliendo **Compila** nel prompt.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Connettersi all'hub IoT ed eseguire DeviceHarness

Dopo aver avviato la creazione del progetto, connettersi all'hub IoT per accedere alla stringa di connessione e monitorare lo stato di avanzamento della generazione di dati.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Accedere ad Azure in Visual Studio Code

1. Accedere alla sottoscrizione di Azure in Visual Studio Code aprendo il riquadro comandi. Premere `Ctrl + Shift + P` oppure selezionare **Visualizza** > **Riquadro comandi**.

1. Al prompt cercare e selezionare **Azure: Accedi**.

1. Viene visualizzata una finestra del browser in cui si chiede di immettere le credenziali. Quando si viene reindirizzati a una pagina di operazione riuscita, è possibile chiudere il browser.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Connettersi all'hub IoT e recuperare la stringa di connessione dell'hub

1. Nella sezione inferiore della finestra di esplorazione di Visual Studio Code selezionare il riquadro **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) per espanderlo.

1. Nel riquadro espanso fare clic su **Select IoT Hub** (Seleziona hub IoT).

1. Quando richiesto, selezionare la sottoscrizione di Azure e quindi l'hub IoT.

1. Fare clic sul riquadro **Azure IoT Hub devices** (Dispositivi hub IoT di Azure), quindi su **...** per visualizzare altre opzioni. Selezionare **Copy IoT Hub connection string** (Copia stringa di connessione di hub IoT).

   ![Copiare la stringa di connessione dell'hub IoT](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Eseguire il progetto DeviceHarness

1. Selezionare **Visualizza** > **Terminale** per aprire il terminale di Visual Studio Code.

   Se non viene visualizzato un prompt, selezionare Accedi.

1. Immettere `dotnet run` nel terminale.

1. Quando viene chiesta la stringa di connessione dell'hub IoT, incollare la stringa di connessione copiata nella sezione precedente.

1. Nel riquadro **Azure IoT Hub devices** (Dispositivi hub IoT di Azure) fare clic sul pulsante di aggiornamento.

   ![Aggiornare l'elenco di dispositivi dell'hub IoT](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. I dispositivi vengono aggiunti all'hub IoT e vengono visualizzati in verde per indicare che vengono usati per l'invio di dati.

1. Per visualizzare i messaggi inviati all'hub, fare clic con il pulsante destro del mouse su un dispositivo e scegliere **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito). I messaggi verranno visualizzati nel riquadro di output in Visual Studio Code.

1. Per interrompere il monitoraggio, fare clic nel riquadro di output **Azure IoT Hub Toolkit** (Toolkit hub IoT di Azure) e scegliere **Stop Monitoring Built-in Event Endpoint** (Interrompi monitoraggio endpoint eventi predefinito).

1. Attendere alcuni minuti mentre viene completata l'esecuzione dell'applicazione.

## <a name="check-iot-hub-for-activity"></a>Controllare l'attività nell'hub IoT

I dati inviati da DeviceHarness sono arrivati nell'hub IoT. È facile verificare se i dati hanno raggiunto l'hub tramite il portale di Azure.

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare all'hub IoT.

1. Nella pagina di panoramica si dovrebbe verificare che i dati sono stati inviati all'hub:  

   ![Visualizzare i messaggi inviati dal dispositivo al cloud nell'hub IoT](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Convalidare i dati in Archiviazione di Azure

I dati appena inviati all'hub IoT sono stati instradati al contenitore di archiviazione creato nell'articolo precedente. Esaminare i dati nell'account di archiviazione.

1. Nel portale di Azure passare all'account di archiviazione.

1. Nel riquadro di spostamento e dell'archiviazione selezionare **Storage Explorer (anteprima)** .

1. In Storage Explorer selezionare **Contenitori BLOB** e quindi **devicedata**.

1. Nel riquadro di contenuto fare clic sulla cartella con il nome dell'hub IoT, seguito da anno, mese, giorno e ora. Verranno visualizzate diverse cartelle che rappresentano i minuti in cui sono stati scritti i dati.

   ![Visualizzare le cartelle nell'archiviazione BLOB](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Fare clic su una di queste cartelle per trovare i dati con le etichette **00** e **01** che corrispondono alla partizione.

1. I file sono scritti in formato [Avro](https://avro.apache.org/), ma facendo doppio clic su uno di essi verrà aperta una nuova scheda del browser e verrà eseguito il rendering parziale dei dati. Se invece viene chiesto di aprire il file in un programma, è possibile scegliere VS Code, che eseguirà correttamente il rendering.

1. Non è necessario provare a leggere o interpretare i dati in questo momento. Questa operazione verrà eseguita nell'articolo successivo.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato usato un progetto .NET Core per creare un set di dispositivi virtuali da usare per inviare dati all'hub IoT e nel contenitore di Archiviazione di Azure. Questo progetto simula uno scenario reale in cui i dispositivi fisici inviano dati, come letture dei sensori, impostazioni operative, segnali e modalità di errore e così via, a un hub IoT e quindi in una risorsa di archiviazione curata. Quando vengono raccolti in una quantità sufficiente, i dati vengono usati per eseguire il training di modelli che stimano la vita utile rimanente del dispositivo, come verrà illustrato nell'articolo successivo.

Continuare con l'articolo successivo per eseguire il training di un modello di Machine Learning con i dati.

> [!div class="nextstepaction"]
> [Eseguire il training e la distribuzione di un modello di Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
