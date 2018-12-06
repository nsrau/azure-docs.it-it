---
title: Eseguire il debug dei moduli funzioni di Azure per Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per eseguire il debug delle funzioni di Azure C# con Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 40b8dfef3100ff8440165de74fb41f6b36afe37a
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52315104"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare ed eseguire il debug delle funzioni di Azure per Azure IoT Edge

Questo articolo mostra come usare [Visual Studio Code (VS Code)](https://code.visualstudio.com/) per eseguire il debug delle funzioni di Azure in Azure IoT Edge.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. Il dispositivo IoT Edge può essere un altro dispositivo fisico. In alternativa, si può simulare il dispositivo IoT Edge nel computer di sviluppo.

> [!NOTE]
> Questo articolo sul debug illustra come collegare un processo a un contenitore di modulo ed eseguirne il debug con VS Code. È possibile eseguire il debug di funzioni C# solo in contenitori Linux amd64. Se non si ha ancora familiarità con le funzionalità di debug di Visual Studio Code, vedere le [informazioni sul debug](https://code.visualstudio.com/Docs/editor/debugging). 

In questo articolo viene usato Visual Studio Code come strumento di sviluppo principale. Installare Visual Studio Code. Aggiungere quindi le estensioni necessarie: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Estensione Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Per creare un modulo, è necessario .NET per creare la cartella del progetto, Docker per creare l'immagine del modulo e un registro contenitori in cui memorizzare l'immagine del modulo:

* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
* [Docker Community Edition](https://docs.docker.com/install/) nel computer di sviluppo 
* [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud. 

Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un dispositivo IoT Edge. Per usare il computer come un dispositivo IoT Edge, seguire i passaggi nella guida introduttiva per [Windows](quickstart.md) o [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Creare un nuovo modello di soluzione

Eseguire questa procedura per creare una soluzione IoT Edge che dispone di un modulo di funzione C#. Ogni soluzione può avere diversi moduli.

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato**.
3. Selezionare **Visualizza** > **Riquadro comandi**.
4. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge). 

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Passare alla cartella in cui si vuole creare la nuova soluzione. Scegliere **Seleziona cartella**. 
6. Immettere un nome per la soluzione. 
7. Scegliere **Funzioni di Azure - C#** come modello per il primo modulo nella soluzione.
8. Immettere un nome per il modulo. Scegliere un nome univoco all'interno del registro contenitori. 
9. Specificare il repository di immagini per il modulo. VS Code popola automaticamente il nome del modulo con il valore **localhost:5000**. Sostituire tale valore con le proprie informazioni di registro. Se per il test si usa un registro Docker locale, **localhost** è corretto. Se si usa Registro contenitori di Azure, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un nome simile a **\<nome registro\>.azurecr.io**. Sostituire solo la parte localhost della stringa, non eliminare il nome del modulo.

   ![Specificare il repository di immagini Docker](./media/how-to-develop-csharp-function/repository.png)

VS Code usa le informazioni fornite per creare una soluzione IoT Edge con un progetto per Funzioni di Azure, quindi la carica in una nuova finestra.

Nella soluzione sono presenti quattro elementi: 

* Una cartella **.vscode** contenente le configurazioni di debug.
* Una cartella **modules** contenente le sottocartelle di ogni modulo. A questo punto è presente un solo elemento. Tuttavia è possibile aggiungerne altri tramite il riquadro comandi selezionando **Azure IoT Edge: Aggiungi modulo Edge IoT**.
* Un file con estensione **env** in cui sono elencate le variabili di ambiente. Se Registro contenitori di Azure è il registro, si avranno un nome utente e una password per Registro contenitori di Azure. 

   >[!NOTE]
   >Il file dell'ambiente viene creato solo se si specifica un repository di immagini per il modulo. Se sono state accettate le impostazioni predefinite di localhost per testare ed eseguire il debug in locale, non è necessario dichiarare le variabili di ambiente. 

* Un file **deployment.template.json** in cui sono elencati il nuovo modulo e un modulo **tempSensor** di esempio che simula i dati utilizzabili per il test. Per altre informazioni su come funzionano i manifesti di distribuzione, vedere [Informazioni su come usare i manifesti della distribuzione per distribuire moduli e definire route](module-composition.md).
* Un file **deployment.debug.template.json** contenente la versione di debug delle immagini del modulo con le opzioni per il contenitore appropriato.

## <a name="develop-your-module"></a>Sviluppare il modulo

Il codice funzione di Azure predefinito disponibile con la soluzione si trova in **modules** > [nome modulo] > **modulename.cs**. Il modulo e il file deployment.template.json sono impostati in modo che sia possibile compilare la soluzione, inviarla al registro del contenitore e distribuirla in un dispositivo per avviare il test senza toccare alcun codice. Il modulo viene compilato solo per accettare l'input da un'origine (in questo caso, il modulo tempSensor che simula i dati) e collegarlo all'hub IoT. 

Quando si è pronti per personalizzare il modello di funzione di Azure con il proprio codice, usare gli [SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md) per compilare moduli che rispondano alle esigenze chiave delle soluzioni IoT quali sicurezza, gestione dei dispositivi e affidabilità. 

## <a name="build-your-module-for-debugging"></a>Compilare il modulo per il debug
1. Per iniziare il debug, usare **Dockerfile.amd64.debug** per ricompilare l'immagine Docker e distribuire nuovamente la soluzione Edge. In Esplora codice di Visual Studio passare al file `deployment.debug.template.json`.
2. Ricompilare la soluzione. Nel riquadro comandi di VS Code immettere ed eseguire il comando **Azure IoT Edge: Build IoT Edge solution** (Azure IoT Edge: Compila soluzione IoT Edge).
3. Selezionare il file `deployment.debug.template.json` per la soluzione dal riquadro comandi. 
4. Fare clic con il pulsante destro del mouse sull'ID di un dispositivo IoT Edge nell'apposito strumento di esplorazione per l'hub IoT di Azure, quindi scegliere **Create deployment for Edge device** (Crea distribuzione per dispositivo Edge). Selezionare il file `deployment.debug.amd64.json` nella cartella `config`. Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione in un terminale integrato di Visual Studio Code.

Controllare lo stato del contenitore nello strumento di esplorazione Docker di Visual Studio Code o tramite il comando `docker ps` nel terminale.

## <a name="start-debugging-c-functions-in-vs-code"></a>Avviare il debug delle funzioni C# in Visual Studio Code
1. Visual Studio Code consente di mantenere le informazioni di configurazione del debug in un file `launch.json` che si trova nella cartella `.vscode` nell'area di lavoro. Il file `launch.json` è stato generato durante la creazione di una nuova soluzione IoT Edge e viene aggiornato ogni volta che si aggiunge un nuovo modulo con il supporto per il debug. Passare alla visualizzazione di debug. Selezionare il file di configurazione del debug corrispondente. Il nome dell'opzione di debug deve essere simile a **Debug remoto NomeModulo (.NET Core)**.

   ![Selezionare la configurazione del debug](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accedere a `modulename.cs`. Aggiungere un punto di interruzione nella funzione.
3. Selezionare **Avvia debug** o premere **F5**. Selezionare il processo a cui collegarsi.
4. Nella visualizzazione di debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra. 


> [!NOTE]
> L'esempio illustra come eseguire il debug delle funzioni IoT Edge in .NET Core nei contenitori. Si basa sulla versione di debug di `Dockerfile.amd64.debug`, che include VSDBG (debugger della riga di comando di .NET Core) nell'immagine del contenitore durante la creazione. Si consiglia di usare direttamente o personalizzare il file `Dockerfile` senza VSDBG per le funzioni IoT Edge per l'ambiente di produzione al termine del debug delle funzioni C#.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md).

Per sviluppare moduli per i dispositivi IoT Edge, [Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
