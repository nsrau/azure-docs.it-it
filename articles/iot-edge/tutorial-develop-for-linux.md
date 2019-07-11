---
title: Sviluppare moduli per dispositivi Linux - Azure IoT Edge | Microsoft Docs
description: Questa esercitazione illustra in modo dettagliato come configurare il computer di sviluppo e le risorse cloud per sviluppare moduli IoT Edge usando contenitori Linux per dispositivi Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: e5499afebf29df2942e74148b33797844fa9c880
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051889"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Esercitazione: Sviluppare moduli IoT Edge per dispositivi Linux

Usare Visual Studio Code per sviluppare e distribuire codice in dispositivi Linux che eseguono IoT Edge. 

Negli articoli di avvio rapido è stato creato un dispositivo IoT Edge usando una macchina virtuale Linux ed è stato distribuito un modulo predefinito da Azure Marketplace. Questa esercitazione illustra in modo dettagliato come sviluppare e distribuire codice personalizzato in un dispositivo IoT Edge. Questa esercitazione è un prerequisito utile per tutte le altre esercitazioni, che illustrano in modo più dettagliato i concetti relativi a linguaggi di programmazione o servizi di Azure specifici. 

Questa esercitazione usa l'esempio di distribuzione di un **modulo C# in un dispositivo Linux**. Questo esempio è stato scelto perché è lo scenario attivo più comune per gli sviluppatori per le soluzioni IoT Edge. Anche se si prevede di usare un linguaggio diverso o di distribuire un servizio di Azure, questa esercitazione è comunque utile per apprendere i concetti e gli strumenti di sviluppo. Dopo aver completato questa introduzione al processo di sviluppo, è possibile scegliere il linguaggio o il servizio di Azure preferito per approfondire i dettagli. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare il computer di sviluppo.
> * Usare IoT Edge Tools per Visual Studio Code per creare un nuovo progetto.
> * Compilare il progetto come contenitore e archiviarlo in un Registro Azure Container.
> * Distribuire il codice in un dispositivo IoT Edge. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Concetti chiave

Questa esercitazione illustra in modo dettagliato le fasi di sviluppo di un modulo IoT Edge. Un *modulo IoT Edge*, talvolta detto semplicemente *modulo*, è un contenitore in cui è presente codice eseguibile. È possibile distribuire uno o più moduli in un dispositivo IoT Edge. I moduli eseguono attività specifiche, ad esempio l'inserimento di dati provenienti dai sensori, l'esecuzione di operazioni di analisi o pulizia dei dati oppure l'invio di messaggi a un hub IoT. Per altre informazioni, vedere [Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md).

Quando si sviluppano moduli IoT Edge, è importante comprendere la differenza tra il computer di sviluppo e il dispositivo IoT Edge di destinazione in cui il modulo verrà distribuito. Il contenitore creato per inserire il codice dei moduli deve corrispondere al sistema operativo del *dispositivo di destinazione*. Ad esempio, lo scenario più comune è quello in cui si sviluppa in un computer Windows un modulo destinato a un dispositivo Linux che esegue IoT Edge. In tal caso, il sistema operativo del contenitore è Linux. Mentre si procede con questa esercitazione, tenere presente la differenza tra *sistema operativo del computer di sviluppo* e *sistema operativo del contenitore*.

Questa esercitazione si riferisce ai dispositivi Linux che eseguono IoT Edge. È possibile usare il sistema operativo preferito per il computer di sviluppo, purché questo possa eseguire contenitori Linux. È consigliabile usare Visual Studio Code per lo sviluppo per dispositivi Linux, quindi in questa esercitazione verrà usata questa soluzione. È possibile usare anche Visual Studio, ma esistono alcune differenze in termini di supporto tra i due strumenti.

La tabella seguente elenca gli scenari di sviluppo supportati per i **contenitori Linux** in Visual Studio Code e Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architettura dei dispositivi Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Servizi di Azure** | Funzioni di Azure <br> Analisi di flusso di Azure <br> Azure Machine Learning |   |
| **Linguaggi** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Altre informazioni** | [Azure IoT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools per Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge Tools per Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

Questa esercitazione illustra i passaggi di sviluppo per Visual Studio Code. Se si preferisce usare Visual Studio, vedere le istruzioni riportate in [Usare Visual Studio 2019 per lo sviluppo e il debug di moduli per Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Prerequisiti

Un computer di sviluppo:

* È possibile usare il proprio computer o una macchina virtuale, a seconda delle preferenze di sviluppo.
* Per lo sviluppo di moduli IoT Edge per dispositivi Linux, è possibile usare la maggior parte dei sistemi operativi in grado di eseguire un motore per i contenitori. Questa esercitazione usa un computer Windows, ma vengono sottolineate le differenze note in MacOS o Linux. 
* Installare [Git](https://git-scm.com/) per eseguire il pull dei pacchetti di modelli di moduli più avanti in questa esercitazione.  
* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Un dispositivo Azure IoT Edge in Linux:

* È consigliabile non eseguire IoT Edge nel computer di sviluppo, ma usare invece un dispositivo separato. Questa distinzione tra computer di sviluppo e dispositivo IoT Edge rispecchia in modo più accurato uno scenario di distribuzione reale e semplifica la comprensione dei diversi concetti.
* Se non è disponibile un secondo dispositivo, vedere l'articolo di avvio rapido per la creazione di un dispositivo IoT Edge in Azure con una [macchina virtuale Linux](quickstart-linux.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure. 

## <a name="install-container-engine"></a>Installare il motore per i contenitori

I moduli IoT Edge vengono inseriti in pacchetti come contenitori, quindi è necessario un motore per i contenitori nel computer di sviluppo per compilare e gestire i contenitori. È consigliabile usare Docker Desktop per lo sviluppo in quanto offre numerose funzionalità ed è un motore per i contenitori molto diffuso. Con Docker Desktop in un dispositivo Windows è possibile passare tra contenitori Linux e contenitori Windows, per sviluppare facilmente moduli per i diversi tipi di dispositivi IoT Edge. 

Usare la documentazione di Docker per eseguire l'installazione nel computer di sviluppo: 

* [Install Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/) (Installare Docker Desktop per Windows)

  * Quando si installa Docker Desktop per Windows, viene chiesto se si vuole usare i contenitori Linux o Windows. Questa decisione può essere modificata in qualsiasi momento usando una semplice opzione. Per questa esercitazione si usano i contenitori Linux perché i moduli sono destinati a dispositivi Linux. Per altre informazioni, vedere [Switch between Windows and Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) (Passare tra contenitori Windows e Linux).

* [Install Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/install/) (Installare Docker Desktop per Mac)

* Leggere l'articolo [About Docker CE](https://docs.docker.com/install/) (Informazioni su Docker CE) per informazioni sull'installazione in diverse piattaforme Linux.

## <a name="set-up-vs-code-and-tools"></a>Configurare VS Code e gli strumenti

Usare le estensioni IoT per Visual Studio Code per sviluppare moduli IoT Edge. Queste estensioni forniscono modelli di progetto, automatizzano la creazione del manifesto della distribuzione e consentono di monitorare e gestire i dispositivi IoT Edge. In questa sezione si installano Visual Studio Code e l'estensione IoT, quindi si configura l'account di Azure per gestire le risorse dell'hub IoT da Visual Studio Code. 

1. Installare [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo. 

2. Una volta completata l'installazione, selezionare **Visualizza** > **Estensioni**. 

3. Cercare **Azure IoT Tools**, che è in realtà una raccolta di estensioni che aiutano a interagire con l'hub IoT e i dispositivi IoT, nonché a sviluppare moduli IoT Edge. 

4. Selezionare **Installa**. Ogni estensione inclusa viene installata singolarmente. 

5. Una volta completata l'installazione delle estensioni, aprire il riquadro comandi selezionando **Visualizza** > **Riquadro comandi**. 

6. Nel riquadro comandi cercare e selezionare **Azure: Sign in** (Azure: Accedi). Seguire le istruzioni per accedere all'account Azure. 

7. Di nuovo nel riquadro comandi cercare e selezionare **Hub IoT di Azure: Selezionare l'hub IoT**. Seguire le istruzioni per selezionare la sottoscrizione di Azure e l'hub IoT. 

7. Aprire la sezione Explorer di Visual Studio Code selezionando l'icona sulla barra delle attività a sinistra oppure selezionando **Visualizza** > **Explorer**. 

8. Nella parte inferiore della sezione Explorer espandere il menu compresso **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Dovrebbero venire visualizzati i dispositivi e i dispositivi IoT Edge associati all'hub IoT selezionato tramite il riquadro comandi. 

   ![Visualizzare i dispositivi nell'hub IoT](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Creare un nuovo progetto di modulo

L'estensione Azure IoT Tools fornisce modelli di progetto per tutti i linguaggi dei moduli IoT Edge supportati in Visual Studio Code. Questi modelli includono il codice e tutti i file necessari per distribuire un modulo funzionante per testare IoT Edge oppure offrono un punto di partenza per personalizzare il modello con la propria logica di business. 

Per questa esercitazione, si userà il modello di modulo C# perché è quello usato più di frequente. 

### <a name="create-a-project-template"></a>Creare un modello di progetto

Nel riquadro comandi di Visual Studio Code cercare e selezionare **Azure IoT Edge: Nuova soluzione IoT Edge**. Seguire le istruzioni e usare i valori seguenti per creare la soluzione: 

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **C# Module** (Modulo C#). |
   | Provide a module name (Specificare un nome per il modulo) | Accettare il valore predefinito **SampleModule**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore viene preinserita in base al nome specificato nell'ultimo passaggio. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br> Il repository di immagini finale sarà simile a \<nome registro\>.azurecr.io/samplemodule. |
 
   ![Specificare il repository di immagini Docker](./media/tutorial-develop-for-linux/image-repository.png)

Una volta che la nuova soluzione viene caricata nella finestra di Visual Studio Code, dedicare un po' di tempo ad acquisire familiarità con i file creati: 

* La cartella **.vscode** contiene un file denominato **launch.json**, usato per il debug dei moduli.
* La cartella **modules** contiene una cartella per ogni modulo nella soluzione. Al momento dovrebbe essere presente solo **SampleModule** o una cartella con il nome assegnato al modulo. La cartella SampleModule contiene il codice di programma principale, i metadati del modulo e diversi file Docker. 
* Il file con estensione **env** contiene le credenziali del Registro Container. Queste credenziali vengono condivise con il dispositivo IoT Edge in modo che possa accedere per eseguire il pull delle immagini dei contenitori. 
* I file **deployment.debug.template.json** e **deployment.template.json** sono modelli che aiutano a creare un manifesto della distribuzione. Un *manifesto della distribuzione* è un file che definisce esattamente i moduli da distribuire in un dispositivo, come configurarli e il modo in cui comunicano tra loro e con il cloud. I file di modello usano puntatori per alcuni valori. Quando si trasforma il modello in un reale manifesto della distribuzione, i puntatori vengono sostituiti con valori forniti da altri file di soluzione. Individuare i due segnaposto comuni nel modello di distribuzione: 

  * Nella sezione relativa alle credenziali del registro l'indirizzo viene inserito automaticamente in base alle informazioni fornite durante la creazione della soluzione. Nome utente e password fanno tuttavia riferimento alle variabili archiviate nel file con estensione env. Ciò serve per garantire la sicurezza, in quanto il file con estensione env è ignorato da git, mentre il modello di distribuzione no. 
  * Nella sezione SampleModule l'immagine del contenitore non viene inserita nemmeno se è stato fornito il repository di immagini durante la creazione della soluzione. Questo segnaposto punta al file **module.json** all'interno della cartella SampleModule. Se si passa a tale file, si noterà che il campo relativo all'immagine contiene il repository, ma anche un valore di tag costituito dalla versione e dalla piattaforma del contenitore. È possibile eseguire l'iterazione della versione manualmente, come parte del ciclo di sviluppo e si seleziona la piattaforma del contenitore usando un'opzione che verrà illustrata più avanti in questa sezione. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Fornire le credenziali del registro all'agente IoT Edge

Il file dell'ambiente archivia le credenziali per il registro contenitori e le condivide con il runtime IoT Edge. Il runtime necessita di queste credenziali per eseguire il pull delle immagini dei contenitori nel dispositivo IoT Edge. 

L'estensione IoT Edge cerca di eseguire il pull delle credenziali del Registro Container da Azure, per inserirle nel file di ambiente. Verificare se le credenziali sono già incluse. In caso contrario, aggiungerle:

1. Aprire il file con estensione **env** nella soluzione del modulo. 
2. Aggiungere i valori di **username** e **password** copiati dal Registro Azure Container.
3. Salvare le modifiche al file con estensione env. 

### <a name="select-your-target-architecture"></a>Selezionare l'architettura di destinazione

Attualmente, Visual Studio Code può sviluppare moduli C# per dispositivi Linux AMD64 e Linux ARM32v7. È necessario selezionare l'architettura di destinazione per ogni soluzione, perché ciò influisce sul modo in cui il contenitore viene compilato ed eseguito. L'impostazione predefinita è Linux AMD64. 

1. Aprire il riquadro comandi e cercare **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: Imposta la piattaforma di destinazione predefinita per la soluzione Edge) oppure selezionare l'icona del collegamento sulla barra laterale nella parte inferiore della finestra. 

   ![Selezionare l'icona dell'architettura sulla barra laterale](./media/tutorial-develop-for-linux/select-architecture.png)

2. Nel riquadro comandi selezionare l'architettura di destinazione nell'elenco di opzioni. Per questa esercitazione si usa una macchina virtuale Ubuntu come dispositivo IoT Edge, quindi si manterrà il valore predefinito **amd64**. 

### <a name="review-the-sample-code"></a>Esaminare il codice di esempio

Il modello di soluzione creato include codice di esempio per un modulo IoT Edge. Questo modulo di esempio riceve semplicemente i messaggi e quindi li invia. La funzionalità di pipeline illustra un concetto importante in IoT Edge, ovvero come i moduli comunicano tra loro.

Per ogni modulo nel codice possono essere dichiarate più code di *input* e *output*. L'hub di IoT Edge in esecuzione nel dispositivo instrada i messaggi dall'output di un modulo all'input di uno o più moduli. Il linguaggio specifico per la dichiarazione di input e output varia a seconda dei linguaggi, ma il concetto è lo stesso in tutti i moduli. Per altre informazioni sul routing tra moduli, vedere [Dichiarare le route](module-composition.md#declare-routes).

L'esempio di codice C# disponibile con il modello di progetto usa la [classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) da IoT Hub SDK per .NET. 

1. Aprire il file **Program.cs**, all'interno della cartella **modules/SampleModule/** . 

2. In program.cs individuare il metodo **SetInputMessageHandlerAsync**.

2. Il metodo [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) consente di configurare una coda di input per la ricezione di messaggi in arrivo. Esaminare questo metodo e vedere in che modo inizializza una coda di input denominata **input1**. 

   ![Trovare il nome di input nel costruttore SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

3. Individuare quindi il metodo **SendEventAsync**.

4. Il metodo [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) consente di elaborare i messaggi ricevuti e di configurare una coda di output per passarli. Esaminare questo metodo e notare che inizializza una coda di output denominata **output1**. 

   ![Trovare il nome di output in SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Aprire il file **deployment.template.json**.

7. Trovare la proprietà **modules** nelle proprietà desiderate di $edgeAgent. 

   Dovrebbero essere elencati due moduli. Il primo è **tempSensor**, incluso in tutti i modelli per impostazione predefinita per fornire dati di temperatura simulati che è possibile usare per testare i moduli. Il secondo è il modulo **SampleModule**, che è stato creato come parte di questa soluzione.

7. In fondo al file trovare le proprietà desiderate per il modulo **$edgeHub**. 

   Una delle funzioni del modulo dell'hub di IoT Edge è quella di instradare i messaggi tra tutti i moduli in una distribuzione. Esaminare i valori nella proprietà **route**. La prima route, **SampleModuleToIoTHub**, usa un carattere jolly ( **\*** ) per indicare i messaggi provenienti da code di output nel modulo SampleModule. Questi messaggi vengono inseriti in *$upstream*, un nome riservato che indica l'hub IoT. La seconda route, sensorToSampleModule, instrada i messaggi provenienti dal modulo tempSensor alla coda di input *input1*, che come indicato nel codice di SampleModule è inizializzata. 

   ![Esaminare le route in deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Compilare ed eseguire il push della soluzione

Sono stati esaminati il codice dei moduli e il modello di distribuzione per comprendere alcuni concetti chiave relativi alla distribuzione. Ora è possibile compilare l'immagine del contenitore SampleModule ed eseguirne il push nel Registro Container. Con l'estensione IoT Tools per Visual Studio Code, questo passaggio genera anche il manifesto della distribuzione in base alle informazioni nel file modello e alle informazioni sui moduli nei file di soluzione. 

### <a name="sign-in-to-docker"></a>Accedere a Docker

Fornire a Docker le credenziali del Registro Container, per consentire il push dell'immagine del contenitore da archiviare nel registro. 

1. Aprire il terminale integrato di Visual Studio Code selezionando **Visualizza** > **Terminale**.

2. Accedere a Docker con le credenziali del Registro Azure Container di cui è stato eseguito il salvataggio dopo la creazione del registro. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sul comando [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilazione ed esecuzione del push 

Visual Studio Code può ora accedere al Registro Container, quindi è possibile trasformare il codice della soluzione in un'immagine del contenitore. 

1. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and Push IoT Edge Solution** (Compila ed esegui il push della soluzione IoT Edge). 

   ![Comando Build and push IoT Edge modules (Compila moduli IoT Edge ed esegui il push)](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Il comando di compilazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto completo della distribuzione, basato sulle informazioni del modello di distribuzione e di altri file della soluzione. In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel Registro Container. 

   Questo processo può richiedere alcuni minuti quando viene eseguito per la prima volta, ma alla successiva esecuzione dei comandi avviene più rapidamente. 

2. Aprire il file **deployment.amd64.json** nella nuova cartella config creata. Il nome del file riflette l'architettura di destinazione, quindi sarà diverso se si sceglie un'architettura diversa.

3. Si noti che i due parametri contenenti i segnaposto sono ora compilati con i valori appropriati. La sezione **registryCredentials** contiene nome utente e password del registro di cui è stato eseguito il pull dal file con estensione env. **SampleModule** contiene il repository di immagini completo, con tag di architettura, nome e versione indicati nel file module.json. 

4. Aprire il file **module.json** nella cartella SampleModule. 

5. Modificare il numero di versione per l'immagine del modulo. Modificare la versione e non $schema-version. Incrementare ad esempio il numero di versione della patch a **0.0.2**, come se fosse stata apportata una piccola correzione nel codice del modulo. 

   >[!TIP]
   >Le versioni dei moduli consentono di usare il controllo della versione e di testare le modifiche in un piccolo set di dispositivi prima di distribuire gli aggiornamenti nell'ambiente di produzione. Se non si incrementa la versione del modulo prima della compilazione e del push, si sovrascrive il repository nel Registro Container. 

6. Salvare le modifiche nel file module.json.

7. Fare di nuovo clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere di nuovo **Build and push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge).

8. Aprire di nuovo il file **deployment.amd64.json**. Si noti che non è stato creato un nuovo file quando è stato eseguito di nuovo il comando di compilazione e push. È invece stato aggiornato lo stesso file per riflettere le modifiche. L'immagine SampleModule ora punta alla versione 0.0.2 del contenitore. 

9. Per verificare ulteriormente il risultato del comando di compilazione e push, aprire il [portale di Azure](https://portal.azure.com) e passare al Registro Container. 

10. Nel Registro Container selezionare **Repository** e quindi **samplemodule**. Verificare che sia stato eseguito il push di entrambe le versioni dell'immagine nel registro.

    ![Visualizzare entrambe le versioni dell'immagine nel Registro Container](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Risolvere problemi

Se si verificano errori durante la compilazione e il push dell'immagine del modulo, spesso il problema è legato alla configurazione di Docker nel computer di sviluppo. Usare le indicazioni seguenti per esaminare la configurazione: 

* È stato eseguito il comando `docker login` usando le credenziali copiate dal Registro Container? Queste credenziali sono diverse rispetto a quelle usate per accedere ad Azure. 
* Il repository di contenitori è corretto? Il nome del Registro Container e quello del modulo sono corretti? Aprire il file **module.json** nella cartella SampleModule per controllare. Il valore del repository deve essere simile a **\<nome registro\>.azurecr.io/samplemodule**. 
* Se è stato usato un nome diverso da **SampleModule** per il modulo, il nome è coerente in tutta la soluzione?
* Il computer esegue contenitori dello stesso tipo di quelli che si stanno compilando? Questa esercitazione è relativa ai dispositivi IoT Edge Linux, quindi in Visual Studio Code deve essere presente l'indicazione **amd64** o **arm32v7** sulla barra laterale e Docker Desktop deve eseguire contenitori Linux.  

## <a name="deploy-modules-to-device"></a>Distribuire i moduli nel dispositivo

Si è verificato che le immagini dei contenitori di cui è stata eseguita la compilazione sono archiviate nel Registro Container, quindi è possibile eseguirne la distribuzione in un dispositivo. Assicurarsi che il dispositivo IoT Edge sia in esecuzione. 

1. Nello strumento di esplorazione di Visual Studio Code espandere la sezione relativa ai dispositivi dell'hub IoT di Azure. 

2. Fare clic con il pulsante destro del mouse sul dispositivo IoT Edge in cui si vuole eseguire la distribuzione, quindi scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo). 

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/tutorial-develop-for-linux/create-deployment.png)

3. In Esplora file passare alla cartella **config** e quindi selezionare il file **deployment.amd64.json**. 

   Non usare il file deployment.template.json, nel quale non sono presenti le credenziali del Registro Container o i valori delle immagini dei moduli. Se la destinazione è un dispositivo Linux ARM32, il manifesto della distribuzione sarà denominato deployment.arm32v7.json. 

4. Espandere i dettagli per il dispositivo IoT Edge e quindi espandere l'elenco **Moduli** per il dispositivo. 

5. Usare il pulsante di aggiornamento per aggiornare la visualizzazione del dispositivo fino a quando non si vedono i moduli tempSensor e SampleModule in esecuzione nel dispositivo. 

   Per l'avvio di entrambi i moduli, potrebbero essere necessari alcuni minuti. Il runtime IoT Edge deve ricevere il nuovo manifesto della distribuzione, eseguire il pull delle immagini dei moduli dal runtime del contenitore e quindi avviare ogni nuovo modulo. 

   ![Visualizzare i moduli in esecuzione nel dispositivo IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visualizzare i messaggi dal dispositivo

Il codice di SampleModule riceve i messaggi tramite la coda di input e li passa attraverso la coda di output. Il manifesto della distribuzione ha dichiarato le route che hanno passato i messaggi a SampleModule da tempSensor e quindi hanno inoltrato i messaggi da SampleModule all'hub IoT. L'estensione Azure IoT Tools per Visual Studio Code consente di visualizzare i messaggi che arrivano all'hub IoT dai singoli dispositivi. 

1. Nella finestra di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul dispositivo IoT Edge da monitorare e quindi scegliere **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito). 

2. Esaminare la finestra di output in Visual Studio Code per vedere i messaggi in arrivo nell'hub IoT. 

   ![Visualizzare i messaggi in arrivo da dispositivo a cloud](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visualizzare le modifiche nel dispositivo

Se si vuole vedere cosa accade nel dispositivo, usare i comandi in questa sezione per controllare il runtime IoT Edge e i moduli in esecuzione nel dispositivo. 

I comandi in questa sezione sono destinati al dispositivo IoT Edge, non al computer di sviluppo. Se si usa una macchina virtuale per il dispositivo IoT Edge, connettersi ora. In Azure passare alla pagina di panoramica della macchina virtuale e selezionare **Connetti** per accedere alla connessione shell sicura. 

* Visualizzare tutti i moduli distribuiti nel dispositivo e controllarne lo stato:

   ```bash
   iotedge list
   ```

   Dovrebbero essere presenti quattro moduli: i due moduli del runtime IoT Edge, tempSensor e SampleModule. Tutti e quattro i moduli devono essere indicati come in esecuzione.

* Esaminare i log per un modulo specifico:

   ```bash
   iotedge logs <module name>
   ```

   I moduli IoT Edge fanno distinzione tra maiuscole e minuscole. 

   I log di tempSensor e SamplModule devono visualizzare i messaggi in corso di elaborazione. Il modulo edgeAgent è responsabile dell'avvio degli altri moduli, quindi i relativi log conterranno le informazioni sull'implementazione del manifesto della distribuzione. Se un modulo non è elencato o non è in esecuzione, i log di edgeAgent conterranno probabilmente gli errori. Il modulo edgeHub è responsabile delle comunicazioni tra i moduli e l'hub IoT. Se i moduli sono in esecuzione, ma i messaggi non arrivano all'hub IoT, i log di edgeHub conterranno probabilmente gli errori. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato Visual Studio Code nel computer di sviluppo ed è stato distribuito il primo modulo IoT Edge. Ora che sono stati appresi i concetti di base, provare ad aggiungere funzionalità a un modulo in modo che possa analizzare i dati che passano attraverso di esso. Scegliere il linguaggio preferito: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)