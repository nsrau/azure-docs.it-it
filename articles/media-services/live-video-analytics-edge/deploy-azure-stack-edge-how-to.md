---
title: Distribuire Analisi video live in Azure Stack Edge
description: Questo articolo elenca i passaggi che consentono di distribuire analisi video in tempo reale sul Azure Stack Edge.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b13bb779a5a780b21f2d5d96ed8831ef5c26564d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937715"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Distribuire Analisi video live in Azure Stack Edge

Questo articolo elenca i passaggi che consentono di distribuire analisi video in tempo reale sul Azure Stack Edge. Una volta che il dispositivo è stato configurato e attivato, è pronto per la distribuzione di analisi video in tempo reale. 

Per l'analisi di video in tempo reale, si distribuirà tramite l'hub tutto, ma le risorse di Azure Stack Edge espongono un'API Kubernetes, che consente al cliente di distribuire soluzioni aggiuntive per l'hub non-Internet che possono interfacciarsi con analisi video in tempo reale. 

> [!TIP]
> L'uso dell'API Kubernetes (K8s) per la distribuzione personalizzata è un caso avanzato. Si consiglia al cliente di creare moduli perimetrali e distribuirli tramite l'hub Internet per ogni risorsa Azure Stack Edge invece di usare l'API Kubernetes. In questo articolo vengono illustrati i passaggi per la distribuzione del modulo Live Video Analytics con l'hub Internet.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure a cui si dispone dei [privilegi di proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner).
* Una risorsa [Azure stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)
   
* [Un hub Internet delle cose](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)
* Un' [entità servizio](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/create-custom-azure-resource-manager-role-how-to#create-service-principal) per il modulo Live Video Analytics.

   Usare una di queste aree in cui è disponibile l'hub Internet: Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali, Giappone orientale, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Canada orientale, Regno Unito meridionale, Francia centrale, Francia meridionale, Svizzera settentrionale, Svizzera occidentale e Giappone occidentale.
* Account di archiviazione

    Si consiglia di usare gli account di archiviazione per utilizzo generico V2 (GPv2).  
    Altre informazioni su un [account di archiviazione per utilizzo generico V2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade?tabs=azure-portal).
* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo. Assicurarsi che sia presente l'[estensione Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Verificare che la rete a cui è connesso il computer di sviluppo accetti il protocollo AMQP (Advanced Message Queueing Protocol) sulla porta 5671. Questa configurazione consente la comunicazione tra Azure IoT Tools e l'hub IoT di Azure.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Configurazione di Azure Stack Edge per l'uso di analisi video in tempo reale

Azure Stack Edge è una soluzione hardware come servizio e un dispositivo di calcolo Edge abilitato per intelligenza artificiale con funzionalità di trasferimento dei dati di rete. Per altre informazioni [, vedere Azure stack Edge e istruzioni dettagliate sulla configurazione](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep). Per iniziare, seguire le istruzioni riportate nei collegamenti seguenti:

* [Creazione di risorse Azure Stack Edge/Data Box Gateway](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)
* [Installazione e configurazione](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-install)
* [Connessione e attivazione](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate)

### <a name="attach-an-iot-hub-to-azure-stack-edge"></a>Alleghi un hub Internet all'Azure Stack Edge

1. Nella [portale di Azure](https://ms.portal.azure.com)passare alla risorsa di Azure stack Edge e fare clic su panoramica. Nel riquadro Calcolo a destra selezionare Inizia subito.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge.png" alt-text="Azure Stack Edge":::
1. Nel riquadro Configura calcolo Edge selezionare Configura calcolo.
1. Nel pannello Configura calcolo Edge immettere quanto segue:
    
    | Campo|valore|
    |---|---|
    |Hub IoT|Scegliere Nuovo o Esistente.<br/>Per impostazione predefinita, per creare una risorsa IoT viene usato un livello Standard (S1). Per usare una risorsa IoT di un livello gratuito, crearne uno e quindi selezionare la risorsa esistente.<br/>In ogni caso, la risorsa hub Internet usa la stessa sottoscrizione e il gruppo di risorse usato per la risorsa Azure Stack Edge.|
    |Nome|Immettere un nome per la risorsa hub IoT.|

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-get-started.png" alt-text="Introduzione a Azure Stack Edge":::
1. Selezionare **Crea**. La creazione della risorsa hub IoT richiede qualche minuto. Al termine della creazione della risorsa hub IoT, il riquadro **Configura calcolo** viene aggiornato per mostrare la configurazione di calcolo. Per verificare che il ruolo calcolo Edge sia stato configurato, selezionare **Visualizza calcolo** nel riquadro **Configura calcolo**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/edge-compute-config.png" alt-text="Creazione di risorse dell'hub Internet":::

    > [!NOTE]
    > Se la finestra di dialogo Configura calcolo viene chiusa prima che l'hub Internet sia associato alla risorsa Azure Stack Edge, l'hub Internet viene creato, ma non viene visualizzato nella configurazione di calcolo. Ricaricare la pagina dopo alcuni minuti e visualizzarne la visualizzazione.
    
    Quando il ruolo di calcolo Edge è configurato nel dispositivo Edge, crea due dispositivi: un dispositivo IoT e un dispositivo IoT Edge. Entrambi i dispositivi possono essere visualizzati nella risorsa dell'hub IoT. Un runtime di IoT Edge è in esecuzione anche sul dispositivo IoT Edge. Attualmente per il dispositivo IoT Edge è disponibile solo la piattaforma Linux.
    
    Una volta completate tutte le informazioni, verrà visualizzata la scheda Configura bordo di calcolo simile alla seguente:
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/configure-edge-compute.png" alt-text="Configurare la scheda di calcolo Edge ":::
 
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Abilitare i prerequisiti di calcolo nell'interfaccia utente locale di Azure Stack Edge

Prima di continuare, verificare che:

* È stata attivata la risorsa Azure Stack Edge.
* È possibile accedere a un sistema client Windows che esegue PowerShell 5,0 o versione successiva per accedere alla risorsa Azure Stack Edge.
* Per distribuire un cluster Kubernetes, è necessario configurare la risorsa Azure Stack Edge tramite l' [interfaccia utente Web locale](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate#connect-to-the-local-web-ui-setup). 
    
    * Per abilitare il calcolo, nell'interfaccia utente Web locale del dispositivo andare alla pagina calcolo.
    
        * Selezionare un'interfaccia di rete che si vuole abilitare per il calcolo. Selezionare Abilita. L'abilitazione dei risultati di calcolo nella creazione di un commutire virtuale sul dispositivo in tale interfaccia di rete.
        * Lasciare vuoti gli IP del nodo di test Kubernetes e gli indirizzi IP dei servizi esterni Kubernetes.
        * Selezionare Applica. questa operazione dovrebbe richiedere circa 2 minuti.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Prerequisiti di calcolo nell'interfaccia utente locale di Azure Stack Edge":::

        * Se DNS non è configurato per l'API Kubernetes e la risorsa Azure Stack Edge, è possibile aggiornare il file host della finestra.
        
            * Aprire un editor di testo come amministratore
            * Apri il file ' to C:\Windows\System32\drivers\etc\hosts '
            * Aggiungere il nome del dispositivo dell'API Kubernetes IPv4 e il nome host al file. Queste informazioni sono reperibili nel portale di Azure Stack Edge nella sezione dispositivi.
            * Salva e chiudi

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Distribuire un modulo Edge di analisi video in tempo reale usando portale di Azure

A questo proposito, verranno eseguiti solo passaggi specifici per la [distribuzione di analisi video in tempo reale tramite l'hub Internet](deploy-iot-edge-device.md).

1. Ignorare i passaggi per la creazione di utenti e gruppi e andare a distribuire un modulo [Edge di analisi video in tempo reale](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) . Seguire i passaggi indicati.
1. In opzioni di creazione del contenitore non è necessario impostare le variabili di ambiente. Ignorare questo passaggio.
1. Aprire la scheda Opzioni di creazione del contenitore.

   * Copiare e incollare il codice JSON seguente nella casella per limitare le dimensioni dei file di log generati dal modulo.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Se si usa il protocollo gRPC con trasferimento di memoria condivisa, usare la modalità IPC host per l'accesso alla memoria condivisa tra le soluzioni di analisi video e le soluzioni di inferenza Live.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > La sezione "binds" nel file JSON include 2 voci. È possibile aggiornare le associazioni del dispositivo perimetrale, ma assicurarsi che tali directory esistano.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": viene usato per associare i dati di configurazione dell'applicazione permanenti dal contenitore e archiviarli sul dispositivo perimetrale.
    * "/var/media:/var/media": associa le cartelle multimediali tra il dispositivo perimetrale e il contenitore. Viene usato per archiviare le registrazioni video quando si esegue una topologia del grafico multimediale che supporta l'archiviazione di clip video sul dispositivo perimetrale.
        
1. Continuare la procedura nel documento e compilare le impostazioni del modulo gemello.
1. Selezionare **Avanti**: route per passare alla sezione route. Specificare le route.

    Mantenere le route predefinite e selezionare Avanti: rivedere + crea per passare alla sezione revisione.
1. [Esaminare e verificare la distribuzione](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>Opzionale Configurare i montaggi del volume Docker

Se si vuole visualizzare i dati nelle directory di lavoro, seguire questa procedura per configurare i montaggi del volume Docker prima di distribuire. 

Questi passaggi illustrano la creazione di un utente del gateway e la configurazione delle condivisioni file per visualizzare il contenuto della directory di lavoro di analisi video live e della cartella media di analisi video in tempo reale.

> [!NOTE]
> Sono supportati i montaggi di binding, ma i montaggi del volume consentono la visualizzazione dei dati e, se lo si desidera, la copia remota. È possibile usare sia i montaggi che quelli del volume, ma non possono puntare allo stesso percorso del contenitore.

1. Aprire portale di Azure e passare alla risorsa Azure Stack Edge.
1. Creare un **utente del gateway** che possa accedere alle condivisioni.
    
    1. Nel riquadro di spostamento a sinistra fare clic su **gateway->utenti**.
    1. Fare clic su **+ Aggiungi utente** per impostare il nome utente e la password. (Scelta consigliata: `lvauser` ).
    1. Fare clic su **Aggiungi**.
    
1. Creare una **condivisione locale** per la persistenza di analisi video in tempo reale.

    1. Fare clic su **gateway->condivisioni**.
    1. Fare clic su **+ Aggiungi condivisioni**.
    1. Impostare un nome di condivisione. (Scelta consigliata: `lva` ).
    1. Mantieni il tipo di condivisione come SMB.
    1. Assicurarsi che **l'opzione Usa condivisione con calcolo Edge** sia selezionata.
    1. Verificare **che l'opzione configura come condivisione locale Edge** sia selezionata.
    1. In dettagli utente concedere l'accesso alla condivisione all'utente appena creato.
    1. Fare clic su **Crea**.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Condivisione locale":::
    
1. Creare una condivisione remota per l'archiviazione di sincronizzazione file.

    1. Creare innanzitutto un account di archiviazione BLOB nella stessa area.
    1. Fare clic su **gateway->condivisioni**.
    1. Fare clic su **+ Aggiungi condivisioni**.
    1. Impostare un nome di condivisione. (Scelta consigliata: supporti).
    1. Mantieni il tipo di condivisione come SMB.
    1. Assicurarsi che **l'opzione Usa condivisione con calcolo Edge** sia selezionata.
    1. Verificare **che l'opzione configura come condivisione locale perimetrale** non sia selezionata.
    1. Selezionare l'account di archiviazione creato di recente.
    1. Impostare un nome per il contenitore.
    1. Impostare il tipo di archiviazione su BLOB in blocchi.
    1. In dettagli utente concedere l'accesso alla condivisione all'utente appena creato.
    1. Fare clic su **Crea**.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Condivisione remota":::
    
    > [!TIP]
    > Utilizzando il client Windows connesso al Azure Stack Edge, connettersi alle condivisioni SMB attenendosi alla procedura descritta [in questo documento](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share).
    
1. Aggiornare le opzioni di creazione del contenitore del modulo Edge di analisi video in tempo reale (vedere il punto 4 in [aggiungere il documento moduli](deploy-iot-edge-device.md#add-modules)) per usare i montaggi del volume.

   ```json
    // Original (Bind Mounts)
    "createOptions": {
        "HostConfig": {
            "Binds": [
                "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
                "/var/media:/var/media"
            ]
        }
    }
    // Updated (Volume Mounts)
    "createOptions": {
        "HostConfig": {
            "Mounts": [
            {
                "Target": "/var/lib/azuremediaservices",
                "Source": "lva",
                "Type": "volume"
            },
            {
                "Target": "/var/media",
                "Source": "media",
                "Type": "volume"
            }]
        }
    }
    ```

### <a name="verify-that-the-module-is-running"></a>Verificare che il modulo sia in esecuzione

Il passaggio finale consiste nell'assicurarsi che il modulo sia connesso e in esecuzione come previsto. Lo stato di runtime del modulo deve essere In esecuzione per il dispositivo IoT Edge nella risorsa dell'hub IoT.

Per verificare che il modulo sia in esecuzione, eseguire queste operazioni:

1. Nella portale di Azure tornare alla risorsa Azure Stack Edge
1. Selezionare il riquadro moduli. Verrà visualizzato il pannello Moduli. Nell'elenco dei moduli identificare il modulo che è stato distribuito. Lo stato di runtime del modulo aggiunto sarà in esecuzione.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Modulo personalizzato":::

### <a name="configure-the-azure-iot-tools-extension"></a>Configurare l'estensione Azure IoT Tools

Seguire queste istruzioni per connettersi all'hub IoT usando l'estensione Azure IoT Tools.

1. In Visual Studio Code selezionare Visualizza > Esplora. In alternativa, premere CTRL+MAIUSC+E.
1. Nell'angolo in basso a sinistra della scheda Explorer selezionare Hub IoT di Azure.
1. Selezionare l'icona Altre opzioni icona per visualizzare il menu di scelta rapida. Quindi scegliere Set IoT Hub Connection String (Imposta la stringa di connessione dell'hub IoT).
1. Quando viene visualizzata una casella di input, immettere la stringa di connessione dell'hub IoT. 

   * Per ottenere la stringa di connessione, passare all'hub cose in portale di Azure e fare clic su criteri di accesso condiviso nel riquadro di spostamento a sinistra.
   * Fare clic su iothubowner per ottenere le chiavi di accesso condivise.
   * Copiare la stringa di connessione – chiave primaria e incollarla nella casella di input del VSCode.

   La stringa di connessione sarà simile alla seguente:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Se la connessione riesce, viene visualizzato l'elenco di dispositivi Edge. Il Azure Stack Edge verrà visualizzato. È ora possibile gestire i dispositivi IoT Edge e interagire con l'hub IoT di Azure tramite il menu di scelta rapida. Per visualizzare i moduli distribuiti nel dispositivo perimetrale, nel dispositivo Azure Stack espandere il nodo moduli.
    
## <a name="troubleshooting"></a>Risoluzione dei problemi

* Accesso all'API Kubernetes (kubectl).

    * Seguire la documentazione per configurare il computer per [l'accesso al cluster Kubernetes](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues).
    * Tutti i moduli IoT Edge distribuiti utilizzano lo `iotedge` spazio dei nomi. Assicurarsi di includerlo quando si usa kubectl.
* Log dei moduli

    Lo `iotedge` strumento non è accessibile per ottenere i log. Per visualizzare i log o la pipe a un file, è necessario usare i [registri kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  . Esempio: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Metriche di Pod e nodi

    Usare [kubectl Top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  per visualizzare le metriche pod e node. Questa funzionalità sarà disponibile nella prossima versione di Azure Stack Edge. >v2007)<br/>`kubectl top pods -n iotedge`
* Rete dei moduli per l'individuazione dei moduli in Azure Stack Edge è necessario che il modulo disponga dell'associazione della porta host in createOptions. Il modulo sarà quindi indirizzabile su `moduleName:hostport` .
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* Montaggio del volume

    Un modulo non verrà avviato se il contenitore sta provando a montare un volume in una directory esistente e non vuota.
* Shared Memory

    La memoria condivisa sulle risorse Azure Stack Edge è supportata in tutti i pod di qualsiasi spazio dei nomi tramite l'IPC host.
    Configurazione della memoria condivisa in un modulo perimetrale per la distribuzione tramite l'hub Internet.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* Avanzate Condivisione percorso Pod

    Quando si usa K8s per distribuire soluzioni di inferenza personalizzate che comunicano con analisi video in tempo reale tramite gRPC, è necessario assicurarsi che i pod siano distribuiti negli stessi nodi dei moduli di analisi video in tempo reale.

    * Opzione 1: usare l'affinità dei nodi e le etichette dei nodi predefinite per la condivisione percorso.

    Attualmente la configurazione personalizzata di NodeSelector non sembra essere un'opzione perché gli utenti non hanno accesso per impostare le etichette nei nodi. Tuttavia, a seconda della topologia e delle convenzioni di denominazione del cliente, potrebbero essere in grado di usare le [etichette dei nodi predefinite](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Una sezione nodeAffinity che fa riferimento a Azure Stack risorse perimetrali con analisi video live può essere aggiunta al manifesto di inferenza per ottenere la condivisione percorso.
    * Opzione 2: usare l'affinità pod per la condivisione percorso (scelta consigliata).
Kubernetes dispone del supporto per l' [affinità Pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  che può pianificare i pod nello stesso nodo. Una sezione podAffinity che fa riferimento al modulo Live Video Analytics può essere aggiunta al manifesto del pod di inferenza per ottenere la condivisione percorso.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>Passaggi successivi

È possibile usare il modulo per analizzare i flussi video live richiamando metodi diretti. [Richiamare i metodi diretti](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) sul modulo.
