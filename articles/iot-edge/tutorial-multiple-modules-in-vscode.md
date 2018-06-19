---
title: Gestire più moduli Azure IoT Edge in VS Code | Microsoft Docs
description: Usare Visual Studio Code per sviluppare soluzioni Azure IoT Edge che usano più moduli.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763035"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code (anteprima)

È possibile usare Visual Studio Code per sviluppare una soluzione Azure IoT Edge con più moduli. Questo articolo mostra come creare, aggiornare e distribuire una soluzione IoT Edge che invia dati di sensore su un dispositivo simulato IoT Edge in Visual Studio Code. 

## <a name="prerequisites"></a>prerequisiti

Per completare le procedure in questo articolo, sono necessari i prerequisiti seguenti:

- [Visual Studio Code](https://code.visualstudio.com/)
- L'[estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- L'[estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)
- Il modello AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Un hub IoT attivo con almeno un dispositivo IoT Edge

È anche necessario [Docker per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) con l'integrazione di Azure IoT Hub Device Explorer per gestire le immagini e i contenitori.

## <a name="prepare-your-first-iot-edge-solution"></a>Preparare la prima soluzione IoT Edge

1. Nel **riquadro comandi** di VS Code immettere ed eseguire il comando **Edge: New IoT Edge solution** (Edge: Nuova soluzione IoT Edge). Selezionare la cartella dell'area di lavoro e specificare il nome della soluzione (il valore predefinito è EdgeSolution). Creare un modulo in C# (con il nome **SampleModule**) come primo modulo utente in questa soluzione. È anche necessario specificare il repository di immagini Docker per il primo modulo. Il repository di immagini predefinito si basa su un registro Docker locale (**localhost:5000/<first module name>**). È possibile sostituirlo con Registro contenitori di Azure o con l'hub Docker.

   > [!NOTE]
   > Se si usa un registro locale di Docker, assicurarsi che il registro sia in esecuzione. Immettere il comando seguente nella finestra della console:
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. La finestra VS Code carica l'area di lavoro della soluzione IoT Edge. La cartella radice contiene una cartella **modules**, una cartella **.vscode** e un file modello del manifesto della distribuzione. Le configurazioni di debug si trovano nella cartella .vscode. Tutti i codici di modulo utente sono sottocartelle della cartella modules. Il file deployment.template.json è il modello del manifesto di distribuzione. Alcuni dei parametri in questo file vengono analizzati dal file module.json, presente nella cartella di ogni modulo.

3. Aggiungere il secondo modulo a questo progetto di soluzione. Immettere ed eseguire il comando **Edge: Aggiungi modulo IoT Edge**. Selezionare il file del modello di distribuzione da aggiornare. Selezionare un modulo **Funzione di Azure - C#** con il nome **SampleFunction** e il repository di immagini Docker corrispondente.

4. Aprire il file deployment.template.json. Verificare che il file dichiari tre moduli e il runtime. Il messaggio viene generato dal modulo tempSensor. Il messaggio viene inoltrato tramite pipe direttamente mediante i moduli SampleModule e SampleFunction e quindi inviato all'hub IoT. 

5. Aggiornare le route per questi moduli con il contenuto seguente:

   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Salvare questo file.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Compilare e distribuire la soluzione IoT Edge

1. Nel **riquadro comandi** di VS Code immettere ed eseguire il comando **Edge: Build IoT Edge solution** (Edge: Compila soluzione IoT Edge). In base al file module.json presente nella cartella di ogni modulo, questo comando inizia a compilare, includere in contenitori ed eseguire il push di ogni immagine Docker del modulo. Il comando passa quindi il valore richiesto al file deployment.template.json e genera il file deployment.json con le informazioni dalla cartella di configurazione. Il terminale integrato di VS Code mostra lo stato della compilazione.

2. In Azure IoT Hub **Device Explorer** fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge e quindi scegliere il comando **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). Selezionare il file deployment.json nella cartella config. Il terminale integrato in VS Code mostra che la distribuzione è stata creata correttamente con un ID distribuzione.

3. Se si sta simulando un dispositivo IoT Edge nel computer di sviluppo, si noterà che tutti i contenitori delle immagini dei moduli vengono avviati in pochi minuti.

## <a name="view-the-generated-data"></a>Visualizzare i dati generati

1. Per monitorare i dati che arrivano all'hub IoT, selezionare **Visualizza** > **Riquadro comandi**. Selezionare quindi **IoT: Start monitoring D2C message** (IoT: Avvia monitoraggio messaggio da dispositivo a cloud). 
2. Per interrompere il monitoraggio dei dati, usare il comando **IoT: Stop monitoring D2C message** (IoT: Interrompi il monitoraggio del messaggio D2C) nel **riquadro comandi**. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altri scenari per lo sviluppo con Azure IoT Edge in Visual Studio Code:

* [Eseguire il debug di un modulo C# in Visual Studio Code](how-to-vscode-debug-csharp-module.md)
* [Eseguire il debug di una funzione C# in Visual Studio Code](how-to-vscode-debug-azure-function.md)
