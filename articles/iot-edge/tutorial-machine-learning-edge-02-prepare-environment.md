---
title: "Esercitazione: Configurare l'ambiente - Machine Learning in Azure IoT Edge"
description: "Esercitazione: Preparare l'ambiente per lo sviluppo e la distribuzione di moduli di Machine Learning al perimetro."
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 22628af5b3e62edfc96699ad051b6a00cf9c1429
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078946"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Esercitazione: Configurare un ambiente di Machine Learning in IoT Edge

> [!NOTE]
> Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Se l'articolo è stato aperto direttamente, è consigliabile iniziare con il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie per avere risultati ottimali.

Questo articolo illustra come preparare l'ambiente per lo sviluppo e la distribuzione. Configurare prima di tutto un computer di sviluppo con tutti gli strumenti necessari. Quindi creare le risorse cloud necessarie in Azure.

## <a name="set-up-the-development-vm"></a>Configurare la VM di sviluppo

Questo passaggio viene in genere eseguito da sviluppatori cloud. Parte del software può risultare utile anche ai data scientist.

È stato creato uno script di PowerShell che crea una macchina virtuale di Azure con molti prerequisiti già configurati. La VM da creare deve essere in grado di gestire la [virtualizzazione annidata](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), per cui sono state scelte le dimensioni della macchina [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md).

La VM di sviluppo verrà configurata con:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop per Windows](https://www.docker.com/products/docker-desktop)
* [Git per Windows](https://gitforwindows.org/)
* [Git Credential Manager per Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [ASP.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-1.1.0)
* [Estensioni di VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

La VM di sviluppo non è strettamente necessaria: tutti gli strumenti di sviluppo possono essere eseguiti nel computer locale. Tuttavia, è consigliabile usare la VM per assicurare un ambiente uniforme.

Per creare e configurare la macchina virtuale sono necessari circa 30 minuti.

1. Clonare o scaricare il repository di esempi di [Machine Learning e IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) nel computer locale.

1. Aprire PowerShell come amministratore e passare alla directory **\IoTEdgeAndMlSample\DevVM** che si trova nella directory radice in cui è stato scaricato il codice. La directory radice dell'origine verrà identificata come `srcdir`.

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   La directory DevVM contiene i file necessari per creare una macchina virtuale di Azure appropriata per completare questa esercitazione.

1. Eseguire il comando seguente per consentire l'esecuzione degli script. Quando richiesto, scegliere **Sì per tutti**.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Eseguire Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Quando richiesto, specificare le informazioni seguenti:

    * **ID sottoscrizione di Azure**: l'ID sottoscrizione, reperibile in [Sottoscrizioni di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale.
    * **Nome del gruppo di risorse**: il nome di un gruppo di risorse nuovo o esistente in Azure.
    * **Località**: scegliere un'area di Azure in cui verrà creata la macchina virtuale, ad esempio "Stati Uniti occidentali 2" o "Europa settentrionale". Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Nome utente**: specificare un nome facile da ricordare per l'account amministratore della macchina virtuale.
    * **Password**: impostare una password per l'account amministratore della VM.

   Lo script viene eseguito per diversi minuti mentre vengono completati i passaggi seguenti:

    1. Lo script installa il modulo [Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Richiede di accedere ad Azure.
    1. Verificare le informazioni per la creazione della VM. Premere **y** o **INVIO** per continuare.
    1. Crea il gruppo di risorse se non esiste.
    1. Distribuisce la macchina virtuale.
    1. Abilita Hyper-V nella VM.
    1. Installa il software necessario per lo sviluppo e clona il repository di esempi.
    1. Riavvia la VM.
    1. Crea un file RDP sul desktop per la connessione alla VM.

   Se viene richiesto di specificare il nome della VM per riavviarla, è possibile copiarlo dall'output dello script. L'output include anche il percorso del file RDP per la connessione alla VM.

### <a name="set-auto-shutdown-schedule"></a>Impostare la pianificazione di arresto automatico

Per ridurre i costi, la VM di sviluppo è stata creata con un arresto automatico pianificato per le 19:00 PST. Può essere necessario aggiornare questa impostazione in base alla località e alla propria pianificazione. Per aggiornare la pianificazione di arresto:

1. Nella portale di Azure passare alla VM creata dallo script.

1. Dal menu del riquadro sinistro, in **Operazioni**, scegliere **Arresto automatico**.

1. Modificare i valori di **Arresto pianificato** e **Fuso orario** come si desidera, quindi selezionare **Salva**.

## <a name="connect-to-the-development-vm"></a>Connettersi alla VM di sviluppo

Dopo aver creato una VM, occorre completare l'installazione del software necessario per l'esercitazione.

1. Fare doppio clic sul file RDP creato dallo script sul desktop.

1. Verrà visualizzata una finestra di dialogo che informa che l'autore della connessione remota è sconosciuto. È accettabile, quindi selezionare **Connetti**.

1. Specificare la password di amministratore fornita per creare la macchina virtuale e fare clic su **OK**.

1. Verrà chiesto di accettare il certificato per la VM. Selezionare **Sì**.

## <a name="install-visual-studio-code-extensions"></a>Installare le estensioni di Visual Studio Code

Dopo aver stabilito la connessione con il computer di sviluppo, aggiungere alcune estensioni utili a Visual Studio Code per semplificare l'esperienza di sviluppo.

1. Connettersi alla macchina virtuale di sviluppo, aprire una finestra di PowerShell e passare alla directory **C:\source\IoTEdgeAndMlSample\DevVM**. Questa directory è stata creata dallo script che ha creato la macchina virtuale.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Eseguire il comando seguente per consentire l'esecuzione degli script. Quando richiesto, scegliere **Sì per tutti**.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Eseguire lo script delle estensioni di Visual Studio Code

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Lo script verrà eseguito per alcuni minuti e installerà le estensioni di VS Code:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurare l'hub IoT e l'archiviazione

Questi passaggi vengono in genere eseguiti da sviluppatori cloud.

L'hub IoT di Azure è il fulcro di qualsiasi applicazione IoT, perché gestisce le comunicazioni sicure tra dispositivi IoT e il cloud. È il principale punto di coordinamento per il funzionamento della soluzione Machine Learning di IoT Edge.

* L'hub IoT usa le route per indirizzare i dati in ingresso provenienti dai dispositivi IoT ad altri servizi downstream. Per inviare i dati del dispositivo ad Archiviazione di Azure, si sfrutteranno le route dell'hub IoT. In Archiviazione di Azure i dati dei dispositivi vengono utilizzati da Azure Machine Learning per eseguire il training del classificatore di vita utile rimanente.

* Più avanti nell'esercitazione, l'hub IoT verrà usato per configurare e gestire il dispositivo Azure IoT Edge.

In questa sezione viene usato uno script per creare un hub IoT di Azure e un account di archiviazione di Azure. Quindi nel portale di Azure configurare una route che inoltri i dati ricevuti dall'hub a un contenitore di Archiviazione di Azure. Per completare questi passaggi, sono necessari circa 10 minuti.

1. Connettersi alla macchina virtuale di sviluppo, aprire una finestra di PowerShell e passare alla directory **IoTHub**.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Eseguire lo script di creazione. Usare gli stessi valori di ID sottoscrizione, località e gruppo di risorse specificati durante la creazione della VM di sviluppo.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Verrà visualizzata la richiesta di accedere ad Azure.
    * Lo script verifica le informazioni per la creazione dell'hub e dell'account di archiviazione. Premere **y** o **INVIO** per continuare.

Per eseguire lo script, sono necessari circa due minuti. Al termine, lo script genera il nome dell'hub IoT e dell'account di archiviazione.

## <a name="review-route-to-storage-in-iot-hub"></a>Esaminare la route all'account di archiviazione nell'hub IoT

Durante la creazione dell'hub IoT, lo script eseguito nella sezione precedente ha creato anche un endpoint personalizzato e una route. Le route dell'hub IoT sono costituite da un'espressione di query e da un endpoint. Se un messaggio corrisponde all'espressione, i dati vengono inviati lungo la route all'endpoint associato. Gli endpoint possono essere hub eventi, code del bus di servizio e argomenti. In questo caso, l'endpoint è un contenitore BLOB in un account di archiviazione. Usare il portale di Azure per esaminare la route creata dallo script.

1. Aprire il [portale di Azure](https://portal.azure.com) e passare al gruppo di risorse usato per questa esercitazione.

1. Nell'elenco di risorse selezionare l'hub IoT creato dallo script. Avrà un nome che termina con caratteri casuali, ad esempio `IotEdgeAndMlHub-jrujej6de6i7w`.

1. Dal menu del riquadro sinistro, in **Messaggistica**, scegliere **Routing messaggi**.

1. Nella pagina **Routing messaggi** selezionare la scheda **Endpoint personalizzati**.

1. Espandere la sezione **Archiviazione**:

   ![Verificare che turbofanDeviceStorage sia presente nell'elenco di endpoint personalizzati](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   In effetti **turbofanDeviceStorage** è incluso nell'elenco di endpoint personalizzati. Notare le caratteristiche seguenti sull'endpoint:

   * Punta al contenitore di archiviazione BLOB creato, denominato `devicedata`, come indicato da **Nome contenitore**.
   * Il **formato del nome file** include partition come ultimo elemento. Questo formato è più pratico per le operazioni sui file che verranno eseguite in seguito con Azure Notebooks nell'esercitazione.
   * Lo **Stato** deve essere integro.

1. Selezionare la scheda **Route**.

1. Selezionare la route denominata **turbofanDeviceDataToStorage**.

1. Nella pagina **Dettagli route** notare che l'endpoint della route è **turbofanDeviceStorage**.

   ![Esaminare i dettagli relativi alla route turbofanDeviceDataToStorage](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Esaminare l'opzione **Query di routing**, che è impostata su **true**. Questa impostazione significa che tutti i messaggi di telemetria dei dispositivi corrisponderanno a questa route e pertanto verranno inviati all'endpoint **turbofanDeviceStorage**.

1. Poiché non sono state apportate modifiche, chiudere questa pagina.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un hub IoT ed è stata configurata una route a un account di archiviazione di Azure. Successivamente verranno inviati i dati di un set di dispositivi simulati all'account di archiviazione tramite l'hub IoT. Più avanti nell'esercitazione, dopo aver configurato il dispositivo e i moduli IoT Edge, verranno riviste le route e verranno fornite altre informazioni sulla query di routing.

Per altre informazioni sui passaggi descritti in questa sezione dell'esercitazione su Machine Learning in IoT Edge, vedere:

* [Nozioni fondamentali su Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Configurare il routing dei messaggi con l'hub IoT](../iot-hub/tutorial-routing.md)
* [Creare un hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md)

Continuare con l'articolo successivo per creare un dispositivo simulato da monitorare.

> [!div class="nextstepaction"]
> [Generare i dati del dispositivo](tutorial-machine-learning-edge-03-generate-data.md)
