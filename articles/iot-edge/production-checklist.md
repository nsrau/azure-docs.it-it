---
title: Preparare dispositivi e distribuzioni per la produzione - Azure IoT Edge | Microsoft Docs
description: Learn how to take your Azure IoT Edge solution from development to production, including setting up your devices with the appropriate certificates and making a deployment plan for future code updates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d8ba8452f5f2d4ab05083e1a97fa0b9ba75017f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457314"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Preparare la distribuzione della soluzione IoT Edge alla produzione

Una volta pronti a portare la soluzione IoT Edge dallo sviluppo alla produzione, assicurarsi che sia configurata per prestazioni costanti.

The information provided in this article isn't all equal. Per assicurarsi di definire le priorità, ogni sezione inizia con elenchi che dividono il lavoro in due sezioni: **importante** da completare prima di passare all'ambiente di produzione, o **utile** da sapere.

## <a name="device-configuration"></a>Configurazione del dispositivo

I dispositivi IoT Edge possono essere qualsiasi cosa, da un dispositivo Raspberry Pi a un computer portatile, a una macchina virtuale in esecuzione in un server. È possibile accedere al dispositivo fisicamente o tramite una connessione virtuale, oppure può essere isolato per lunghi periodi di tempo. Either way, you want to make sure that it's configured to work appropriately. 

* **Importante**
    * Installare i certificati di produzione
    * Disporre di un piano di gestione dei dispositivi
    * Usare Moby come motore del contenitore

* **Utile**
    * Scegliere il protocollo di upstream

### <a name="install-production-certificates"></a>Installare i certificati di produzione

Ogni dispositivo IoT Edge nell'ambiente di produzione richiede un certificato di autorità di certificazione (CA) del dispositivo installato. Tale certificato della CA viene quindi dichiarato per il runtime di IoT Edge nel file config.yaml file. Per semplificare lo sviluppo e il test, il runtime di IoT Edge consente di creare certificati temporanei se non sono presenti certificati dichiarati nel file config.yaml. Tuttavia, questi certificati temporanei scadono dopo tre mesi e non sono sicuri per gli scenari di produzione. 

Per comprendere il ruolo del certificato della CA del dispositivo, vedere [Come Azure IoT Edge usa i certificati](iot-edge-certs.md).

Per altre informazioni su come installare i certificati su un dispositivo IoT Edge e farvi riferimento dal file config.yaml, vedere [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md). I passaggi per configurare i certificati sono gli stessi nel caso in cui il dispositivo verrà essere utilizzato come gateway o meno. In questo articolo vengono forniti gli script per generare i certificati di esempio solo per il test. Non usare i certificati di esempio nell'ambiente di produzione. 

### <a name="have-a-device-management-plan"></a>Disporre di un piano di gestione dei dispositivi

Prima di rendere disponibili tutti i dispositivi nell'ambiente di produzione è necessario sapere come si intende gestire gli aggiornamenti futuri. Per un dispositivo IoT Edge, l'elenco dei componenti da aggiornare può includere:

* Il firmware del dispositivo
* Le librerie del sistema operativo
* Motore del contenitore, ad esempio Moby
* Daemon IoT Edge
* Certificati di CA

For more information, see [Update the IoT Edge runtime](how-to-update-iot-edge.md). Gli attuali metodi per aggiornare il daemon di IoT Edge richiedono l'accesso SSH o fisico al dispositivo IoT Edge. If you have many devices to update, consider adding the update steps to a script or use an automation tool like Ansible.

### <a name="use-moby-as-the-container-engine"></a>Usare Moby come motore del contenitore

A container engine is a prerequisite for any IoT Edge device. Solo il motore moby è supportato nell'ambiente di produzione. Gli altri motori del contenitore, come Docker, funzionano con IoT Edge ed è comunque possibile usare questi motori per lo sviluppo. Il motore moby può essere ridistribuito quando usato con Azure IoT Edge e Microsoft fornisce assistenza per questo motore.

### <a name="choose-upstream-protocol"></a>Scegliere il protocollo di upstream

The protocol (and therefore the port used) for upstream communication to IoT Hub can be configured for both the IoT Edge agent and the IoT Edge hub. Il protocollo predefinito è AMQP, ma è possibile modificarlo a seconda della configurazione di rete. 

I due moduli runtime hanno una variabile di ambiente **UpstreamProtocol**. I valori validi per la variabile sono: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure the UpstreamProtocol variable for the IoT Edge agent in the config.yaml file on the device itself. For example, if your IoT Edge device is behind a proxy server that blocks AMQP ports, you may need to configure the IoT Edge agent to use AMQP over WebSocket (AMQPWS) to establish the initial connection to IoT Hub. 

Una volta che il dispositivo IoT Edge si connette, assicurarsi di continuare a configurare la variabile UpstreamProtocol per entrambi i moduli runtime nelle distribuzioni future. È possibile trovare un esempio di questo processo in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Distribuzione

* **Utile**
    * Essere coerenti con il protocollo upstream
    * Set up host storage for system modules
    * Reduce memory space used by the IoT Edge hub
    * Non usare le versioni di debug delle immagini del modulo

### <a name="be-consistent-with-upstream-protocol"></a>Essere coerenti con il protocollo upstream

If you configured the IoT Edge agent on your IoT Edge device to use a different protocol than the default AMQP, then you should declare the same protocol in all future deployments. Ad esempio, se il dispositivo IoT Edge si trova dietro un server proxy che blocca le porte AMQP,è probabile che il dispositivo sia stato configurato per connettersi tramite AMQP su WebSocket (AMQPWS). When you deploy modules to the device, configure the same AMQPWS protocol for the IoT Edge agent and IoT Edge hub, or else the default AMQP will override the settings and prevent you from connecting again. 

You only have to configure the UpstreamProtocol environment variable for the IoT Edge agent and IoT Edge hub modules. Tutti i moduli aggiuntivi adottano qualsiasi protocollo impostato nei moduli del runtime. 

È possibile trovare un esempio di questo processo in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Set up host storage for system modules

The IoT Edge hub and agent modules use local storage to maintain state and enable messaging between modules, devices, and the cloud. For better reliability and performance, configure the system modules to use storage on the host filesystem.

For more information, see [Host storage for system modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduce memory space used by IoT Edge hub

If you're deploying constrained devices with limited memory available, you can configure IoT Edge hub to run in a more streamlined capacity and use less disk space. These configurations do limit the performance of the IoT Edge hub, however, so find the right balance that works for your solution. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Non ottimizzare le prestazioni su dispositivi vincolati

The IoT Edge hub is optimized for performance by default, so it attempts to allocate large chunks of memory. Questa configurazione può causare problemi di stabilità in dispositivi più piccoli come Raspberry Pi. If you're deploying devices with constrained resources, you may want to set the **OptimizeForPerformance** environment variable to **false** on the IoT Edge hub. 

When **OptimizeForPerformance** is set to **true**, the MQTT protocol head uses the PooledByteBufferAllocator which has better performance but allocates more memory. The allocator does not work well on 32 bit operating systems or on devices with low memory. Additionally, when optimized for performance, RocksDb allocates more memory for its role as the local storage provider. 

Per altre informazioni, vedere [Problemi di stabilità nei dispositivi con risorse limitate](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Disabilitare i protocolli non usati

Another way to optimize the performance of the IoT Edge hub and reduce its memory usage is to turn off the protocol heads for any protocols that you're not using in your solution. 

Protocol heads are configured by setting boolean environment variables for the IoT Edge hub module in your deployment manifests. Le tre variabili sono:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Tutte le tre variabili hanno *due caratteri di sottolineatura* e possono essere impostate su true o false. 

#### <a name="reduce-storage-time-for-messages"></a>Ridurre il tempo di archiviazione per i messaggi

The IoT Edge hub module stores messages temporarily if they cannot be delivered to IoT Hub for any reason. You can configure how long the IoT Edge hub holds on to undelivered messages before letting them expire. If you have memory concerns on your device, you can lower the **timeToLiveSecs** value in the IoT Edge hub module twin. 

Il valore predefinito del parametro timeToLiveSecs è 7200 secondi, che equivale a due ore. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Non usare le versioni di debug delle immagini del modulo

Quando si passa da scenari di test a scenari di produzione, ricordarsi di rimuovere le configurazioni di debug dai manifesti di distribuzione. Verificare che nessuna delle immagini dei moduli nei manifesti di distribuzione abbia il suffisso  **\.debug**. Se sono state aggiunte opzioni di creazione per esporre le porte nei moduli per il debug, rimuovere anche queste opzioni di creazione. 

## <a name="container-management"></a>Gestione di contenitori

* **Importante**
    * Gestire l'accesso nel registro contenitori
    * Usare tag per gestire le versioni

### <a name="manage-access-to-your-container-registry"></a>Gestire l'accesso nel registro contenitori

Prima di distribuire moduli nei dispositivi IoT Edge di produzione, assicurarsi di controllare l'accesso al registro contenitori in modo che gli utenti esterni non possano accedere o apportare modifiche alle immagini del contenitore. Usare un registro contenitori privato, non pubblico, per gestire le immagini del contenitore. 

Nelle esercitazioni e in altri documenti, viene indicato di usare nel dispositivo IoT Edge le stesse credenziali del registro contenitori usate nel computer di sviluppo. Queste istruzioni sono destinate solamente a facilitare la configurazione degli ambienti di test e di sviluppo e non devono essere seguite in uno scenario di produzione. Registro Azure Container consiglia [l'autenticazione con le entità servizio](../container-registry/container-registry-auth-service-principal.md) quando le applicazioni o i servizi eseguono il pull delle immagini del contenitore in modo automatico o automatizzato, così come i dispositivi IoT Edge. Creare un'entità servizio con accesso in lettura al registro contenitori e specificare tale nome utente e password nel manifesto di distribuzione.

### <a name="use-tags-to-manage-versions"></a>Usare tag per gestire le versioni

A tag is a docker concept that you can use to distinguish between versions of docker containers. I tag sono suffissi come **1.0** che vanno alla fine di un repository del contenitore. Ad esempio, **mcr.microsoft.com/azureiotedge-agent:1.0**. I tag sono modificabili e possono essere modificati in modo da puntare a un altro contenitore in qualsiasi momento, in modo che il team debba concordare una convenzione da seguire quando si aggiornano le immagini del modulo. 

I tag consentono inoltre di applicare gli aggiornamenti sui dispositivi IoT Edge. Quando si esegue il push di una versione aggiornata di un modulo nel registro contenitori, incrementare il tag. Eseguire quindi il push di una nuova distribuzione per i dispositivi con il tag incrementato. Il motore contenitore riconoscerà il tag incrementato come una nuova versione ed eseguirà il pull della versione più recente del modulo nel dispositivo. 

Per un esempio di una convenzione di tag, vedere [Aggiornare il runtime IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) per informazioni su come IoT Edge usa i tag in sequenza e tag specifici per tenere traccia delle versioni. 

## <a name="networking"></a>Rete

* **Utile**
    * Verificare la configurazione in uscita e in entrata
    * Allow connections from IoT Edge devices
    * Configurare la comunicazione tramite un proxy

### <a name="review-outboundinbound-configuration"></a>Verificare la configurazione in uscita e in entrata

I canali di comunicazione tra Azure IoT Edge e hub IoT di Azure sono sempre configurati per essere in uscita. Per la maggior parte degli scenari di IoT Edge, sono necessarie solo tre connessioni. Il motore del contenitore deve connettersi con il registro contenitori (o i registri) che contiene le immagini del modulo. Il runtime IoT Edge deve connettersi all'hub IoT per recuperare le informazioni di configurazione del dispositivo e per inviare i messaggi e i dati di telemetria. E se si usa il provisioning automatico, il daemon di IoT Edge deve connettersi al servizio di provisioning di dispositivi. Per altre informazioni, vedere [Regole di configurazione del firewall e delle porte](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Allow connections from IoT Edge devices

If your networking setup requires that you explicitly permit connections made from IoT Edge devices, review the following list of IoT Edge components:

* **Agente IoT Edge** apre una connessione MQTT/AMQP permanente all'hub IoT, possibilmente tramite WebSockets. 
* **Hub di IoT Edge** apre una singola connessione AMQP permanente o più connessioni MQTT all'hub IoT, probabilmente tramite WebSockets. 
* **Daemon di IoT Edge** effettua chiamate HTTPS intermittenti all'hub IoT. 

In tutti i tre casi, il nome DNS dovrebbe corrisponde al modello \*.azure-devices.net. 

Inoltre, il **motore del contenitore** effettua chiamate ai registri contenitori tramite HTTPS. Per recuperare le immagini del contenitore del runtime IoT Edge, il nome DNS è mcr.microsoft.com. Il motore del contenitore si connette ad altri registri in base alla configurazione della distribuzione. 

Questo elenco di controllo è un punto di partenza per le regole del firewall:

   | URL (\* = carattere jolly) | Porte TCP in uscita | Utilizzo |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registro contenitori Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Accesso DPS (facoltativo) |
   | \*.azurecr.io | 443 | Personal and third-party container registries |
   | \*.blob.core.windows.net | 443 | Download Azure Container Registry image deltas from blob storage  | 
   | \*.azure-devices.net | 5671, 8883, 443 | Accesso hub IoT |
   | \*.docker.io  | 443 | Docker Hub access (optional) |

Some of these firewall rules are inherited from Azure Container Registry. For more information, see [Configure rules to access an Azure container registry behind a firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Configurare la comunicazione tramite un proxy

Se i dispositivi vengono distribuiti su una rete che utilizza un server proxy, devono essere in grado di comunicare attraverso il proxy per raggiungere l'hub IoT e i registri contenitori. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gestione della soluzione

* **Utile**
    * Impostare i log e la diagnostica
    * Prendere in considerazione i test e le pipeline CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Impostare i log e la diagnostica

On Linux, the IoT Edge daemon uses journals as the default logging driver. È possibile usare lo strumento della riga di comando `journalctl` per eseguire una query dei log daemon. In Windows, il daemon di IoT Edge usa la diagnostica di PowerShell. Usare `Get-IoTEdgeLog` per eseguire query dei log da daemon. IoT Edge modules use the JSON driver for logging, which is the  default.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Quando si esegue il test di una distribuzione di IoT Edge, è generalmente possibile accedere ai dispositivi per recuperare i log e risolvere i problemi. In uno scenario di distribuzione, quell'opzione potrebbe non essere disponibile. Considerare in che modo raccogliere informazioni sui dispositivi in produzione. Una possibilità consiste nell'usare un modulo di registrazione che raccoglie le informazioni da altri moduli e le invia al cloud. Un esempio di un modulo di registrazione [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), oppure è possibile progettare il proprio. 

### <a name="place-limits-on-log-size"></a>Place limits on log size

By default the Moby container engine does not set container log size limits. Over time this can lead to the device filling up with logs and running out of disk space. Consider the following options to prevent this:

**Option: Set global limits that apply to all container modules**

You can limit the size of all container logfiles in the container engine log options. The following example sets the log driver to `json-file` (recommended) with limits on size and number of files:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Add (or append) this information to a file named `daemon.json` and place it the right location for your device platform.

| Piattaforma | Località |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

The container engine must be restarted for the changes to take effect.

**Option: Adjust log settings for each container module**

You can do so in the **createOptions** of each module. ad esempio:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

**Additional options on Linux systems**

* Configure the container engine to send logs to `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/) by setting `journald` as the default logging driver. 

* Periodically remove old logs from your device by installing a logrotate tool. Usare la specifica del file seguente: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Prendere in considerazione i test e le pipeline CI/CD

Per uno scenario di distribuzione di IoT Edge più efficiente, provare a integrare la distribuzione di produzione nel test e nelle pipeline CI/CD. Azure IoT Edge supporta più piattaforme di integrazione CI/CD, tra cui Azure DevOps. Per altre informazioni, vedere [Integrazione e distribuzione continue in Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [distribuzione automatica di IoT Edge](module-deployment-monitoring.md).
* Vedere come IoT Edge supporta l'[integrazione e la distribuzione continue](how-to-ci-cd.md).
