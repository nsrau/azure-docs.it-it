---
title: Sviluppare moduli per dispositivi Windows - Azure IoT Edge | Microsoft Docs
description: Questa esercitazione illustra in modo dettagliato come configurare il computer di sviluppo e le risorse cloud per sviluppare moduli IoT Edge usando contenitori Windows per dispositivi Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/20/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 631338c0217eb61f4f98cd06ffa16cb2500f246b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146741"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Esercitazione: Sviluppare moduli IoT Edge per dispositivi Windows

Usare Visual Studio 2017 per sviluppare e distribuire codice in dispositivi Windows che eseguono IoT Edge.

Nela procedura di avvio rapido è stato creato un dispositivo IoT Edge usando una macchina virtuale Windows ed è stato distribuito un modulo predefinito da Azure Marketplace. Questa esercitazione illustra in modo dettagliato come sviluppare e distribuire codice personalizzato in un dispositivo IoT Edge. Questa esercitazione è un prerequisito utile per tutte le altre esercitazioni, che illustrano in modo più dettagliato i concetti relativi a linguaggi di programmazione o servizi di Azure specifici. 

Questa esercitazione usa l'esempio di distribuzione di un **modulo C in un dispositivo Windows**. Questo esempio è stato scelto per la sua semplicità, in modo che sia possibile apprendere informazioni sugli strumenti di sviluppo senza doversi preoccupare di avere le librerie appropriate installate. Una volta appresi i concetti di sviluppo, è possibile scegliere il linguaggio o il servizio di Azure preferito per approfondire i dettagli. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare il computer di sviluppo.
> * Usare IoT Edge Tools per Visual Studio 2017 per creare un nuovo progetto.
> * Compilare il progetto come contenitore e archiviarlo in un Registro Azure Container.
> * Distribuire il codice in un dispositivo IoT Edge. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Concetti chiave

Questa esercitazione illustra in modo dettagliato le fasi di sviluppo di un modulo IoT Edge. Un *modulo IoT Edge*, talvolta detto semplicemente *modulo*, è un contenitore in cui è presente codice eseguibile. È possibile distribuire uno o più moduli in un dispositivo IoT Edge. I moduli eseguono attività specifiche, ad esempio l'inserimento di dati provenienti dai sensori, l'esecuzione di operazioni di analisi o pulizia dei dati oppure l'invio di messaggi a un hub IoT. Per altre informazioni, vedere [Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md).

Quando si sviluppano moduli IoT Edge, è importante comprendere la differenza tra il computer di sviluppo e il dispositivo IoT Edge di destinazione in cui il modulo verrà distribuito. Il contenitore creato per inserire il codice dei moduli deve corrispondere al sistema operativo del *dispositivo di destinazione*. Per lo sviluppo di contenitori Windows, questo concetto è più semplice perché i contenitori Windows vengono eseguiti solo nei sistemi operativi Windows. Ma è possibile, ad esempio, usare il computer di sviluppo Windows per compilare moduli per dispositivi IoT Edge Linux. In uno scenario di questo tipo, è necessario assicurarsi che il computer di sviluppo esegua contenitori Linux. Mentre si procede con questa esercitazione, tenere presente la differenza tra *sistema operativo del computer di sviluppo* e *sistema operativo del contenitore*.

Questa esercitazione si riferisce ai dispositivi Windows che eseguono IoT Edge. I dispositivi IoT Edge Windows usano contenitori Windows. È consigliabile usare Visual Studio 2017 per lo sviluppo per dispositivi Windows, quindi in questa esercitazione verrà usata questa soluzione. È anche possibile usare Visual Studio Code, ma ci sono alcune differenze in termini di supporto tra i due strumenti.

La tabella seguente elenca gli scenari di sviluppo supportati per i **contenitori Windows** in Visual Studio Code e Visual Studio 2017.

|   | Visual Studio Code | Visual Studio 2017 |
| - | ------------------ | ------------------ |
| **Servizi di Azure** | Funzioni di Azure <br> Analisi di flusso di Azure |   |
| **Linguaggi** | C# (debug non supportato) | C <br> C# |
| **Altre informazioni** | [Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools per Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) |

Questa esercitazione illustra i passaggi di sviluppo per Visual Studio 2017. Se si preferisce usare Visual Studio Code, vedere le istruzioni in [Usare Visual Studio Code per sviluppare moduli per Azure IoT Edge ed eseguirne il debug](how-to-vs-code-develop-module.md).

## <a name="prerequisites"></a>Prerequisiti

Un computer di sviluppo:

* Windows 10 con l'aggiornamento 1809 o successivo.
* È possibile usare il proprio computer o una macchina virtuale, a seconda delle preferenze di sviluppo.
* Installare [Git](https://git-scm.com/). 
* Installare Azure IoT C SDK per Windows x64 tramite vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

<!--vcpkg only required for C development-->

Un dispositivo Azure IoT Edge in Windows:

* È consigliabile non eseguire IoT Edge nel computer di sviluppo, ma usare invece un dispositivo separato. Questa distinzione tra computer di sviluppo e dispositivo IoT Edge rispecchia in modo più accurato uno scenario di distribuzione reale e semplifica la comprensione dei diversi concetti.
* Se non è disponibile un secondo dispositivo, vedere l'articolo di avvio rapido per la creazione di un dispositivo IoT Edge in Azure con una [macchina virtuale Windows](quickstart.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure. 

## <a name="install-container-engine"></a>Installare il motore per i contenitori

I moduli IoT Edge vengono inseriti in pacchetti come contenitori, quindi è necessario un motore per i contenitori nel computer di sviluppo per compilare e gestire i contenitori. È consigliabile usare Docker Desktop per lo sviluppo in quanto offre numerose funzionalità ed è un motore per i contenitori molto diffuso. Con Docker Desktop in un computer Windows è possibile passare tra contenitori Linux e contenitori Windows, per sviluppare facilmente moduli per i diversi tipi di dispositivi IoT Edge. 

Usare la documentazione di Docker per eseguire l'installazione nel computer di sviluppo: 

* [Install Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/) (Installare Docker Desktop per Windows)

  * Quando si installa Docker Desktop per Windows, viene chiesto se si vuole usare i contenitori Linux o Windows. Per questa esercitazione usare **contenitori Windows**. Per altre informazioni, vedere [Switch between Windows and Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) (Passare tra contenitori Windows e Linux).


## <a name="set-up-visual-studio-and-tools"></a>Configurare Visual Studio e gli strumenti

Usare le estensioni IoT per Visual Studio 2017 per sviluppare moduli IoT Edge. Queste estensioni forniscono modelli di progetto, automatizzano la creazione del manifesto della distribuzione e consentono di monitorare e gestire i dispositivi IoT Edge. In questa sezione si installano Visual Studio e l'estensione IoT Edge, quindi si configura l'account di Azure per gestire le risorse dell'hub IoT da Visual Studio. 

1. Se Visual Studio non è già presente nel computer di sviluppo, [installare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017) con i carichi di lavoro seguenti: 

   * Sviluppo di Azure
   * Sviluppo per desktop con C++
   * Sviluppo multipiattaforma .NET Core

1. Se Visual Studio 2017 è già presente nel computer di sviluppo, assicurarsi che la versione sia 15.7 o successiva. Seguire i passaggi descritti in [Modificare Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) per aggiungere i carichi di lavoro richiesti, se non sono già presenti.

2. Scaricare e installare l'estensione [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) per Visual Studio 2017. 

3. Dopo aver completato le installazioni, aprire Visual Studio.

4. Selezionare **Visualizza** > **Cloud Explorer**. 

5. Selezionare l'icona del profilo in Cloud Explorer e accedere all'account di Azure, se non si è già connessi. 

6. Una volta effettuato l'accesso, vengono elencate le sottoscrizioni di Azure. Selezionare le sottoscrizioni a cui si vuole accedere tramite Cloud Explorer e quindi selezionare **Applica**. 

7. Espandere la sottoscrizione, quindi **Hub IoT** e infine l'hub IoT. Dovrebbe venire visualizzato un elenco di dispositivi IoT, che è possibile gestire con Explorer. 

   ![Accedere alle risorse dell'hub IoT in Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Creare un nuovo progetto di modulo

L'estensione Azure IoT Edge Tools fornisce modelli di progetto per tutti i linguaggi dei moduli IoT Edge supportati in Visual Studio 2017. Questi modelli hanno tutti i file e il codice necessari per distribuire un modulo funzionante per testare IoT Edge oppure per fornire un punto di partenza per personalizzare il modello con la propria logica di business. 

1. Eseguire Visual Studio come amministratore.

2. Selezionare **File** > **Nuovo** > **Progetto**. 

3. Nella finestra del nuovo progetto selezionare il tipo di progetto **Azure IoT** e scegliere il progetto **Azure IoT Edge**. Rinominare la soluzione e il progetto oppure accettare il nome predefinito **AzureIoTEdgeApp1**. Selezionare **OK** per creare il progetto. 

   ![Creare un nuovo progetto Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

4. Nella finestra relativa a modulo e applicazione IoT Edge configurare il progetto con i valori seguenti: 

   | Campo | Valore |
   | ----- | ----- |
   | Piattaforma applicativa | Deselezionare **Linux Amd64** e selezionare **WindowsAmd64**. |
   | Selezionare un modello: | Selezionare **C Module** (Modulo C). | 
   | Module project name (Nome progetto modulo) | Accettare il nome predefinito **IoTEdgeModule1**. | 
   | Docker image repository (Repository immagini Docker) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata in base al valore del nome del progetto di modulo. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br> Il repository di immagini finale sarà simile a \<nome registro\>.azurecr.io/iotedgemodule1. |

   ![Configurare il progetto per il Registro Container, il tipo di modulo e il dispositivo di destinazione](./media/tutorial-develop-for-windows/add-application-and-module.png)

5. Selezionare **OK** per applicare le modifiche. 

Una volta che il nuovo progetto viene caricato nella finestra di Visual Studio, dedicare un po' di tempo ad acquisire familiarità con i file creati: 

* Un progetto IoT Edge denominato **AzureIoTEdgeApp1.Windows.Amd64**.
    * La cartella **Modules** (Moduli) contiene i puntatori ai moduli inclusi nel progetto. In questo caso, dovrebbe essere semplicemente IoTEdgeModule1. 
    * Il file **deployment.template.json** è un modello che aiuta a creare un manifesto della distribuzione. Un *manifesto della distribuzione* è un file che definisce esattamente i moduli da distribuire in un dispositivo, come configurarli e il modo in cui comunicano tra loro e con il cloud. 
* Un progetto di modulo IoT Edge denominato **IoTEdgeModule1**.
    * Il file **main.c** contiene il codice del modulo C predefinito fornito con il modello di progetto. Il modulo predefinito accetta l'input da un'origine e lo passa all'hub IoT. 
    * Il file **module.json** contiene i dettagli del modulo, tra cui il repository di immagini completo, la versione delle immagini e il documento Dockerfile da usare per ogni piattaforma supportata.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Fornire le credenziali del registro all'agente IoT Edge

Il runtime IoT Edge richiede le credenziali del registro per eseguire il pull delle immagini dei contenitori nel dispositivo IoT Edge. Aggiungere queste credenziali al modello di distribuzione. 

1. Aprire il file **deployment.template.json**.

2. Trovare la proprietà **registryCredentials** nelle proprietà desiderate di $edgeAgent. 

3. Aggiornare la proprietà con le credenziali, usando questo formato: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Review the sample code

The solution template that you created includes sample code for an IoT Edge module. This sample module simply receives messages and then passes them on. The pipeline functionality demonstrates an important concept in IoT Edge, which is how modules communicate with each other.

Each module can have multiple *input* and *output* queues declared in their code. The IoT Edge hub running on the device routes messages from the output of one module into the input of one or more modules. The specific language for declaring inputs and outputs varies between languages, but the concept is the same across all modules. For more information about routing between modules, see [Declare routes](module-composition.md#declare-routes).

1. In the **main.c** file, find the **SetupCallbacksForModule** function.

2. This function sets up an input queue to receive incoming messages. It calls the C SDK module client function [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback). Review this function and see that it initializes an input queue called **input1**. 

   ![Find the input name in the SetInputMessageCallback constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Next, find the **InputQueue1Callback** function.

4. This function processes received messages and sets up an output queue to pass them along. It calls the C SDK module client function [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync). Review this function and see that it initializes an output queue called **output1**. 

   ![Find the output name in the SendEventToOutputAsync constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open the **deployment.template.json** file.

6. Find the **modules** property of the $edgeAgent desired properties. 

   There should be two modules listed here. The first is **tempSensor**, which is included in all the templates by default to provide simulated temperature data that you can use to test your modules. The second is the **IotEdgeModule1** module that you created as part of this project.

   This modules property declares which modules should be included in the deployment to your device or devices. 

7. Find the **routes** property of the $edgeHub desired properties. 

   One of the functions if the IoT Edge hub module is to route messages between all the modules in a deployment. Review the values in the routes property. The first route, **IotEdgeModule1ToIoTHub**, uses a wildcard character (**\***) to include any message coming from any output queue in the IoTEdgeModule1 module. These messages go into *$upstream*, which is a reserved name that indicates IoT Hub. The second route, **sensorToIotEdgeModule1**, takes messages coming from the tempSensor module and routes them to the *input1* input queue of the IotEdgeModule1 module. 

   ![Review routes in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## Build and push your solution

You've reviewed the module code and the deployment template to understand some key deployment concepts. Now, you're ready to build the IotEdgeModule1 container image and push it to your container registry. With the IoT tools extension for Visual Studio, this step also generates the deployment manifest based on the information in the template file and the module information from the solution files. 

### Sign in to Docker

Provide your container registry credentials to Docker on your development machine so that it can push your container image to be stored in the registry. 

1. Open PowerShell or a command prompt.

2. Sign in to Docker with the Azure container registry credentials that you saved after creating the registry. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sul comando [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilazione ed esecuzione del push

Il computer di sviluppo può ora accedere al Registro Container, come pure possono fare i dispositivi IoT Edge. A questo punto, è possibile trasformare il codice del progetto in un'immagine del contenitore. 

1. Fare clic con il pulsante destro del mouse sulla cartella del progetto **AzureIotEdgeApp1.Windows.Amd64** e scegliere **Build and Push IoT Edge Modules** (Compila moduli IoT Edge ed esegui il push). 

   ![Comando Build and push IoT Edge modules (Compila moduli IoT Edge ed esegui il push)](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Il comando di compilazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto della distribuzione completo, in base alle informazioni nel modello di distribuzione e altri file di soluzione. In secondo luogo, esegue `docker build` per compilare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel Registro Container. 

   Questo processo può richiedere alcuni minuti quando viene eseguito per la prima volta, ma alla successiva esecuzione dei comandi avviene più rapidamente. 

2. Aprire il file **deployment.windows-amd64.json** nella nuova cartella config creata. La cartella config potrebbe non venire visualizzata in Esplora soluzioni in Visual Studio. In questo caso, selezionare l'icona **Mostra tutti i file** sulla barra delle applicazioni di Esplora soluzioni.

3. Trovare il parametro **image** della sezione IotEdgeModule1. Si noti che l'immagine contiene il repository di immagini completo, con tag di architettura, nome e versione indicati nel file module.json.

4. Aprire il file **module.json** nella cartella IotEdgeModule1. 

5. Modificare il numero di versione per l'immagine del modulo. Modificare la versione e non $schema-version. Incrementare ad esempio il numero di versione della patch a **0.0.2**, come se fosse stata apportata una piccola correzione nel codice del modulo. 

   >[!TIP]
   >Le versioni dei moduli consentono di usare il controllo della versione e di testare le modifiche in un piccolo set di dispositivi prima di distribuire gli aggiornamenti nell'ambiente di produzione. Se non si incrementa la versione del modulo prima della compilazione e del push, si sovrascrive il repository nel Registro Container. 

6. Salvare le modifiche nel file module.json.

7. Fare di nuovo clic con il pulsante destro del mouse sulla cartella del progetto **AzureIotEdgeApp1.Windows.Amd64** e scegliere di nuovo **Build and Push IoT Edge Modules** (Compila moduli IoT Edge ed esegui il push). 

8. Aprire di nuovo il file **deployment.windows-amd64.json**. Si noti che non è stato creato un nuovo file quando è stato eseguito di nuovo il comando di compilazione e push. È invece stato aggiornato lo stesso file per riflettere le modifiche. L'immagine IotEdgeModule1 ora punta alla versione 0.0.2 del contenitore. Questa modifica nel manifesto della distribuzione indica al dispositivo IoT Edge che è disponibile una nuova versione di un modulo di cui eseguire il pull. 

9. Per verificare ulteriormente il risultato del comando di compilazione e push, aprire il [portale di Azure](https://portal.azure.com) e passare al Registro Container. 

10. Nel Registro Container selezionare **Repository** e quindi **iotedgemodule1**. Verificare che sia stato eseguito il push di entrambe le versioni dell'immagine nel registro.

    ![Visualizzare entrambe le versioni dell'immagine nel Registro Container](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Risolvere problemi

Se si verificano errori durante la compilazione e il push dell'immagine del modulo, spesso il problema è legato alla configurazione di Docker nel computer di sviluppo. Usare le indicazioni seguenti per esaminare la configurazione: 

* È stato eseguito il comando `docker login` usando le credenziali copiate dal Registro Container? Queste credenziali sono diverse rispetto a quelle usate per accedere ad Azure. 
* Il repository di contenitori è corretto? Il nome del Registro Container e quello del modulo sono corretti? Aprire il file **module.json** nella cartella IotEdgeModule1 per controllare. Il valore del repository deve essere simile a **\<nome registro\>.azurecr.io/iotedgemodule1**. 
* Se è stato usato un nome diverso da **IotEdgeModule1** per il modulo, il nome è coerente in tutta la soluzione?
* Il computer esegue contenitori dello stesso tipo di quelli che si stanno compilando? Questa esercitazione è relativa ai dispositivi IoT Edge Windows, quindi i file di Visual Studio devono avere l'estensione **windows-amd64** e Docker Desktop deve eseguire contenitori Windows. 

## <a name="deploy-modules-to-device"></a>Distribuire i moduli nel dispositivo

Si è verificato che le immagini dei contenitori di cui è stata eseguita la compilazione sono archiviate nel Registro Container, quindi è possibile eseguirne la distribuzione in un dispositivo. Assicurarsi che il dispositivo IoT Edge sia in esecuzione. 

1. Aprire Cloud Explorer in Visual Studio ed espandere i dettagli per l'hub IoT. 

2. Selezionare il nome del dispositivo in cui eseguire la distribuzione. Nell'elenco **Azioni** selezionare **Creare distribuzione**.

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/tutorial-develop-for-windows/create-deployment.png)


3. In Esplora file passare alla cartella config del progetto e selezionare il file **deployment.windows-amd64.json**. Questo file si trova spesso in `C:\Users\<username>\source\repos\AzureIotEdgeApp1\AzureIotEdgeApp1.Windows.Amd64\config\deployment.windows-amd64.json`

   Non usare il file deployment.template.json, nel quale non sono presenti i valori completi delle immagini dei moduli. 

4. Espandere i dettagli per il dispositivo IoT Edge in Cloud Explorer per visualizzare i moduli nel dispositivo.

5. Usare il pulsante **Aggiorna** per aggiornare lo stato del dispositivo e verificare che i moduli tempSensor e IotEdgeModule1 siano stati distribuiti nel dispositivo. 


   ![Visualizzare i moduli in esecuzione nel dispositivo IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visualizzare i messaggi dal dispositivo

Il codice di IotEdgeModule1 riceve i messaggi tramite la coda di input e li passa attraverso la coda di output. Il manifesto della distribuzione ha dichiarato le route che hanno passato i messaggi da tempSensor a IotEdgeModule1 e quindi hanno inoltrato i messaggi da IotEdgeModule1 all'hub IoT. L'estensione Azure IoT Edge Tools per Visual Studio consente di visualizzare i messaggi che arrivano all'hub IoT dai singoli dispositivi. 

1. In Visual Studio Cloud Explorer selezionare il nome del dispositivo IoT Edge in cui è stata eseguita la distribuzione. 

2. Dal menu **Azioni** scegliere **Start Monitoring D2C Message** (Avvia il monitoraggio dei messaggi D2C).

3. Esaminare la sezione **Output** in Visual Studio per vedere i messaggi in arrivo nell'hub IoT. 

   Per l'avvio di entrambi i moduli, potrebbero essere necessari alcuni minuti. Il runtime IoT Edge deve ricevere il nuovo manifesto della distribuzione, eseguire il pull delle immagini dei moduli dal runtime del contenitore e quindi avviare ogni nuovo modulo. Se 

   ![Visualizzare i messaggi in arrivo da dispositivo a cloud](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visualizzare le modifiche nel dispositivo

Se si vuole vedere cosa accade nel dispositivo, usare i comandi in questa sezione per controllare il runtime IoT Edge e i moduli in esecuzione nel dispositivo. 

I comandi in questa sezione sono destinati al dispositivo IoT Edge, non al computer di sviluppo. Se si usa una macchina virtuale per il dispositivo IoT Edge, connettersi ora. In Azure passare alla pagina di panoramica della macchina virtuale e selezionare **Connetti** per accedere a Connessione Desktop remoto. Nel dispositivo aprire una finestra di comando o di PowerShell per eseguire i comandi `iotedge`.

* Visualizzare tutti i moduli distribuiti nel dispositivo e controllarne lo stato:

   ```cmd
   iotedge list
   ```

   Dovrebbero essere presenti quattro moduli: i due moduli del runtime IoT Edge, tempSensor e IotEdgeModule1. Tutti e quattro i moduli devono essere indicati come in esecuzione.

* Esaminare i log per un modulo specifico:

   ```cmd
   iotedge logs <module name>
   ```

   I moduli IoT Edge fanno distinzione tra maiuscole e minuscole. 

   I log di tempSensor e IotEdgeModule1 devono visualizzare i messaggi in corso di elaborazione. Il modulo edgeAgent è responsabile dell'avvio degli altri moduli, quindi i relativi log conterranno le informazioni sull'implementazione del manifesto della distribuzione. Se un modulo non è elencato o non è in esecuzione, i log di edgeAgent conterranno probabilmente gli errori. Il modulo edgeHub è responsabile delle comunicazioni tra i moduli e l'hub IoT. Se i moduli sono in esecuzione, ma i messaggi non arrivano all'hub IoT, i log di edgeHub conterranno probabilmente gli errori. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato Visual Studio 2017 nel computer di sviluppo ed è stato distribuito il primo modulo IoT Edge. Ora che sono stati appresi i concetti di base, provare ad aggiungere funzionalità a un modulo in modo che possa analizzare i dati che passano attraverso di esso. Scegliere il linguaggio preferito: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)