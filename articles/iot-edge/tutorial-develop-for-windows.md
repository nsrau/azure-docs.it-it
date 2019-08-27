---
title: Sviluppare moduli per dispositivi Windows - Azure IoT Edge | Microsoft Docs
description: Questa esercitazione illustra in modo dettagliato come configurare il computer di sviluppo e le risorse cloud per sviluppare moduli IoT Edge usando contenitori Windows per dispositivi Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/15/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 66fa7c2f61af250e4b63b67f6941bed768bd94c4
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541921"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Esercitazione: Sviluppare moduli IoT Edge per dispositivi Windows

Usare Visual Studio per sviluppare e distribuire codice in dispositivi Windows che eseguono IoT Edge.

Nela procedura di avvio rapido è stato creato un dispositivo IoT Edge usando una macchina virtuale Windows ed è stato distribuito un modulo predefinito da Azure Marketplace. Questa esercitazione illustra in modo dettagliato come sviluppare e distribuire codice personalizzato in un dispositivo IoT Edge. Questa esercitazione è un prerequisito utile per le altre esercitazioni, che illustrano in modo più dettagliato linguaggi di programmazione o servizi di Azure specifici. 

Questa esercitazione usa come esempio la distribuzione di un **modulo C# in un dispositivo Windows**. Questo esempio è stato scelto perché rappresenta lo scenario di sviluppo più comune. Se il proprio interesse è rivolto allo sviluppo in un linguaggio diverso o si prevede di distribuire servizi di Azure come moduli, questa esercitazione è comunque utile per acquisire informazioni sugli strumenti di sviluppo. Una volta appresi i concetti di sviluppo, è possibile scegliere il linguaggio o il servizio di Azure preferito per approfondire i dettagli. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare il computer di sviluppo.
> * Usare IoT Edge Tools per Visual Studio per creare un nuovo progetto.
> * Compilare il progetto come contenitore e archiviarlo in un Registro Azure Container.
> * Distribuire il codice in un dispositivo IoT Edge. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Concetti chiave

Questa esercitazione illustra in modo dettagliato le fasi di sviluppo di un modulo IoT Edge. Un *modulo IoT Edge*, talvolta detto semplicemente *modulo*, è un contenitore in cui è presente codice eseguibile. È possibile distribuire uno o più moduli in un dispositivo IoT Edge. I moduli eseguono attività specifiche, ad esempio l'inserimento di dati provenienti dai sensori, l'esecuzione di operazioni di analisi o pulizia dei dati oppure l'invio di messaggi a un hub IoT. Per altre informazioni, vedere [Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md).

Quando si sviluppano moduli IoT Edge, è importante comprendere la differenza tra il computer di sviluppo e il dispositivo IoT Edge di destinazione in cui il modulo verrà distribuito. Il contenitore creato per inserire il codice dei moduli deve corrispondere al sistema operativo del *dispositivo di destinazione*. Per lo sviluppo di contenitori Windows, questo concetto è più semplice perché i contenitori Windows vengono eseguiti solo nei sistemi operativi Windows. Ma è possibile, ad esempio, usare il computer di sviluppo Windows per compilare moduli per dispositivi IoT Edge Linux. In uno scenario di questo tipo, è necessario assicurarsi che il computer di sviluppo esegua contenitori Linux. Mentre si procede con questa esercitazione, tenere presente la differenza tra *sistema operativo del computer di sviluppo* e *sistema operativo del contenitore*.

Questa esercitazione si riferisce ai dispositivi Windows che eseguono IoT Edge. I dispositivi IoT Edge Windows usano contenitori Windows. È consigliabile usare Visual Studio per lo sviluppo per dispositivi Windows, quindi in questa esercitazione verrà usata questa soluzione. È anche possibile usare Visual Studio Code, ma ci sono alcune differenze in termini di supporto tra i due strumenti.

La tabella seguente elenca gli scenari di sviluppo supportati per i **contenitori Windows** in Visual Studio Code e Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Servizi di Azure** | Funzioni di Azure <br> Analisi di flusso di Azure |   |
| **Linguaggi** | C# (debug non supportato) | C <br> C# |
| **Altre informazioni** | [Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools per Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge Tools per Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Prerequisiti

Un computer di sviluppo:

* Windows 10 con l'aggiornamento 1809 o successivo.
* È possibile usare il proprio computer o una macchina virtuale, a seconda delle preferenze di sviluppo.
* Installare [Git](https://git-scm.com/). 

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

Le estensioni IoT per Visual Studio semplificano lo sviluppo di moduli IoT Edge. Queste estensioni forniscono modelli di progetto, automatizzano la creazione del manifesto della distribuzione e consentono di monitorare e gestire i dispositivi IoT Edge. In questa sezione si installano Visual Studio e l'estensione IoT Edge, quindi si configura l'account di Azure per gestire le risorse dell'hub IoT da Visual Studio. 

Questa esercitazione illustra i passaggi di sviluppo per Visual Studio 2019. Se si usa Visual Studio 2017 (versione 15.7 o successiva), i passaggi sono molto simili. Se si preferisce usare Visual Studio Code, vedere le istruzioni in [Usare Visual Studio Code per sviluppare moduli per Azure IoT Edge ed eseguirne il debug](how-to-vs-code-develop-module.md). 

1. Preparare Visual Studio 2019 nel computer di sviluppo. 

   * Se Visual Studio non è già presente nel computer di sviluppo, [installare Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) con i carichi di lavoro seguenti: 

      * Sviluppo di Azure
      * Sviluppo per desktop con C++
      * Sviluppo multipiattaforma .NET Core

   * Se Visual Studio 2019 è già presente nel computer di sviluppo, seguire la procedura descritta in [Modificare Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) per aggiungere i carichi di lavoro necessari.

2. Scaricare e installare l'estensione [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) per Visual Studio 2019. 

   Se si usa Visual Studio 2017 (versione 15.7 o successiva), scaricare e installare [Azure IoT Edge Tools per Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Dopo aver completato le installazioni, aprire Visual Studio 2019 e selezionare **Continua senza codice**.

4. Selezionare **Visualizza** > **Cloud Explorer**. 

5. Selezionare l'icona del profilo in Cloud Explorer e accedere all'account di Azure, se non si è già connessi. 

6. Una volta effettuato l'accesso, vengono elencate le sottoscrizioni di Azure. Espandere la sottoscrizione che contiene l'hub IoT. 

7. All'interno della sottoscrizione espandere **Hub IoT** e quindi l'hub IoT. Dovrebbe venire visualizzato un elenco di dispositivi IoT, che è possibile gestire con Explorer. 

   ![Accedere alle risorse dell'hub IoT in Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Creare un nuovo progetto di modulo

L'estensione Azure IoT Edge Tools fornisce modelli di progetto per tutti i linguaggi dei moduli IoT Edge supportati in Visual Studio. Questi modelli includono il codice e tutti i file necessari per distribuire un modulo funzionante per testare IoT Edge oppure offrono un punto di partenza per personalizzare il modello con la propria logica di business. 

1. Selezionare **File** > **Nuovo** > **Progetto**.

2. Nella finestra del nuovo progetto cercare **IoT Edge** e scegliere il progetto **Azure IoT Edge (Windows amd64)** . Fare clic su **Avanti**. 

   ![Creare un nuovo progetto Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. Nella finestra Configura il nuovo progetto assegnare al progetto e alla soluzione un nuovo nome descrittivo, ad esempio **CSharpTutorialApp**. Fare clic su **Crea** per creare il progetto.

   ![Configurare un nuovo progetto Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)

4. Nella finestra Aggiungi modulo configurare il progetto con i valori seguenti: 

   | Campo | Valore |
   | ----- | ----- |
   | Modello di Visual Studio | Selezionare **C# Module** (Modulo C#). | 
   | Nome modulo | Accettare il valore predefinito **IotEdgeModule1**. | 
   | URL del repository | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata in base al valore del nome del progetto di modulo. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br> Il repository di immagini finale sarà simile a \<nome registro\>.azurecr.io/iotedgemodule1. |

      ![Configurare il progetto per il dispositivo di destinazione, il tipo di modulo e il registro contenitori](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Selezionare **Aggiungi** per creare il modulo. 

Una volta che il nuovo progetto viene caricato nella finestra di Visual Studio, dedicare un po' di tempo ad acquisire familiarità con i file creati: 

* Progetto IoT Edge denominato **CSharpTutorialApp**.
    * La cartella **Modules** (Moduli) contiene i puntatori ai moduli inclusi nel progetto. In questo caso sarà semplicemente IotEdgeModule1. 
    * Il file **deployment.template.json** è un modello che aiuta a creare un manifesto della distribuzione. Un *manifesto della distribuzione* è un file che definisce esattamente i moduli da distribuire in un dispositivo, come configurarli e il modo in cui comunicano tra loro e con il cloud. 
* Un progetto di modulo IoT Edge denominato **IotEdgeModule1**.
    * Il file **program.cs** contiene il codice del modulo C# predefinito disponibile con il modello di progetto. Il modulo predefinito accetta l'input da un'origine e lo passa all'hub IoT. 
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
   ```

4. Salvare il file deployment.template.json. 

### <a name="review-the-sample-code"></a>Esaminare il codice di esempio

Il modello di soluzione creato include codice di esempio per un modulo IoT Edge. Questo modulo di esempio riceve semplicemente i messaggi e quindi li invia. La funzionalità di pipeline illustra un concetto importante in IoT Edge, ovvero come i moduli comunicano tra loro.

Per ogni modulo nel codice possono essere dichiarate più code di *input* e *output*. L'hub di IoT Edge in esecuzione nel dispositivo instrada i messaggi dall'output di un modulo all'input di uno o più moduli. Il linguaggio specifico per la dichiarazione di input e output varia a seconda dei linguaggi, ma il concetto è lo stesso in tutti i moduli. Per altre informazioni sul routing tra moduli, vedere [Dichiarare le route](module-composition.md#declare-routes).

L'esempio di codice C# disponibile con il modello di progetto usa la [classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) da IoT Hub SDK per .NET. 

1. Nel file **program.cs** individuare il metodo **SetInputMessageHandlerAsync**.

2. Il metodo [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) consente di configurare una coda di input per la ricezione di messaggi in arrivo. Esaminare questo metodo e vedere in che modo inizializza una coda di input denominata **input1**. 

   ![Individuare il nome di input nel costruttore SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Individuare quindi il metodo **SendEventAsync**.

4. Il metodo [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) consente di elaborare i messaggi ricevuti e di configurare una coda di output per passarli. Esaminare questo metodo e vedere che inizializza una coda di output denominata **output1**. 

   ![Individuare il nome di output nel costruttore SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Aprire il file **deployment.template.json**.

6. Trovare la proprietà **modules** nelle proprietà desiderate di $edgeAgent. 

   Dovrebbero essere elencati due moduli. Il primo è **tempSensor**, incluso in tutti i modelli per impostazione predefinita per fornire dati di temperatura simulati che è possibile usare per testare i moduli. Il secondo è il modulo **IotEdgeModule1**, che è stato creato come parte di questo progetto.

   La proprietà di questo modulo dichiara quali moduli includere nella distribuzione in uno o più dispositivi. 

7. Trovare la proprietà **routes** nelle proprietà desiderate di $edgeHub. 

   Una delle funzioni del modulo dell'hub di IoT Edge è quella di instradare i messaggi tra tutti i moduli in una distribuzione. Esaminare i valori nella proprietà routes. La prima route, **IotEdgeModule1ToIoTHub**, usa un carattere jolly ( **\*** ) per includere tutti i messaggi provenienti da qualsiasi coda di output nel modulo IotEdgeModule1. Questi messaggi vengono inseriti in *$upstream*, un nome riservato che indica l'hub IoT. La seconda route, **sensorToIotEdgeModule1**, instrada i messaggi provenienti dal modulo tempSensor alla coda di input *input1* del modulo IotEdgeModule1. 

   ![Esaminare le route in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>Compilare ed eseguire il push della soluzione

Sono stati esaminati il codice dei moduli e il modello di distribuzione per comprendere alcuni concetti chiave relativi alla distribuzione. Ora è possibile creare l'immagine del contenitore IotEdgeModule1 ed eseguirne il push nel registro contenitori. Con l'estensione IoT Tools per Visual Studio, questo passaggio genera anche il manifesto della distribuzione in base alle informazioni incluse nel file modello e alle informazioni sui moduli nei file di soluzione. 

### <a name="sign-in-to-docker"></a>Accedere a Docker

Specificare le credenziali del registro contenitori in Docker nel computer di sviluppo per consentire il push dell'immagine del contenitore da archiviare nel registro. 

1. Aprire PowerShell o un prompt dei comandi.

2. Accedere a Docker con le credenziali del Registro Azure Container di cui è stato eseguito il salvataggio dopo la creazione del registro. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sul comando [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilazione ed esecuzione del push

Il computer di sviluppo può ora accedere al Registro Container, come pure possono fare i dispositivi IoT Edge. A questo punto, è possibile trasformare il codice del progetto in un'immagine del contenitore. 

1. Fare clic con il pulsante destro del mouse sulla cartella del progetto **CSharpTutorialApp** e scegliere **Build and Push IoT Edge Modules** (Compila moduli IoT Edge ed esegui il push). 

   ![Comando Build and push IoT Edge modules (Compila moduli IoT Edge ed esegui il push)](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Il comando di compilazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto completo della distribuzione, basato sulle informazioni del modello di distribuzione e di altri file della soluzione. In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel Registro Container. 

   Questo processo può richiedere alcuni minuti quando viene eseguito per la prima volta, ma alla successiva esecuzione dei comandi avviene più rapidamente. 

2. Aprire il file **deployment.windows-amd64.json** nella nuova cartella config creata. La cartella config potrebbe non venire visualizzata in Esplora soluzioni in Visual Studio. In questo caso, selezionare l'icona **Mostra tutti i file** sulla barra delle applicazioni di Esplora soluzioni.

3. Trovare il parametro **image** della sezione IotEdgeModule1. Si noti che l'immagine contiene il repository di immagini completo, con tag di architettura, nome e versione indicati nel file module.json.

4. Aprire il file **module.json** nella cartella IotEdgeModule1. 

5. Modificare il numero di versione per l'immagine del modulo. Modificare la versione e non $schema-version. Incrementare ad esempio il numero di versione della patch a **0.0.2**, come se fosse stata apportata una piccola correzione nel codice del modulo. 

   >[!TIP]
   >Le versioni dei moduli consentono di usare il controllo della versione e di testare le modifiche in un piccolo set di dispositivi prima di distribuire gli aggiornamenti nell'ambiente di produzione. Se non si incrementa la versione del modulo prima della compilazione e del push, si sovrascrive il repository nel Registro Container. 

6. Salvare le modifiche nel file module.json.

7. Fare di nuovo clic con il pulsante destro del mouse sulla cartella del progetto **CSharpTutorialApp** e scegliere di nuovo **Build and Push IoT Edge Modules** (Compila moduli IoT Edge ed esegui il push). 

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


3. In Esplora file passare alla cartella config del progetto e selezionare il file **deployment.windows-amd64.json**. Questo file si trova spesso in `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Non usare il file deployment.template.json, nel quale non sono presenti i valori completi delle immagini dei moduli. 

4. Espandere i dettagli per il dispositivo IoT Edge in Cloud Explorer per visualizzare i moduli nel dispositivo.

5. Usare il pulsante **Aggiorna** per aggiornare lo stato del dispositivo e verificare che i moduli tempSensor e IotEdgeModule1 siano stati distribuiti nel dispositivo. 


   ![Visualizzare i moduli in esecuzione nel dispositivo IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visualizzare i messaggi dal dispositivo

Il codice di IotEdgeModule1 riceve i messaggi tramite la coda di input e li passa attraverso la coda di output. Il manifesto della distribuzione ha dichiarato le route che hanno passato i messaggi da tempSensor a IotEdgeModule1 e quindi hanno inoltrato i messaggi da IotEdgeModule1 all'hub IoT. L'estensione Azure IoT Edge Tools per Visual Studio consente di visualizzare i messaggi che arrivano all'hub IoT dai singoli dispositivi. 

1. In Visual Studio Cloud Explorer selezionare il nome del dispositivo IoT Edge in cui è stata eseguita la distribuzione. 

2. Dal menu **Azioni** scegliere **Start Monitoring Built-in Event Endpoint** (Avvia il monitoraggio dell'endpoint evento predefinito).

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

In questa esercitazione è stato configurato Visual Studio 2019 nel computer di sviluppo ed è stato distribuito il primo modulo IoT Edge. Ora che sono stati appresi i concetti di base, provare ad aggiungere funzionalità a un modulo in modo che possa analizzare i dati che passano attraverso di esso. Scegliere il linguaggio preferito: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
