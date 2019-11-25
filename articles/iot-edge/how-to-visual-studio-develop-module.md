---
title: Develop and debug modules in Visual Studio - Azure IoT Edge | Microsoft Docs
description: Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2f8b0fe83e10beb3b65dca08e18b03f4fc11947e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457100"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge

È possibile trasformare la logica di business in moduli per Azure IoT Edge. This article shows you how to use Visual Studio 2019 as the main tool to develop and debug modules.

Azure IoT Edge Tools for Visual Studio offre i vantaggi seguenti:

- Possibilità di creare, modificare, compilare, eseguire ed effettuare il debug di soluzioni e moduli per Azure IoT Edge nel computer di sviluppo locale.
- Capacità per la distribuzione della soluzione Azure IoT Edge a un dispositivo Azure IoT Edge tramite l'hub IoT di Azure.
- Code your Azure IoT modules in C or C# while having all of the benefits of Visual Studio development.
- Funzionalità di gestione di dispositivi e moduli per Azure IoT Edge con l'interfaccia utente.

This article shows you how to use the Azure IoT Edge Tools for Visual Studio 2019 to develop your IoT Edge modules. Si apprenderà anche come distribuire il progetto a un dispositivo Azure IoT Edge. Currently, Visual Studio 2019 provides support for modules written in C and C#. The supported device architectures are Windows X64 and Linux X64 or ARM32. For more information about supported operating systems, languages, and architectures, see [Language and architecture support](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si usi un computer o una macchina virtuale Windows come computer di sviluppo. On Windows computers you can develop either Windows or Linux modules. To develop Windows modules, use a Windows computer running version 1809/build 17763 or newer. To develop Linux modules, use a Windows computer that meets the [requirements for Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Because this article uses Visual Studio 2019 as the main development tool, install Visual Studio. Make sure you include the **Azure development** and **Desktop development with C++** workloads in your Visual Studio 2019 installation. You can [Modify Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) to add the required workloads.

After your Visual Studio 2019 is ready, you also need the following tools and components:

- Download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) from the Visual Studio marketplace to create an IoT Edge project in Visual Studio 2019.

> [!TIP]
> If you are using Visual Studio 2017, please download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for VS 2017 from the Visual Studio marketplace

- Scaricare e installare [Docker Community Edition](https://docs.docker.com/install/) nel computer di sviluppo per compilare ed eseguire le immagini del modulo. È necessario configurare Docker CE per l'esecuzione in modalità contenitore Linux o in modalità contenitore Windows.

- Impostare l'ambiente di sviluppo locale per eseguire e testare la soluzione IoT Edge ed effettuarne il debug installando [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/). Install [Python (2.7/3.6+) and Pip](https://www.python.org/) and then install the **iotedgehubdev** package by running the following command in your terminal. Assicurarsi che la versione di Azure IoT EdgeHub Dev Tool sia superiore a 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone the repository and install the Vcpkg library manager, and then install the **azure-iot-sdk-c package** for Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud.

- Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un dispositivo IoT Edge. Per usare il computer come dispositivo IoT Edge, seguire i passaggi nella guida introduttiva per [Linux](quickstart-linux.md) o [Windows](quickstart.md). Se si esegue il daemon di IoT Edge nel computer di sviluppo, potrebbe essere necessario arrestare EdgeHub ed EdgeAgent prima di iniziare lo sviluppo in Visual Studio.

### <a name="check-your-tools-version"></a>Controllare la versione degli strumenti

1. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**. Espandere **Installati > Strumenti** per visualizzare **Azure IoT Edge Tools** e **Cloud Explorer for Visual Studio**.

1. Prendere nota della versione installata. È possibile confrontare questa versione con quella più recente in Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)).

1. Se la versione è precedente a quella disponibile in Visual Studio Marketplace, aggiornare gli strumenti in Visual Studio come illustrato nella sezione seguente.

### <a name="update-your-tools"></a>Aggiornare gli strumenti

1. Nella finestra di dialogo **Estensioni e aggiornamenti** espandere **Aggiornamenti > Visual Studio Marketplace**, selezionare **Azure IoT Edge Tools** o **Cloud Explorer for Visual Studio** e selezionare **Aggiorna**.

1. Dopo aver scaricato l'aggiornamento degli strumenti, chiudere Visual Studio per attivare l'aggiornamento degli strumenti con il programma di installazione di VSIX.

1. Nel programma di installazione selezionare **OK** per avviare il processo e quindi **Modifica** per aggiornare gli strumenti.

1. Al termine dell'aggiornamento, selezionare **Chiudi** e riavviare Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Creare un progetto Azure IoT Edge

Il modello di progetto Azure IoT Edge in Visual Studio consente di creare un progetto che può essere distribuito in dispositivi Azure IoT Edge nell'hub IoT di Azure. First you create an Azure IoT Edge solution, and then you generate the first module in that solution. Ogni soluzione IoT Edge può contenere più di un modulo.

> [!TIP]
> La struttura del progetto IoT Edge creato da Visual Studio è diversa rispetto a quella in Visual Studio Code.

1. In Visual Studio new project dialog, search and select **Azure IoT Edge** project and click **Next**. In project configuration window, enter a name for your project and specify the location, and then select **Create**. Il nome del progetto predefinito è **AzureIoTEdgeApp1**.

   ![Creare un nuovo progetto](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. In the **Add IoT Edge Application and Module** window, select either **C# Module** or **C Module** and then specify your module name and module image repository. Visual Studio popola automaticamente il nome del modulo con il valore **localhost:5000/<nome del modulo\>** . Sostituire tale valore con le proprie informazioni di registro. Se per il test si usa un registro Docker locale, **localhost** è corretto. Se si usa Registro Azure Container, specificare il server di accesso indicato nelle impostazioni del registro. The login server looks like **_\<registry name\>_ .azurecr.io**. Only replace the **localhost:5000** part of the string so that the final result looks like **\<*registry name*\>.azurecr.io/ _\<your module name\>_** . The default module name is **IotEdgeModule1**

   ![Add Application and Module](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Select **OK** to create the Azure IoT Edge solution with a module that uses either C# or C.

Now you have an **AzureIoTEdgeApp1.Linux.Amd64** project or an **AzureIoTEdgeApp1.Windows.Amd64** project, and also an **IotEdgeModule1** project in your solution. Each **AzureIoTEdgeApp1** project has a `deployment.template.json` file, which defines the modules you want to build and deploy for your IoT Edge solution, and also defines the routes between modules. The default solution has a **SimulatedTemperatureSensor** module and a **IotEdgeModule1** module. The **SimulatedTemperatureSensor** module generates simulated data to the **IotEdgeModule1** module, while the default code in the **IotEdgeModule1** module directly pipes received messages to Azure IoT Hub.

The **IotEdgeModule1** project is a .NET Core 2.1 console application if it's a C# module. Contiene i file Docker richiesti per l'esecuzione del dispositivo IoT Edge con un contenitore Windows o un contenitore Linux. The `module.json` file describes the metadata of a module. The actual module code, which takes Azure IoT Device SDK as a dependency, is found in the `Program.cs` or `main.c` file.

## <a name="develop-your-module"></a>Sviluppare il modulo

The default module code that comes with the solution is located at **IotEdgeModule1** > **Program.cs** (for C#) or **main.c** (C). The module and the `deployment.template.json` file are set up so that you can build the solution, push it to your container registry, and deploy it to a device to start testing without touching any code. The module is built to take input from a source (in this case, the **SimulatedTemperatureSensor** module that simulates data) and pipe it to Azure IoT Hub.

When you're ready to customize the module template with your own code, use the [Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md) to build modules that address the key needs for IoT solutions such as security, device management, and reliability.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inizializzare iotedgehubdev con la stringa di connessione del dispositivo IoT Edge

1. Copiare la stringa di connessione di un dispositivo IoT Edge qualsiasi da **Stringa di connessione primaria** in Visual Studio Cloud Explorer. Assicurarsi di non copiare la stringa di connessione di un dispositivo non Edge, dal momento che l'icona di un dispositivo IoT Edge è diversa da quella di un dispositivo non Edge.

   ![Copiare la stringa di connessione del dispositivo Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Go to **Tools** > **Azure IoT Edge Tools** > **Setup IoT Edge Simulator**, paste the connection string and click **OK**.

   ![Apertura della finestra di impostazione della stringa di connessione del dispositivo Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Immettere la stringa di connessione del primo passaggio e quindi selezionare **OK**.

> [!NOTE]
> È necessario seguire questa procedura solo una volta nel computer di sviluppo, poiché i risultati vengono applicati automaticamente a tutte le soluzioni Azure IoT Edge successive. Questa procedura può essere eseguita nuovamente se è necessario usare una stringa di connessione diversa.

## <a name="build-and-debug-single-module"></a>Build and debug single module

In genere, è consigliabile testare ed eseguire il debug di ogni modulo prima di eseguirlo all'interno di un'intera soluzione con più moduli.

1. Right-click **IotEdgeModule1** and select **Set as StartUp Project** from the context menu.

   ![Impostare il progetto di avvio](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Premere **F5** o fare clic sul pulsante riportato di seguito per eseguire il modulo; potrebbero occorrere circa 10&ndash;20 secondi alla prima esecuzione.

   ![Eseguire il modulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Si noterà che viene avviata un'app console .NET Core se il modulo è stato inizializzato correttamente.

   ![Modulo in esecuzione](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. If developing in C#, set a breakpoint in the `PipeMessage()` function in **Program.cs**; if using C, set a breakpoint in the `InputQueue1Callback()` function in **main.c**. You can then test it by sending a message by running the following command in a **Git Bash** or **WSL Bash** shell. (non è possibile eseguire il comando `curl` in PowerShell o nel prompt dei comandi).

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Eseguire il debug del modulo singolo](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Il punto di interruzione deve essere attivato. È possibile controllare le variabili nella finestra **Variabili locali** di Visual Studio.

   > [!TIP]
   > È anche possibile usare [PostMan](https://www.getpostman.com/) o altri strumenti API per inviare messaggi invece di `curl`.

1. Premere **CTRL+F5** oppure fare clic sul pulsante Arresta per arrestare il debug.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Compilare ed eseguire il debug di una soluzione IoT Edge con più moduli

Dopo aver completato lo sviluppo di un modulo singolo, è possibile eseguire ed effettuare il debug di un'intera soluzione con più moduli.

1. Add a second module to the solution by right-clicking **AzureIoTEdgeApp1** and selecting **Add** > **New IoT Edge Module**. The default name of the second module is **IotEdgeModule2** and will act as another pipe module.

1. Open the file `deployment.template.json` and you'll see **IotEdgeModule2** has been added in the **modules** section. Sostituire la sezione **routes** con il contenuto seguente. Se i nomi dei moduli sono stati personalizzati, assicurarsi di aggiornare questi nomi in modo che corrispondano.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Fare clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e selezionare **Imposta come progetto di avvio** dal menu di scelta rapida.

1. Creare i punti di interruzione e quindi premere **F5** per eseguire ed effettuare il debug di più moduli contemporaneamente. You should see multiple .NET Core console app windows, which each window representing a different module.

   ![Eseguire il debug di più moduli](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Premere **CTRL+F5** oppure selezionare il pulsante Arresta per arrestare il debug.

## <a name="build-and-push-images"></a>Compilare le immagini ed eseguirne il push

1. Assicurarsi che **AzureIoTEdgeApp1** sia il progetto di avvio. Selezionare **Debug** o **Versione** come configurazione per la compilazione delle immagini del modulo.

    > [!NOTE]
    > Quando si sceglie **Debug**, Visual Studio usa `Dockerfile.(amd64|windows-amd64).debug` per compilare le immagini Docker. Ciò include VSDBG, ovvero il debugger della riga di comando di .NET Core, nell'immagine del contenitore durante la compilazione. Per i moduli IoT Edge per l'ambiente di produzione, è consigliabile usare la configurazione **Versione**, che usa `Dockerfile.(amd64|windows-amd64)` senza VSDBG.

1. Se si usa un registro privato come Registro Azure Container, usare il comando Docker seguente per l'accesso. Se si usa un registro locale, è possibile [eseguire un registro locale](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se si usa un registro privato come Registro Azure Container, è necessario aggiungere le informazioni di accesso del registro alle impostazioni di runtime presenti nel file `deployment.template.json`. Sostituire i segnaposto con il nome utente amministratore, la password e il nome del registro di Registro Azure Container effettivi.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Fare clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e selezionare **Build and Push Edge Solution** (Compila ed esegui il push della soluzione Edge) per compilare ed eseguire il push dell'immagine Docker per ogni modulo.

   ![Compilare le immagini ed eseguirne il push](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Nell'articolo della guida introduttiva usato per configurare il dispositivo IoT Edge è stato distribuito un modulo usando il portale di Azure. È anche possibile distribuire i moduli con Cloud Explorer per Visual Studio. Il manifesto della distribuzione, il file `deployment.json`, è già disponibile per questo scenario; è sufficiente selezionare un dispositivo che riceverà la distribuzione.

1. Aprire **Cloud Explorer** facendo clic su **Visualizza** > **Cloud Explorer**. Make sure you've logged in to Visual Studio 2019.

1. In **Cloud Explorer** espandere la sottoscrizione, trovare l'hub IoT di Azure e il dispositivo Azure IoT Edge da distribuire.

1. Fare clic con il pulsante destro del mouse sul dispositivo IoT Edge per creare una distribuzione. È necessario selezionare il file manifesto della distribuzione in `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Non si deve selezionare `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Click the refresh button to see the new modules running along with the **SimulatedTemperatureSensor** module and **$edgeAgent** and **$edgeHub**.

## <a name="view-generated-data"></a>Visualizzare i dati generati

1. To monitor the D2C message for a specific device, select the device in the list and then click **Start Monitoring Built-in Event Endpoint** in the **Action** window.

1. To stop monitoring data, select the device in the list and then select **Stop Monitoring Built-in Event Endpoint** in the **Action** window.

## <a name="next-steps"></a>Passaggi successivi

Per sviluppare moduli personalizzati per i dispositivi IoT Edge, [comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
