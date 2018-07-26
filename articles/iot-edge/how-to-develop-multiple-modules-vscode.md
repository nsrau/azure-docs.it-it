---
title: Utilizzare più moduli di Azure IoT Edge in VS Code | Microsoft Docs
description: Usare l'estensione IoT per Visual Studio Code per sviluppare contemporaneamente più moduli per Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 31fe210b87a052438956d813db0d104e0f2cdb6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041252"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code

È possibile usare Visual Studio Code per sviluppare una soluzione Azure IoT Edge con più moduli. Questo articolo mostra come creare, aggiornare e distribuire una soluzione IoT Edge che invia dati di sensore su un dispositivo simulato IoT Edge in Visual Studio Code. 

## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure in questo articolo, sono necessari i prerequisiti seguenti:

- [Visual Studio Code](https://code.visualstudio.com/)
- L'[estensione Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- L'[estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
- Un hub IoT attivo con almeno un dispositivo IoT Edge

È anche necessario [Docker per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) con l'integrazione di Azure IoT Hub Device Explorer per gestire le immagini e i contenitori.

## <a name="create-your-iot-edge-solution"></a>Creare la soluzione IoT Edge

1. In Visual Studio Code aprire il terminale integrato selezionando **Visualizza** > **Terminale integrato**. 

1. Nel **riquadro comandi** di VS Code immettere ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge). Selezionare la cartella dell'area di lavoro e specificare il nome della soluzione (il valore predefinito è EdgeSolution). Creare un modulo in C# (con il nome **PipeModule**) come primo modulo utente in questa soluzione. Il modello predefinito di modulo C# è un modulo di pipe, che invia direttamente tramite pipe i messaggi da upstream a downstream. È anche necessario specificare il repository di immagini Docker per il primo modulo. Il repository di immagini predefinito si basa su un registro Docker locale (**localhost:5000/<first module name>**). È possibile sostituirlo con Registro contenitori di Azure o con l'hub Docker. 

2. La finestra di VS Code carica l'area di lavoro della soluzione IoT Edge. La cartella radice contiene una cartella **modules**, una cartella **.vscode** e un file modello del manifesto della distribuzione. Le configurazioni di debug si trovano nella cartella .vscode. Tutti i codici di modulo utente sono sottocartelle della cartella modules. Il file deployment.template.json è il modello del manifesto di distribuzione. Alcuni dei parametri in questo file vengono analizzati dal file module.json, presente nella cartella di ogni modulo.

3. Aggiungere il secondo modulo a questo progetto di soluzione. Esistono diversi modi per aggiungere un nuovo modulo alla soluzione corrente. Immettere ed eseguire il comando **Azure IoT Edge: Aggiungi modulo IoT Edge**. Selezionare il file del modello di distribuzione da aggiornare. Oppure fare clic con il pulsante destro del mouse sulla cartella modules o fare clic con il pulsante destro del mouse sul file deployment.template.json e scegliere **Aggiungi modulo IoT Edge**. Sarà disponibile un elenco a discesa per selezionare il tipo di modulo. Selezionare un modulo **Funzioni di Azure - C#** con il nome **PipeFunction** e il repository di immagini Docker corrispondente. Il modello predefinito di modulo di funzioni C# è un modulo di pipe, che invia direttamente tramite pipe i messaggi da upstream a downstream.

4. Aprire il file deployment.template.json. Verificare che il file dichiari tre moduli e il runtime. Il messaggio viene generato dal modulo tempSensor. Il messaggio viene inoltrato tramite pipe direttamente mediante i moduli SampleModule e SampleFunction e quindi inviato all'hub IoT. 

5. Aggiornare le route per questi moduli con il contenuto seguente:

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Salvare questo file.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Compilare e distribuire la soluzione IoT Edge

1. Nel **riquadro comandi** di VS Code immettere ed eseguire il comando **Edge: Build IoT Edge solution** (Edge: Compila soluzione IoT Edge). In base al file module.json presente nella cartella di ogni modulo, questo comando inizia a compilare, includere in contenitori ed eseguire il push di ogni immagine Docker del modulo. Il comando passa quindi il valore richiesto al file deployment.template.json e genera il file deployment.json con le informazioni dalla cartella di configurazione. Il terminale integrato di VS Code mostra lo stato della compilazione. 

2. In Azure IoT Hub **Device Explorer** fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge e quindi scegliere il comando **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). Selezionare il file deployment.json nella cartella config. Il terminale integrato in VS Code mostra che la distribuzione è stata creata correttamente con un ID distribuzione.

3. Se si sta simulando un dispositivo IoT Edge nel computer di sviluppo, si noterà che tutti i contenitori delle immagini dei moduli vengono avviati in pochi minuti.

## <a name="view-the-generated-data"></a>Visualizzare i dati generati

1. Per monitorare i dati che arrivano all'hub IoT, selezionare **Visualizza** > **Riquadro comandi**. Selezionare quindi **IoT: Start monitoring D2C message** (IoT: Avvia monitoraggio messaggio da dispositivo a cloud). 
2. Per interrompere il monitoraggio dei dati, usare il comando **IoT: Stop monitoring D2C message** (IoT: Interrompi il monitoraggio del messaggio D2C) nel **riquadro comandi**. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altri scenari per lo sviluppo con Azure IoT Edge in Visual Studio Code:

* Sviluppare moduli in VS Code con [c#](how-to-develop-csharp-module.md) o [Node. js](how-to-develop-node-module.md).
* Sviluppare Funzioni di Azure in VS Code con [C#](how-to-develop-csharp-function.md).

Per sviluppare moduli per i dispositivi IoT Edge, [Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).