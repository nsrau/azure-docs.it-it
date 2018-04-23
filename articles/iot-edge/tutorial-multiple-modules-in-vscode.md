---
title: Usare più moduli di IoT Edge in Visual Studio Code | Microsoft Docs
description: Distribuire Azure Machine Learning come modulo in un dispositivo periferico
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6c94701507f86f6ecab2875f952215cc3e4cc719
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code - Anteprima
È possibile usare Visual Studio Code per sviluppare la soluzione IoT Edge con più moduli. Questo articolo descrive dettagliatamente la creazione, l'aggiornamento e la distribuzione di una soluzione IoT Edge che invia tramite pipe i dati dei sensori nel dispositivo IoT Edge simulato in Visual Studio Code. In questo articolo viene spiegato come:

* Usare Visual Studio Code per creare una soluzione IoT Edge
* Usare VS Code per aggiungere un nuovo modulo alla soluzione IoT Edge funzionante. 
* Distribuire la soluzione IoT Edge (più moduli) nel dispositivo IoT Edge
* Visualizzare i dati generati

## <a name="prerequisites"></a>prerequisiti
* Completare le esercitazioni seguenti
  * [Distribuire un modulo C#](tutorial-csharp-module.md)
  * [Distribuire una funzione C#](tutorial-deploy-function.md)
  * [Deploy Python module](tutorial-python-module.md) (Distribuire un modulo Python)
* [Docker per VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) con integrazione dell'utilità di esplorazione per la gestione di immagini e contenitori.


## <a name="prepare-your-first-iot-edge-solution"></a>Preparare la prima soluzione IoT Edge
1. Nel riquadro comandi di VS Code digitare ed eseguire il comando **Edge: New IoT Edge solution**. Selezionare quindi la cartella dell'area di lavoro, specificare il nome della soluzione (il nome predefinito è **EdgeSolution**) e creare un modulo C# (**SampleModule**) come primo modulo utente in questa soluzione. È anche necessario specificare il repository di immagini Docker per il primo modulo. Il repository di immagini Docker si basa su un registro Docker locale (`localhost:5000/<first module name>`). È anche possibile sostituirlo con il registro contenitori di Azure o con l'hub Docker.

> [!NOTE]
> Se si usa un registro Docker locale, verificare che il registro sia in esecuzione digitando il comando `docker run -d -p 5000:5000 --restart=always --name registry registry:2` nella finestra della console.

2. La finestra VS Code caricherà l'area di lavoro della soluzione IoT Edge. Nella cartella radice sono presenti una cartella `modules`, una cartella `.vscode` e un file modello del manifesto della distribuzione. Nella cartella si trovano le configurazioni di debug `.vscode`. Tutti i codici dei moduli utente saranno sottocartelle della cartella `modules`. `deployment.template.json` è il modello del manifesto della distribuzione. Alcuni dei parametri di questo file verranno analizzati da `module.json`, presente nella cartella di ogni modulo.

3. Aggiungere il secondo modulo a questo progetto di soluzione. Questa volta digitare ed eseguire **Edge: Add IoT Edge module** e selezionare il file del modello di distribuzione da aggiornare. Selezionare quindi una **funzione di Azure - C#** con il nome **SampleFunction** e il repository di immagini Docker da aggiungere.

4. Ora la prima soluzione IoT Edge con due moduli di base è pronta. Il modulo C# predefinito funge da modulo dei messaggi della pipe, mentre la funzione C# funge da funzione dei messaggi della pipe. In `deployment.template.json` si noterà che questa soluzione contiene tre moduli. Il messaggio verrà generato dal modulo `tempSensor` e inoltrato direttamente tramite pipe con `SampleModule` e `SampleFunction`, quindi verrà inviato all'hub IoT. Aggiornare le route per questi moduli con il contenuto seguente. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Salvare questo file.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Compilare e distribuire la soluzione IoT Edge
1. Nel riquadro comandi di VS Code digitare ed eseguire il comando **Edge: Build IoT Edge solution**. In base al file `module.json` presente nella cartella di ogni modulo, questo comando effettuerà un controllo e inizierà a compilare, includere in contenitori ed eseguire il push di ogni immagine Docker del modulo. Analizzerà quindi il valore necessario per `deployment.template.json` e genererà `deployment.json` con il valore effettivo nella cartella `config`. È possibile visualizzare lo stato della compilazione nel terminale integrato di VS Code.

2. Fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge nell'apposito strumento di esplorazione per l'hub IoT di Azure, quindi scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). Selezionare `deployment.json` nella cartella `config`. Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione nel terminale integrato di VS Code.

3. Se si [simula un dispositivo IoT Edge](tutorial-simulate-device-linux.md) nel computer di sviluppo, si noterà che tutti i contenitori delle immagini dei moduli verranno avviati in pochi minuti.

## <a name="view-generated-data"></a>Visualizzare i dati generati

1. Per monitorare i dati in arrivo nell'hub IoT, selezionare **Visualizza** > **Riquadro comandi** e cercare **IoT: Start monitoring D2C message** (IoT: Avvia il monitoraggio del messaggio D2C). 
2. Per interrompere il monitoraggio dei dati, usare il comando **IoT: Stop monitoring D2C message** (IoT: Interrompi il monitoraggio del messaggio D2C) nel riquadro comandi. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari durante lo sviluppo di Azure IoT Edge in Visual Studio Code, è possibile continuare con uno degli articoli seguenti:

* [Eseguire il debug di un modulo C# in Visual Studio Code](how-to-vscode-debug-csharp-module.md)
* [Eseguire il debug di una funzione C# in Visual Studio Code](how-to-vscode-debug-azure-function.md)