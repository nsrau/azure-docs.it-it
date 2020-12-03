---
title: Esercitazione - Creare una gerarchia di dispositivi IoT Edge - Azure IoT Edge
description: Questa esercitazione illustra come creare una struttura gerarchica di dispositivi IoT Edge usando i gateway.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 28b34ecaf51406b35c67d3838714691390f5adf7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453059"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Esercitazione: Creare una gerarchia di dispositivi IoT Edge (Anteprima)

Questa esercitazione illustra come distribuire nodi di Azure IoT Edge in reti organizzate in livelli gerarchici. Ogni livello di una gerarchia è un dispositivo gateway che gestisce messaggi e richieste provenienti da dispositivi del livello sottostante.

>[!NOTE]
>Per questa funzionalità è necessario IoT Edge versione 1.2, disponibile in anteprima pubblica, che esegue contenitori Linux.

È possibile strutturare una gerarchia di dispositivi in modo che solo il livello superiore disponga della connettività al cloud e che i livelli inferiori possano comunicare solo con i livelli a nord e a sud adiacenti. Questa suddivisione in livelli della rete è alla base della maggior parte delle reti industriali, che seguono lo [standard ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

L'obiettivo di questa esercitazione è creare una gerarchia di dispositivi IoT Edge che simuli un ambiente di produzione. Alla fine dell'esercitazione si distribuirà il [modulo Simulated Temperature Sensor](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) in un dispositivo di livello inferiore senza accesso a Internet scaricando le immagini del contenitore attraverso la gerarchia.

Per realizzare questo obiettivo, questa esercitazione illustra nel dettaglio la procedura di creazione di una gerarchia di dispositivi IoT Edge, la distribuzione di contenitori di runtime IoT Edge nei dispositivi e la configurazione locale dei dispositivi. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare e definire le relazioni in una gerarchia di dispositivi IoT Edge.
> * Configurare il runtime IoT Edge nei dispositivi della gerarchia.
> * Installare certificati coerenti nella gerarchia dei dispositivi.
> * Aggiungere carichi di lavoro ai dispositivi della gerarchia.
> * Usare un modulo proxy API per instradare in modo sicuro il traffico HTTP attraverso una singola porta dai dispositivi di livello inferiore.

In questa esercitazione vengono definiti i livelli di rete seguenti:

* **Livello superiore**: i dispositivi IoT Edge in questo livello possono connettersi direttamente al cloud.

* **Livello inferiore**: i dispositivi IoT Edge in questo livello non possono connettersi direttamente al cloud. Devono passare attraverso uno o più dispositivi IoT Edge intermediari per inviare e ricevere dati.

Questa esercitazione usa una gerarchia costituita da due dispositivi per semplicità. Un dispositivo, **topLayerDevice**, rappresenta il dispositivo al livello superiore della gerarchia che può connettersi direttamente al cloud. Questo dispositivo è detto anche **dispositivo padre**. L'altro dispositivo, **lowerLayerDevice**, rappresenta il dispositivo al livello inferiore della gerarchia che non può connettersi direttamente al cloud. Questo dispositivo è detto anche **dispositivo figlio**. È possibile aggiungere altri dispositivi di livello inferiore per rappresentare l'ambiente di produzione. Gli eventuali dispositivi aggiunti al livello inferiore dovranno essere configurati allo stesso modo di **lowerLayerDevice**.

## <a name="prerequisites"></a>Prerequisiti

Per creare una gerarchia di dispositivi IoT Edge occorre:

* Un computer (Windows o Linux) con connettività Internet.
* Due dispositivi Linux da configurare come dispositivi IoT Edge. Se non si hanno dispositivi a disposizione, è possibile usare [macchine virtuali di Azure](../virtual-machines/linux/index.yml).
* Un account Azure con una sottoscrizione valida. Se non si ha una [sottoscrizione di Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Interfaccia della riga di comando di Azure v2.3.1 con l'estensione Azure IoT versione 0.10.6 o successiva installata. Questa esercitazione usa [Azure Cloud Shell](../cloud-shell/overview.md). Se non si ha familiarità con Azure Cloud Shell, [consultare una guida di avvio rapido per informazioni dettagliate](./quickstart-linux.md#use-azure-cloud-shell).

È anche possibile provare questo scenario seguendo l'[esempio di Azure IoT Edge per l'IoT industriale](https://aka.ms/iotedge-nested-sample) basato su script, che distribuisce macchine virtuali di Azure come dispositivi preconfigurati per simulare un ambiente di fabbrica.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configurare la gerarchia di dispositivi IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Creare una gerarchia di dispositivi IoT Edge

Il primo passaggio, che consiste nella creazione di dispositivi IoT Edge, può essere eseguito tramite il portale di Azure o l'interfaccia della riga di comando di Azure. In questa esercitazione si creerà una gerarchia di due dispositivi IoT Edge: **topLayerDevice** e il relativo figlio **lowerLayerDevice**.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://ms.portal.azure.com/) passare all'hub IoT.

1. Nel menu nel riquadro sinistro, in **Gestione dispositivi automatica**, selezionare **IoT Edge**.

1. Selezionare **+ Aggiungi un dispositivo IoT Edge**. Questo dispositivo sarà il dispositivo del livello superiore, quindi occorre immettere un ID dispositivo univoco appropriato. Selezionare **Salva**.

1. Selezionare di nuovo **+ Aggiungi un dispositivo IoT Edge**. Questo dispositivo sarà il dispositivo del livello inferiore, quindi occorre immettere un ID dispositivo univoco appropriato.

1. Scegliere **Imposta un dispositivo padre**, selezionare il dispositivo del livello superiore dall'elenco dei dispositivi e quindi scegliere **OK**. Selezionare **Salva**.

   ![Impostazione del dispositivo padre per il dispositivo del livello inferiore](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. In [Azure Cloud Shell](https://shell.azure.com/) immettere il comando seguente per creare un dispositivo IoT Edge nell'hub. Questo dispositivo sarà il dispositivo del livello superiore, quindi occorre immettere un ID dispositivo univoco appropriato:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Immettere il comando seguente per creare il dispositivo IoT Edge figlio e creare la relazione padre-figlio tra i dispositivi:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Prendere nota della stringa di connessione di ciascun dispositivo IoT Edge. Verranno usate più avanti.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://ms.portal.azure.com/) passare alla sezione **IoT Edge** dell'hub IoT.

1. Fare clic sull'ID dispositivo di uno dei dispositivi nell'elenco dei dispositivi.

1. Selezionare **Copia** nel campo **Stringa di connessione primaria** e registrare la stringa in una posizione di propria scelta.

1. Ripetere questo passaggio per tutti gli altri dispositivi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. In [Azure Cloud Shell](https://shell.azure.com/), per ogni dispositivo, immettere il comando seguente per recuperare la stringa di connessione del dispositivo e registrarla in una posizione di propria scelta:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>Creare i certificati

Tutti i dispositivi in uno [scenario di gateway](iot-edge-as-gateway.md) hanno bisogno di un certificato condiviso per configurare connessioni sicure tra di essi. Seguire la procedura seguente per creare certificati demo per entrambi i dispositivi in questo scenario.

Per creare certificati demo in un dispositivo Linux, è necessario clonare gli script di generazione e configurarli per l'esecuzione locale in bash.

1. Clonare il repository Git IoT Edge che contiene gli script per generare certificati demo.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Passare alla directory che si vuole usare, Tutti i file di certificato e di chiave verranno creati in questa directory.

1. Copiare i file di configurazione e script dal repository IoT Edge clonato alla directory di lavoro.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Creare il certificato CA radice e un certificato intermedio.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Questo comando di script crea diversi file di certificato e di chiave, ma come **certificato CA radice** per la gerarchia del gateway si userà il file seguente:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Creare due set di certificati CA e chiavi private dei dispositivi IoT Edge con il comando seguente: un set per il dispositivo del livello superiore e un set per il dispositivo del livello inferiore. Specificare per i certificati CA nomi facili da ricordare e che consentano di distinguere un certificato dall'altro.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   Questo comando di script crea diversi file di certificato e di chiave, ma in ogni dispositivo IoT Edge si userà la coppia di certificato e chiave seguente, a cui viene fatto riferimento nel file config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Ogni dispositivo deve avere una copia del certificato CA radice e una copia del certificato CA e della chiave privata specifici del dispositivo. È possibile usare un'unità USB o [Secure File Copy](https://www.ssh.com/ssh/scp/) per spostare i certificati in ogni dispositivo.

1. Una volta trasferiti i certificati, installare la CA radice per ogni dispositivo.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   L'output di questo comando dovrebbe indicare che un certificato è stato aggiunto in /etc/ssl/certs.

### <a name="install-iot-edge-on-the-devices"></a>Installare IoT Edge nei dispositivi

Installare IoT Edge seguendo questa procedura in entrambi i dispositivi.

1. Aggiornare gli elenchi di pacchetti nel dispositivo.

   ```bash
   sudo apt-get update
   ```

1. Installare il motore Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Installare hsmlib e il daemon IoT Edge <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/libiothsm-std_1.2.0.rc2-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/iotedge_1.2.0_rc2-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>Configurare il runtime di IoT Edge

Configurare il runtime di IoT Edge seguendo questa procedura in entrambi i dispositivi. La procedura di configurazione del runtime di IoT Edge per i dispositivi è costituita da quattro passaggi, tutti eseguiti modificando il file di configurazione di IoT Edge:

1. Effettuare manualmente il provisioning di ogni dispositivo aggiungendo la relativa stringa di connessione al file di configurazione.

1. Completare la configurazione dei certificati del dispositivo puntando il file di configurazione al certificato della CA del dispositivo, alla chiave privata della CA del dispositivo e al certificato CA radice.

1. Eseguire il bootstrap del sistema con l'agente di IoT Edge.

1. Aggiornare il parametro **hostname** del dispositivo del **livello superiore** e i parametri **hostname** e **parent_hostname** dei dispositivi del **livello inferiore**.

Completare questi passaggi e riavviare il servizio IoT Edge per configurare i dispositivi.

1. Aprire il file di configurazione di IoT Edge in ogni dispositivo.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Trovare le configurazioni del provisioning del file e rimuovere il commento dalla sezione **Manual provisioning configuration using a connection string**.

1. Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge. Verificare che tutte le altre sezioni di provisioning siano impostate come commento. Verificare che la riga **provisioning:** non sia preceduta da spazi vuoti e che gli elementi annidati siano rientrati di due spazi.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >Per incollare il contenuto degli Appunti in Nano, premere `Shift+Right Click` o `Shift+Insert`.

1. Trovare la sezione **certificates**. Rimuovere il commento e aggiornare i tre campi di certificati in modo che puntino ai certificati creati nella sezione precedente e spostati nel dispositivo IoT Edge. Specificare i percorsi URI, che sono in formato `file:///<path>/<filename>`.

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. Per il dispositivo del **livello superiore** trovare il parametro **hostname**. Aggiornare il valore con il nome di dominio completo (FQDN) o l'indirizzo IP del dispositivo IoT Edge. Qualunque sia il valore scelto, usarlo in modo coerente nei dispositivi della gerarchia.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. Per i dispositivi IoT Edge del **livello inferiore** aggiornare il file di configurazione in modo che punti al nome di dominio completo o all'indirizzo IP del dispositivo padre, in base al valore del campo **hostname** del dispositivo padre. Per il dispositivo IoT Edge del **livello superiore** lasciare questo parametro impostato come commento.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > Per le gerarchie con più di un livello inferiore, aggiornare il campo *parent_hostname* con il nome di dominio completo del dispositivo nel livello immediatamente superiore.

1. Per il dispositivo del **livello superiore** trovare la sezione yaml **agent** e aggiornare il valore dell'immagine alla versione corretta dell'agente IoT Edge. In questo caso, l'agente IoT Edge del livello superiore dovrà puntare al Registro Azure Container in cui è disponibile la versione in anteprima pubblica dell'immagine dell'agente IoT Edge.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Per i dispositivi IoT Edge dei **livelli inferiori**, aggiornare il nome di dominio del valore dell'immagine in modo che punti al nome di dominio completo o all'indirizzo IP del dispositivo padre seguito dalla porta del proxy API 8000. Il modulo proxy API verrà aggiunto nella sezione successiva.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

1. Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Distribuire i moduli nel dispositivo del livello superiore

I passaggi che rimangono per completare la configurazione del runtime di IoT Edge e distribuire i carichi di lavoro vengono eseguiti dal cloud tramite il portale di Azure o l'interfaccia della riga di comando di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nel [portale di Azure](https://ms.portal.azure.com/):

1. Passare all'hub IoT.

1. Nel menu nel riquadro sinistro, in **Gestione dispositivi automatica**, selezionare **IoT Edge**.

1. Fare clic sull'ID dispositivo del dispositivo IoT Edge del **livello superiore** nell'elenco dei dispositivi.

1. Sulla barra superiore selezionare **Imposta moduli**.

1. Selezionare **Impostazioni runtime** accanto all'icona a forma di ingranaggio.

1. In **Hub Edge**, nel campo dell'immagine, immettere `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2`.

   ![Modificare l'immagine dell'hub Edge](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Aggiungere le variabili di ambiente seguenti al modulo Hub Edge:

    | Nome | valore |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Modificare le variabili di ambiente dell'hub Edge](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. In **Agente IoT Edge**, nel campo dell'immagine, immettere `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2`. Selezionare **Salva**.

1. Aggiungere il modulo del registro Docker al dispositivo del livello superiore. Selezionare **+ Aggiungi** e scegliere **Modulo IoT Edge** nell'elenco a discesa. Specificare il nome `registry` per il modulo del registro Docker e immettere `registry:latest` per l'URI dell'immagine. Aggiungere quindi variabili di ambiente e opzioni di creazione in modo che il modulo del registro locale punti al registro contenitori Microsoft da cui scaricare le immagini del contenitore e per inviare queste immagini al registro sulla porta 5000.

1. Nella scheda Variabili di ambiente immettere la coppia nome-valore di variabile di ambiente seguente:

    | Nome | valore |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. Nella scheda Opzioni di creazione del contenitore immettere il codice JSON seguente:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Aggiungere quindi il modulo proxy API al dispositivo del livello superiore. Selezionare **+ Aggiungi** e scegliere **Modulo del Marketplace** nell'elenco a discesa. Cercare `IoT Edge API Proxy` e selezionare il modulo. Il proxy API di IoT Edge usa la porta 8000 ed è configurato per usare il modulo di registro denominato `registry` sulla porta 5000 per impostazione predefinita.

1. Selezionare **Rivedi e crea** e quindi **Crea** per completare la distribuzione. Il runtime IoT Edge del dispositivo del livello superiore, che ha accesso a Internet, effettuerà il pull e l'esecuzione delle configurazioni in **anteprima pubblica** dell'hub di IoT Edge e dell'agente IoT Edge.

   ![Distribuzione completa contenente l'hub Edge, l'agente IoT Edge, il modulo del registro e il modulo proxy API](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. In [Azure Cloud Shell](https://shell.azure.com/) immettere il comando seguente per creare un file deployment.json:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Copiare il contenuto del codice JSON seguente nel file deployment.json, quindi salvare e chiudere il file.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Immettere il comando seguente per creare una distribuzione nel dispositivo IoT Edge del livello superiore:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>Distribuire i moduli nel dispositivo del livello inferiore

È possibile usare sia il portale di Azure che l'interfaccia della riga di comando di Azure per distribuire i carichi di lavoro dal cloud ai dispositivi del **livello inferiore**.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nel [portale di Azure](https://ms.portal.azure.com/):

1. Passare all'hub IoT.

1. Nel menu nel riquadro sinistro, in **Gestione dispositivi automatica**, selezionare **IoT Edge**.

1. Fare clic sull'ID dispositivo del dispositivo del livello inferiore nell'elenco dei dispositivi IoT Edge.

1. Sulla barra superiore selezionare **Imposta moduli**.

1. Selezionare **Impostazioni runtime** accanto all'icona a forma di ingranaggio.

1. In **Hub Edge**, nel campo dell'immagine, immettere `$upstream:8000/azureiotedge-hub:1.2.0-rc2`.

1. Aggiungere le variabili di ambiente seguenti al modulo Hub Edge:

    | Nome | valore |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. In **Agente IoT Edge**, nel campo dell'immagine, immettere `$upstream:8000/azureiotedge-agent:1.2.0-rc2`. Selezionare **Salva**.

1. Aggiungere il modulo sensore temperatura. Selezionare **+ Aggiungi** e scegliere **Modulo del Marketplace** nell'elenco a discesa. Cercare `Simulated Temperature Sensor` e selezionare il modulo.

1. In **Moduli IoT Edge** selezionare il modulo `Simulated Temperature Sensor` appena aggiunto e aggiornare l'URI dell'immagine in modo che punti a `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0`.

1. Selezionare **Salva**, quindi **Rivedi e crea** e infine **Crea** per completare la distribuzione.

   ![Distribuzione completa contenente l'hub Edge, l'agente IoT Edge e il modulo Simulated Temperature Sensor](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. In [Azure Cloud Shell](https://shell.azure.com/) immettere il comando seguente per creare un file deployment.json:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Copiare il contenuto del codice JSON seguente nel file deployment.json, quindi salvare e chiudere il file.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Immettere il comando seguente per creare una distribuzione di moduli nel dispositivo IoT Edge del livello inferiore:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## View generated data

The **Simulated Temperature Sensor** module that you pushed generates sample environment data. It sends messages that include ambient temperature and humidity, machine temperature and pressure, and a timestamp.

You can watch the messages arrive at your IoT hub by using the [Azure IoT Hub extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

You can also view these messages through the [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare le configurazioni locali e le risorse di Azure create in questo articolo per evitare addebiti.

Per eliminare le risorse:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Selezionare il nome del gruppo di risorse contenente le risorse di test di IoT Edge. 

3. Esaminare l'elenco delle risorse contenute nel gruppo di risorse. Per eliminarle tutte, è possibile selezionare **Elimina gruppo di risorse**. Se se ne vogliono eliminare solo alcune, è possibile fare clic su ogni risorsa per eliminarle singolarmente. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati configurati due dispositivi IoT Edge come gateway, di cui uno è stato impostato come dispositivo padre dell'altro. Quindi è stato illustrato il pull di un'immagine del contenitore nel dispositivo figlio tramite un gateway. È anche possibile provare questo scenario seguendo l'[esempio di Azure IoT Edge per l'IoT industriale](https://aka.ms/iotedge-nested-sample) basato su script, che distribuisce macchine virtuali di Azure come dispositivi preconfigurati per simulare un ambiente di fabbrica.

È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può creare altre soluzioni per il business.

> [!div class="nextstepaction"]
> [Distribuire un modello di Azure Machine Learning come modulo](tutorial-deploy-machine-learning.md)