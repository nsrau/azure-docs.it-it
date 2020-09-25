---
title: Come installare ed eseguire il contenitore di analisi spaziale-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Il contenitore analisi spaziale consente di rilevare persone e distanze.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 52df2ad0dc4c60c24e341a9765e31bcf9776bf5e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91277292"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Installare ed eseguire il contenitore di analisi spaziale (anteprima)

Il contenitore analisi spaziale consente di analizzare i video in streaming in tempo reale per comprendere le relazioni spaziali tra le persone, lo spostamento e le interazioni con gli oggetti in ambienti fisici. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * Per eseguire il contenitore di analisi spaziale sono necessari la chiave e l'endpoint della risorsa creata. La chiave e l'endpoint verranno usati in un secondo momento.


### <a name="spatial-analysis-container-requirements"></a>Requisiti dei contenitori di analisi spaziale

Per eseguire il contenitore di analisi spaziale, è necessario un dispositivo di calcolo con una [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). Si consiglia di usare [Azure stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) con accelerazione GPU, tuttavia il contenitore viene eseguito in qualsiasi altro computer desktop che soddisfi i requisiti minimi. Si fa riferimento a questo dispositivo come computer host.

#### <a name="azure-stack-edge-device"></a>[Dispositivo Azure Stack Edge](#tab/azure-stack-edge)

Azure Stack Edge è una soluzione hardware come servizio e un dispositivo di calcolo Edge abilitato per intelligenza artificiale con funzionalità di trasferimento dei dati di rete. Per istruzioni dettagliate sulla preparazione e la configurazione, vedere la [documentazione di Azure stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep).

#### <a name="desktop-machine"></a>[Computer desktop](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Requisiti hardware minimi

* RAM di sistema da 4 GB
* 4 GB di RAM GPU
* CPU a 8 core
* 1 GPU NVIDIA Tesla T4
* 20 GB di spazio su disco rigido

#### <a name="recommended-hardware"></a>Hardware consigliato

* RAM di sistema 32 GB
* 16 GB di RAM GPU
* CPU a 8 core
* 2 GPU NVIDIA Tesla T4
* 50 GB di spazio SSD

In questo articolo vengono scaricati e installati i pacchetti software seguenti. Il computer host deve essere in grado di eseguire quanto segue (vedere di seguito per le istruzioni):

* [Driver NVIDIA graphics](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) e [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Configurazioni per i [MP NVIDIA](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (servizio a più processi).
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) e [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) Runtime.

---

| Requisito | Descrizione |
|--|--|
| Fotocamera | Il contenitore di analisi spaziale non è associato a un marchio della fotocamera specifico. Il dispositivo della fotocamera deve: supportare il protocollo RTSP (Real-Time Streaming Protocol) e la codifica H. 264, essere accessibile al computer host e essere in grado di eseguire lo streaming a 15FPS e risoluzione 1080p. |
| Sistema operativo Linux | [Ubuntu Desktop 18,04 LTS](http://releases.ubuntu.com/18.04/) deve essere installato nel computer host.  |


## <a name="request-approval-to-run-the-container"></a>Richiedi l'approvazione per eseguire il contenitore

Compilare e inviare il [modulo di richiesta](https://aka.ms/csgate) per richiedere l'approvazione per l'esecuzione del contenitore.

Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo aver inviato il modulo, il team di servizi cognitivi di Azure lo esaminerà e invierà un messaggio di posta elettronica con una decisione.

> [!IMPORTANT]
> * Nel modulo è necessario usare un indirizzo di posta elettronica associato a un ID sottoscrizione di Azure.
> * La risorsa Visione artificiale usata per eseguire il contenitore deve essere stata creata con l'ID sottoscrizione di Azure approvato.

Una volta approvata, sarà possibile eseguire il contenitore dopo averlo scaricato da Microsoft Container Registry (di Microsoft), descritto più avanti in questo articolo.

Non sarà possibile eseguire il contenitore se la sottoscrizione di Azure non è stata approvata.

## <a name="set-up-the-host-computer"></a>Configurare il computer host

Si consiglia di usare un dispositivo Azure Stack Edge per il computer host. Se si sta configurando un dispositivo diverso, fare clic su **computer desktop** .

#### <a name="azure-stack-edge-device"></a>[Dispositivo Azure Stack Edge](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Configurare il calcolo nel portale di Azure Stack Edge 
 
L'analisi spaziale USA le funzionalità di calcolo di Azure Stack Edge per eseguire una soluzione di intelligenza artificiale. Per abilitare le funzionalità di calcolo, verificare che: 

* Il dispositivo Azure Stack Edge è stato [connesso e attivato](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate) . 
* Si dispone di un sistema client Windows che esegue PowerShell 5,0 o versione successiva per accedere al dispositivo.  
* Per distribuire un cluster Kubernetes, è necessario configurare il dispositivo Azure Stack Edge tramite l' **interfaccia utente locale** nel [portale di Azure](https://portal.azure.com/): 
  1. Abilitare la funzionalità di calcolo sul dispositivo Azure Stack Edge. Per abilitare il calcolo, passare alla pagina **calcolo** nell'interfaccia Web del dispositivo. 
  2. Selezionare un'interfaccia di rete che si vuole abilitare per il calcolo, quindi fare clic su **Abilita**. Verrà creato un commutire virtuale sul dispositivo su tale interfaccia di rete.
  3. Lasciare vuoti gli indirizzi IP del nodo di test Kubernetes e gli indirizzi IP dei servizi esterni di Kubernetes.
  4. Fare clic su **Applica**. Questa operazione può richiedere circa due minuti. 

![Configurare il calcolo](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Configurare un ruolo di calcolo Edge e creare una risorsa dell'hub Internet delle cose

Nella [portale di Azure](https://portal.azure.com/)passare alla risorsa di Azure stack Edge. Nella pagina **Overview** o nell'elenco di navigazione fare clic **sul pulsante calcolo Edge per iniziare.** Nel riquadro **Configura calcolo Edge**   fare clic su **Configura**. 

![Collegamento](media/spatial-analysis/configure-edge-compute-tile.png)

Nella pagina **Configura calcolo Edge**   scegliere un hub Internet esistente oppure scegliere di crearne uno nuovo. Per impostazione predefinita, viene usato un piano tariffario standard (S1) per creare una risorsa dell'hub Internet. Per usare una risorsa dell'hub per le cose di livello gratuito, crearne una e selezionarla. La risorsa dell'hub Internet usa la stessa sottoscrizione e il gruppo di risorse usato dalla risorsa Azure Stack Edge 

Fare clic su **Crea**. La creazione di risorse dell'hub Internet può richiedere un paio di minuti. Dopo la creazione della risorsa hub Internet, il riquadro **Configura calcolo Edge** verrà aggiornato per mostrare la nuova configurazione. Per verificare che il ruolo di calcolo perimetrale sia stato configurato, selezionare **Visualizza configurazione** nel riquadro **Configura calcolo**   .

Quando il ruolo di calcolo Edge è configurato nel dispositivo Edge, crea due dispositivi: un dispositivo IoT e un dispositivo IoT Edge. Entrambi i dispositivi possono essere visualizzati nella risorsa dell'hub IoT. Il runtime di Azure IoT Edge sarà già in esecuzione nel dispositivo IoT Edge.            

> [!NOTE]
> * Attualmente è supportata solo la piattaforma Linux per i dispositivi IoT Edge. Per informazioni sulla risoluzione dei problemi relativi al dispositivo Azure Stack Edge, vedere l'articolo [registrazione e risoluzione dei problemi](spatial-analysis-logging.md) .
> * Per altre informazioni su come configurare un dispositivo IoT Edge per la comunicazione tramite un server proxy, vedere [configurare un dispositivo IOT Edge per la comunicazione tramite un server proxy](https://docs.microsoft.com/azure/iot-edge/how-to-configure-proxy-support#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>Abilita MPS su Azure Stack Edge 

1. Eseguire una sessione di Windows PowerShell come amministratore. 

2. Verificare che il servizio Gestione remota Windows sia in esecuzione nel client. Nel terminale di PowerShell usare il comando seguente 
    
    ```powershell
    winrm quickconfig
    ```
    
    Se vengono visualizzati avvisi relativi a un'eccezione del firewall, controllare il tipo di connessione di rete e vedere la documentazione [gestione remota Windows](https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) .

3. Assegnare una variabile all'indirizzo IP del dispositivo. 
    
    ```powershell
    $ip = "" Replace with the IP address of your device. 
    ```
    
4. Per aggiungere l'indirizzo IP del dispositivo all'elenco di host attendibili del client, usare il comando seguente: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Avviare una sessione di Windows PowerShell nel dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Fornire la password quando richiesto. Usare la stessa password usata per accedere all'interfaccia utente Web locale. La password dell'interfaccia utente Web locale predefinita è `Password1` .

Digitare `Start-HcsGpuMPS` per avviare il servizio MPS sul dispositivo. 

Per informazioni sulla risoluzione dei problemi relativi al dispositivo Azure Stack Edge, vedere [Troubleshooting the Azure stack Edge Device](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[Computer desktop](#tab/desktop-machine)

Seguire queste istruzioni se il computer host non è un dispositivo Azure Stack Edge.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Installare NVIDIA CUDA Toolkit e i driver NVIDIA Graphics nel computer host

Usare lo script bash seguente per installare i driver NVIDIA graphics necessari e il Toolkit CUDA.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Riavviare il computer ed eseguire il comando seguente.

```bash
nvidia-smi
```

Viene visualizzato l'output seguente.

![Output driver NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Installare Docker CE e NVIDIA-docker2 nel computer host

Installare Docker CE nel computer host.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Installare il pacchetto software *NVIDIA-Docker-2* .

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Abilitare i Management Pack NVIDIA nel computer host

> [!TIP]
> * Non installare MPS se la capacità di calcolo della GPU è inferiore a 7. x (pre-volta). Per informazioni di riferimento, vedere [compatibilità CUDA](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) . 
> * Eseguire le istruzioni MPS da una finestra del terminale nel computer host. Non all'interno dell'istanza del contenitore docker.

Per prestazioni e utilizzo ottimali, configurare le GPU del computer host per il [servizio multiprocesso NVIDIA (MP)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Eseguire le istruzioni MPS da una finestra del terminale nel computer host.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Configurare Azure IoT Edge nel computer host

Per distribuire il contenitore di analisi spaziale nel computer host, creare un'istanza di un servizio [Hub Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) per l'uso del piano tariffario standard (S1) o gratuito (F0). Se il computer host è un Azure Stack Edge, usare la stessa sottoscrizione e il gruppo di risorse usato dalla risorsa Azure Stack Edge.

Usare l'interfaccia della riga di comando di Azure per creare un'istanza dell'hub Azure. Sostituire i parametri laddove appropriato. In alternativa, è possibile creare l'hub Azure per la [portale di Azure](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "test-resource-group" --location "WestUS"

az iot hub create --name "test-iot-hub-123" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "test-iot-hub-123" --device-id "my-edge-device" --edge-enabled
```

Se il computer host non è un dispositivo Azure Stack Edge, sarà necessario installare [Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) versione 1.0.8. Per scaricare la versione corretta, attenersi alla procedura seguente:

Server Ubuntu 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Copiare l'elenco generato.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installare la chiave pubblica GPG Microsoft.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Aggiornare gli elenchi di pacchetti nel dispositivo.

```bash
sudo apt-get update
```

Installare la versione di 1.0.8:

```bash
sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
```

Registrare quindi il computer host come dispositivo IoT Edge nell'istanza dell'hub Internet, usando una stringa di [connessione](https://docs.microsoft.com/azure/iot-edge/how-to-register-device#register-in-the-azure-portal).

È necessario connettere il dispositivo IoT Edge all'hub Azure. È necessario copiare la stringa di connessione dal dispositivo IoT Edge creato in precedenza. In alternativa, è possibile eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```bash
az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

Sul computer host aperto  `/etc/iotedge/config.yaml` per la modifica. Sostituire `ADD DEVICE CONNECTION STRING HERE` con la stringa di connessione. Salvare e chiudere il file. Eseguire questo comando per riavviare il servizio IoT Edge nel computer host.

```bash
sudo systemctl restart iotedge
```

Distribuire il contenitore di analisi spaziale come un modulo di Internet delle cose nel computer host, dall' [portale di Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) o dall'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli). Se si usa il portale, impostare l'URI dell'immagine sul percorso del Container Registry di Azure. 

Usare i passaggi seguenti per distribuire il contenitore usando l'interfaccia della riga di comando di Azure.

---

### <a name="iot-deployment-manifest"></a>Manifesto della distribuzione di Internet delle cose

Per semplificare la distribuzione di contenitori in più computer host, è possibile creare un file manifesto di distribuzione per specificare le opzioni di creazione del contenitore e le variabili di ambiente. È possibile trovare un esempio di [manifesto di distribuzione in GitHub](https://go.microsoft.com/fwlink/?linkid=2142179).

La tabella seguente illustra le diverse variabili di ambiente usate dal modulo IoT Edge. È anche possibile impostarli nel manifesto di distribuzione collegato in precedenza, usando l' `env` attributo in `spatialanalysis` :

| Nome dell'impostazione | valore | Descrizione|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Informazioni Dettagliato | Livello di registrazione, selezionare uno dei due valori|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Non modificare|
| ARCHON_PERF_MARKER| false| Impostare su true per la registrazione delle prestazioni. in caso contrario, deve essere false| 
| ARCHON_NODES_LOG_LEVEL | Informazioni Dettagliato | Livello di registrazione, selezionare uno dei due valori|
| OMP_WAIT_POLICY | PASSIVO | Non modificare|
| QT_X11_NO_MITSHM | 1 | Non modificare|
| API_KEY | Chiave API| Raccogliere questo valore da portale di Azure dalla risorsa Visione artificiale. È possibile trovarlo nella sezione **chiave ed endpoint** per la risorsa. |
| BILLING_ENDPOINT | URI dell'endpoint| Raccogliere questo valore da portale di Azure dalla risorsa Visione artificiale. È possibile trovarlo nella sezione **chiave ed endpoint** per la risorsa.|
| CONTRATTO DI LICENZA | accettare | Questo valore deve essere impostato in modo da *accettare* l'esecuzione del contenitore |
| VISUALIZZARE | : 1 | Questo valore deve corrispondere all'output di `echo $DISPLAY` nel computer host. I dispositivi Azure Stack Edge non dispongono di una visualizzazione. Questa impostazione non è applicabile|


> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

Dopo aver aggiornato ilDeploymentManifest.jsdi esempio [ nel](https://go.microsoft.com/fwlink/?linkid=2142179) file con le proprie impostazioni e la selezione delle operazioni, è possibile usare il comando dell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) seguente per distribuire il contenitore nel computer host, come modulo di IOT Edge.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

|Parametro  |Descrizione  |
|---------|---------|
| `--deployment-id` | Nuovo nome per la distribuzione. |
| `--hub-name` | Nome dell'hub Azure. |
| `--content` | Nome del file di distribuzione. |
| `--target-condition` | Nome del dispositivo IoT Edge per il computer host. |
| `-–subscription` | ID o nome sottoscrizione. |

Questo comando avvierà la distribuzione. Per visualizzare lo stato della distribuzione, passare alla pagina dell'portale di Azure istanza dell'hub Internet Azure. Lo stato può essere visualizzato come *417: la configurazione della distribuzione del dispositivo non viene impostata* fino a quando il dispositivo non completa il download delle immagini del contenitore e avvia l'esecuzione.

## <a name="validate-that-the-deployment-is-successful"></a>Verificare che la distribuzione sia stata completata correttamente

Per verificare se il contenitore è in esecuzione, sono disponibili diverse opzioni. Individuare lo *stato di runtime* nelle **impostazioni del modulo IOT Edge** per il modulo di analisi spaziale nell'istanza dell'Hub Azure Internet nel portale di Azure. Verificare che il **valore desiderato** e il **valore restituito** per lo *stato di runtime* siano *in esecuzione*.

![Esempio di verifica della distribuzione](./media/spatial-analysis/deployment-verification.png)

Una volta completata la distribuzione e il contenitore è in esecuzione, il **computer host** inizierà a inviare eventi all'hub Azure. Se è stata usata la `.debug` versione delle operazioni, viene visualizzata una finestra del visualizzatore per ogni fotocamera configurata nel manifesto della distribuzione. È ora possibile definire le linee e le zone da monitorare nel manifesto di distribuzione e seguire le istruzioni per eseguire di nuovo la distribuzione. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Configurare le operazioni eseguite dall'analisi spaziale

È necessario usare [le operazioni di analisi spaziale](spatial-analysis-operations.md) per configurare il contenitore per l'uso di telecamere connesse, configurare le operazioni e altro ancora. Per ogni dispositivo della fotocamera configurato, le operazioni per l'analisi spaziale genereranno un flusso di output di messaggi JSON, inviato all'istanza dell'hub Azure.

## <a name="redeploy-or-delete-the-deployment"></a>Ridistribuire o eliminare la distribuzione

Se è necessario aggiornare la distribuzione, è necessario assicurarsi che le distribuzioni precedenti siano state distribuite correttamente oppure è necessario eliminare IoT Edge le distribuzioni di dispositivi che non sono state completate. In caso contrario, le distribuzioni continueranno, lasciando il sistema in uno stato non valido. È possibile usare l'portale di Azure o l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment).

## <a name="use-the-output-generated-by-the-container"></a>Usare l'output generato dal contenitore

Se si desidera iniziare a utilizzare l'output generato dal contenitore, vedere gli articoli seguenti:

*   Usare l'SDK di hub eventi di Azure per il linguaggio di programmazione scelto per connettersi all'endpoint dell'hub Azure Internet e ricevere gli eventi. Per altre informazioni, vedere [leggere i messaggi da dispositivo a cloud dall'endpoint predefinito](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) . 
*   Configurare il routing dei messaggi nell'hub di Azure per l'invio degli eventi ad altri endpoint oppure salvare gli eventi nell'archiviazione BLOB di Azure e così via. Per ulteriori informazioni, vedere [routing dei messaggi dell'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) Internet. 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Esecuzione dell'analisi spaziale con un file video registrato

È possibile usare l'analisi spaziale con video registrati o Live. Per usare l'analisi spaziale per i video registrati, provare a registrare un file video e salvarlo come file MP4. Creare un account di archiviazione BLOB in Azure o utilizzarne uno esistente. Aggiornare quindi le seguenti impostazioni di archiviazione BLOB nel portale di Azure:
    1. Modificare il **trasferimento sicuro necessario** per **disabilitarlo**
    2. Modifica **Consenti accesso pubblico BLOB** su **abilitato**

Passare alla sezione del **contenitore** e creare un nuovo contenitore o utilizzarne uno esistente. Caricare quindi il file video nel contenitore. Espandere le impostazioni del file caricato e selezionare **genera**firma di accesso condiviso. Assicurarsi di impostare la **Data di scadenza** abbastanza lunga per coprire il periodo di test. Impostare i **protocolli consentiti** su *http* (*https* non è supportato).

Fare clic su **genera token SAS e URL** e copiare l'URL SAS BLOB. Sostituire l'oggetto iniziando `https` con `http` e testare l'URL in un browser che supporta la riproduzione video.

Sostituire `VIDEO_URL` nel [manifesto di distribuzione](https://go.microsoft.com/fwlink/?linkid=2142179) con l'URL creato per tutti i grafici. Impostare `VIDEO_IS_LIVE` su `false` e ridistribuire il contenitore di analisi spaziale con il manifesto aggiornato. Vedere l'esempio seguente.

Il modulo di analisi spaziale inizierà a utilizzare il file video e continuerà a riprodursi automaticamente.


```json
"zonecrossing": {
  "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
  "version": 1,
  "enabled": true,
  "parameters": {
      "VIDEO_URL": "Replace http url here",
      "VIDEO_SOURCE_ID": "personcountgraph",
      "VIDEO_IS_LIVE": false,
        "VIDEO_DECODE_GPU_INDEX": 0,
      "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
      "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"threshold\":35.0}]}"
    }
  },

```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi durante l'avvio o l'esecuzione del contenitore, vedere [telemetria e risoluzione dei](spatial-analysis-logging.md) problemi per i passaggi per i problemi comuni. Questo articolo contiene anche informazioni sulla generazione e la raccolta di log e sulla raccolta dell'integrità del sistema.

## <a name="billing"></a>Fatturazione

Il contenitore di analisi spaziale Invia le informazioni di fatturazione ad Azure usando una risorsa Visione artificiale nell'account Azure. L'uso dell'analisi spaziale nell'anteprima pubblica è attualmente gratuito. 

I contenitori di servizi cognitivi di Azure non sono concessi in licenza per l'esecuzione senza essere connessi all'endpoint di misurazione/fatturazione. È necessario consentire ai contenitori di comunicare sempre le informazioni di fatturazione all'endpoint di fatturazione. I contenitori di servizi cognitivi non inviano dati del cliente, ad esempio il video o l'immagine da analizzare, a Microsoft.


## <a name="summary"></a>Summary

In questo articolo sono stati appresi concetti e flussi di lavoro per il download, l'installazione e l'esecuzione del contenitore di analisi spaziale. In sintesi:

* L'analisi spaziale è un contenitore Linux per docker.
* Le immagini del contenitore vengono scaricate da Microsoft Container Registry.
* Le immagini del contenitore vengono eseguite come moduli delle cose in Azure IoT Edge.
* Come configurare il contenitore e distribuirlo in un computer host.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Web per il conteggio delle persone](spatial-analysis-web-app.md)
* [Configurare le operazioni di analisi spaziale](spatial-analysis-operations.md)
* [Registrazione e risoluzione dei problemi](spatial-analysis-logging.md)
* [Guida al posizionamento della fotocamera](spatial-analysis-camera-placement.md)
* [Guida al posizionamento di zone e linee](spatial-analysis-zone-line-placement.md)
