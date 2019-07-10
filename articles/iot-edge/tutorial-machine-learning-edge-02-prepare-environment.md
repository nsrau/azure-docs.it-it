---
title: Configurare l'ambiente - Machine Learning in Azure IoT Edge | Microsoft Docs
description: Preparare l'ambiente per lo sviluppo e la distribuzione di moduli di Machine Learning al perimetro.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fd3b5766ec2bd8d1babf847598f1fbe5b6511ce7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432838"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Esercitazione: Configurare un ambiente di Machine Learning in IoT Edge

> [!NOTE]
> Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Se l'articolo è stato aperto direttamente, è consigliabile iniziare con il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie per risultati ottimali.

Questo articolo dell'esercitazione completa su Azure Machine Learning in IoT Edge consente di preparare l'ambiente per lo sviluppo e la distribuzione. Configurare prima di tutto un computer di sviluppo con tutti gli strumenti necessari. Quindi creare le risorse cloud necessarie in Azure.

## <a name="set-up-a-development-machine"></a>Configurare un computer di sviluppo

Questo passaggio viene in genere eseguito da sviluppatori cloud. Parte del software può risultare utile anche ai data scientist.

Nel corso dell'articolo vengono eseguite varie attività di sviluppo, tra cui scrittura di codice, compilazione, configurazione e distribuzione di moduli IoT Edge e dispositivi IoT. Per semplicità, è stato creato uno script di PowerShell che crea una macchina virtuale di Azure con molti prerequisiti già configurati. La VM da creare deve essere in grado di gestire la [virtualizzazione annidata](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), per cui sono state scelte le dimensioni della macchina DS8V3.

La VM di sviluppo verrà configurata con:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop per Windows](https://www.docker.com/products/docker-desktop)
* [Git per Windows](https://gitforwindows.org/)
* [Git Credential Manager per Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Estensioni di VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

La VM di sviluppo non è strettamente necessaria: tutti gli strumenti di sviluppo possono essere eseguiti nel computer locale. Tuttavia, è consigliabile usare la VM per assicurare un ambiente uniforme.

Per creare e configurare la macchina virtuale sono necessari circa 30 minuti.

### <a name="get-the-script"></a>Ottenere lo script

Clonare o scaricare lo script di PowerShell dal repository di esempi di [Machine Learning e IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample).

### <a name="create-an-azure-virtual-machine"></a>Creare una macchina virtuale di Azure

La directory DevVM contiene i file necessari per creare una macchina virtuale di Azure appropriata per completare questa esercitazione.

1. Aprire PowerShell come amministratore e passare alla directory in cui è stato scaricato il codice. La directory radice dell'origine verrà identificata come `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Eseguire il comando seguente per consentire l'esecuzione degli script. Quando richiesto, scegliere **Sì per tutti**.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Eseguire Create-AzureDevVM.ps1 da questa directory.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Quando richiesto, specificare le informazioni seguenti:
      * **ID sottoscrizione di Azure**: l'ID sottoscrizione, reperibile nel portale di Azure
      * **Nome del gruppo di risorse**: il nome di un gruppo di risorse nuovo o esistente in Azure
      * **Località**: scegliere un'area di Azure in cui verrà creata la macchina virtuale, ad esempio westus2 o northeurope. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: specificare un nome facile da ricordare per l'account amministratore da creare e usare nella macchina virtuale.
      * **AdminPassword**: impostare una password per l'account amministratore nella macchina virtuale.

    * Se Azure PowerShell non è installato, lo script installerà il [modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Verrà visualizzata la richiesta di accedere ad Azure.

    * Lo script verifica le informazioni per la creazione della VM. Premere `y` o `Enter` per continuare.

Lo script viene eseguito per diversi minuti mentre vengono completati i passaggi seguenti:

* Creare il gruppo di risorse se non esiste
* Distribuire la macchina virtuale
* Abilitare Hyper-V nella VM
* Installare il software necessario per lo sviluppo e clonare il repository di esempi
* Riavviare la VM
* Creare un file RDP sul desktop per la connessione alla VM

### <a name="set-auto-shutdown-schedule"></a>Impostare la pianificazione di arresto automatico

Per ridurre i costi, la VM è stata creata con una pianificazione di arresto automatico impostata su 19:00 PST. Può essere necessario aggiornare questo orario in base alla località e alla propria pianificazione. Per aggiornare la pianificazione di arresto:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare alla macchina virtuale nel gruppo di risorse specificato nella sezione precedente.

3. Selezionare **Arresto automatico** nel riquadro di spostamento laterale.

4. Immettere un nuovo orario per l'arresto in **Arresto pianificato** oppure cambiare il **Fuso orario** e quindi fare clic su **Salva**.

### <a name="connect-and-configure-development-machine"></a>Connettere e configurare il computer di sviluppo

Dopo aver creato una VM, occorre completare l'installazione del software necessario per l'esercitazione.

#### <a name="start-a-remote-desktop-session"></a>Avviare una sessione Desktop remoto

1. Lo script di creazione della VM ha creato un file RDP nel desktop.

2. Fare doppio clic sul file denominato **\<Nome VM di Azure\>.rdp**.

3. Verrà visualizzata una finestra di dialogo che informa che l'autore della connessione remota è sconosciuto. Fare clic sulla casella di controllo **Non visualizzare più questo messaggio per le connessioni a questo computer** e selezionare **Connetti**.

4. Quando richiesto, specificare il valore di AdminPassword usato durante l'esecuzione dello script per configurare la VM e fare clic su **OK**.

5. Verrà chiesto di accettare il certificato per la VM. Selezionare **Non visualizzare più questo messaggio per le connessioni a questo computer** e scegliere **Sì**.

#### <a name="install-visual-studio-code-extensions"></a>Installare le estensioni di Visual Studio Code

Dopo aver stabilito la connessione con il computer di sviluppo, aggiungere alcune estensioni utili a Visual Studio Code per semplificare l'esperienza di sviluppo.

1. In una finestra di PowerShell passare a **C:\\source\\IoTEdgeAndMlSample\\DevVM**.

2. Consentire l'esecuzione di script nella macchina virtuale digitando.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Eseguire lo script.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. Lo script verrà eseguito per alcuni minuti e installerà le estensioni di VS Code:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurare l'hub IoT e l'archiviazione

Questi passaggi vengono in genere eseguiti da sviluppatori cloud.

L'hub IoT di Azure rappresenta la base di qualsiasi applicazione IoT. Gestisce la comunicazione sicura tra dispositivi IoT e il cloud. È il principale punto di coordinamento per il funzionamento della soluzione Machine Learning di IoT Edge.

* L'hub IoT usa le route per indirizzare i dati in ingresso provenienti dai dispositivi IoT ad altri servizi downstream. Le route dell'hub IoT verranno usate per inviare dati dei dispositivi ad Archiviazione di Azure, dove potranno essere usati da Azure Machine Learning per eseguire il training del classificatore di vita utile rimanente.

* Più avanti nell'esercitazione, l'hub IoT verrà usato per configurare e gestire il dispositivo Azure IoT Edge.

In questa sezione viene usato uno script per creare un hub IoT di Azure e un account di archiviazione di Azure. Quindi viene configurata una route che inoltra i dati ricevuti dall'hub a un contenitore BLOB del servizio di archiviazione di Azure usando il portale di Azure. Per completare questi passaggi, sono necessari circa 10 minuti.

### <a name="create-cloud-resources"></a>Creare le risorse cloud

1. Nel computer di sviluppo aprire una finestra di PowerShell.

1. Passare alla directory IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Eseguire lo script di creazione. Usare gli stessi valori di ID sottoscrizione, località e gruppo di risorse specificati durante la creazione della VM di sviluppo.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Verrà visualizzata la richiesta di accedere ad Azure.
    * Lo script verifica le informazioni per la creazione dell'hub e dell'account di archiviazione. Premere `y` o `Enter` per continuare.

Per eseguire lo script, sono necessari circa due minuti. Al termine, lo script genera il nome dell'hub e dell'account di archiviazione.

### <a name="review-route-to-storage-in-iot-hub"></a>Esaminare la route all'account di archiviazione nell'hub IoT

Durante la creazione dell'hub IoT, lo script eseguito nella sezione precedente ha creato anche un endpoint personalizzato e una route. Le route dell'hub IoT sono costituite da un'espressione di query e da un endpoint. Se un messaggio corrisponde all'espressione, i dati vengono inviati lungo la route all'endpoint associato. Gli endpoint possono essere hub eventi, code del bus di servizio e argomenti. In questo caso, l'endpoint è un contenitore BLOB in un account di archiviazione. Usare il portale di Azure per esaminare la route creata dallo script.

1. Aprire il [portale di Azure](https://portal.azure.com).

1. Scegliere tutti i servizi nel riquadro di spostamento sinistro, digitare IoT nella casella di ricerca e selezionare **Hub IoT**.

1. Scegliere l'hub IoT creato nel passaggio precedente.

1. Nel riquadro di spostamento sinistro dell'hub IoT scegliere **Routing messaggi**.

1. La pagina Routing messaggi include due schede, **Route** e **Endpoint personalizzati**. Selezionare la scheda **Endpoint personalizzati**.

1. In **Archiviazione BLOB** selezionare **turbofanDeviceStorage**.

1. Questo endpoint punta a un contenitore BLOB denominato **devicedata** nell'account di archiviazione creato nel passaggio precedente, denominato **iotedgeandml\<suffisso univoco\>** .

1. Inoltre, il **formato del nome file BLOB** è cambiato rispetto al valore predefinito perché ora l'ultimo elemento del nome include la partizione. Questo formato è più pratico per le operazioni sui file che verranno eseguite in seguito con Azure Notebooks nell'esercitazione.

1. Chiudere il pannello di dettagli dell'endpoint e tornare nella pagina **Routing messaggi**.

1. Selezionare la scheda **Route**.

1. Selezionare la route denominata **turbofanDeviceDataToStorage**.

1. L'endpoint della route è l'endpoint personalizzato **turbofanDeviceStorage**.

1. Esaminare la query di routing, che è impostata su **true**. Questo significa che tutti i messaggi di telemetria dei dispositivi corrisponderanno a questa route e pertanto verranno inviati all'endpoint **turbofanDeviceStorage**.

1. Chiudere i dettagli della route.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un hub IoT ed è stata configurata una route a un account di archiviazione di Azure. Nell'articolo successivo verranno inviati dati da un set di dispositivi simulati all'account di archiviazione tramite l'hub IoT. Più avanti nell'esercitazione, dopo aver configurato il dispositivo e i moduli IoT Edge, verranno riviste le route e verranno fornite altre informazioni sulla query di routing.

Per altre informazioni sui passaggi descritti in questa sezione dell'esercitazione su Machine Learning in IoT Edge, vedere:

* [Nozioni fondamentali su Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Configurare il routing dei messaggi con l'hub IoT](../iot-hub/tutorial-routing.md)
* [Creare un hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md)

Continuare con l'articolo successivo per creare un dispositivo simulato da monitorare.

> [!div class="nextstepaction"]
> [Generare i dati del dispositivo](tutorial-machine-learning-edge-03-generate-data.md)
