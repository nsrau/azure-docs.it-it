---
title: Installazione di Azure IoT Edge in Linux ARM32 | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Linux su dispositivi ARM32, come Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224702"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installare il runtime di Azure IoT Edge in Linux (ARM32v7/armhf)

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud. 

Per altre informazioni sul funzionamento del runtime di IoT Edge e sui componenti inclusi, vedere [Informazioni sul runtime Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge in un dispositivo IoT Edge ARM32v7/armhf Linux. Ad esempio, questi passaggi funzionerebbe per i dispositivi Raspberry Pi. Per un elenco dei sistemi operativi ARM32 supportati, vedere [Azure IOT Edge sistemi supportati](support.md#operating-systems). 

>[!NOTE]
>I pacchetti disponibili nei repository di software Linux sono soggetti alle condizioni di licenza indicate in ogni pacchetto (/usr/share/doc/*nome-pacchetto*). Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

## <a name="install-the-latest-version"></a>Installare la versione più recente

Usare le sezioni seguenti per installare la versione più recente del servizio Azure IoT Edge sui dispositivi ARM Linux. 

### <a name="install-the-container-runtime"></a>Installare il runtime per contenitori

Azure IoT Edge si basa su un runtime del contenitore [compatibile con OCI](https://www.opencontainers.org/). Per gli scenari di produzione è consigliabile usare il motore [basato su Moby](https://mobyproject.org/) fornito di seguito. È l'unico motore di contenitore supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono compatibili con il runtime basato su Moby.

I comandi seguenti installano sia il motore basato su Moby che l'interfaccia della riga di comando (CLI). L'interfaccia della riga di comando è utile per lo sviluppo, ma è facoltativa per le distribuzioni di produzione.

```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f
```

### <a name="install-the-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di IoT Edge

Il **daemon di sicurezza di IOT Edge** fornisce e gestisce gli standard di sicurezza sul dispositivo IOT Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

Una volta completata l'installazione di IoT Edge, l'output richiederà di aggiornare il file di configurazione. Seguire i passaggi nella sezione [configurare il daemon di sicurezza Azure IOT Edge](#configure-the-azure-iot-edge-security-daemon) per completare il provisioning del dispositivo. 

## <a name="install-a-specific-version"></a>Installare una versione specifica

Se si vuole installare una versione specifica di Azure IoT Edge, è possibile indirizzare i file dei componenti direttamente dal repository di GitHub IoT Edge. Usare gli stessi `curl` comandi elencati nelle sezioni precedenti per ottenere tutti i componenti IOT Edge nel dispositivo: il motore di Moby e l'interfaccia della riga di comando, il libiothsm e infine il daemon di sicurezza IoT Edge. L'unica differenza è che si sostituiscono gli URL **aka.ms** con i collegamenti che puntano direttamente alla versione di ogni componente che si desidera utilizzare.

Passare alla [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases)e individuare la versione di rilascio di destinazione. Espandere la sezione **Asset** per la versione e scegliere i file che corrispondono all'architettura del dispositivo IOT Edge. Ogni IoT Edge versione contiene i file **iotedge** e **libiothsm** . Non tutte le versioni includono **Moby-Engine** o **Moby-CLI**. Se non è ancora stato installato il motore di contenitori di Moby, esaminare le versioni precedenti fino a quando non ne viene trovato uno che include i componenti di Moby. 

Una volta completata l'installazione di IoT Edge, l'output richiederà di aggiornare il file di configurazione. Seguire i passaggi della sezione successiva per completare il provisioning del dispositivo. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurare il daemon di sicurezza di Azure IoT Edge

Configurare il runtime di IoT Edge per collegare il dispositivo fisico con un'identità del dispositivo esistente in un hub IoT di Azure. 

Il daemon può essere configurato usando il file di configurazione in `/etc/iotedge/config.yaml`. Per impostazione predefinita, il file è protetto da scrittura, pertanto potrebbe essere necessario disporre di autorizzazioni elevate per modificarlo.

È possibile effettuare il provisioning di un singolo dispositivo IoT Edge manualmente usando una stringa di connessione dispositivo fornita dall'hub IoT. In alternativa, è possibile usare il servizio di provisioning di dispositivi per effettuare il provisioning di dispositivi automaticamente. Ciò è utile quando si dispone di molti dispositivi di cui effettuare il provisioning. A seconda del provisioning, scegliere lo script di installazione appropriato. 

### <a name="option-1-manual-provisioning"></a>Opzione 1: provisioning manuale

Per eseguire manualmente il provisioning di un dispositivo, è necessario specificare una [stringa di connessione del dispositivo](how-to-register-device-portal.md) che è possibile creare registrando un nuovo dispositivo IOT Edge nell'hub Internet.

Aprire il file di configurazione. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Trovare le configurazioni del provisioning del file e rimuovere il commento dalla sezione di configurazione del provisioning **manuale** . Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge. Assicurarsi che tutte le altre sezioni di provisioning siano impostate come commento.

```yaml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# DPS TPM provisioning configuration
# provisioning:
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   attestation:
#     method: "tpm"
#     registration_id: "{registration_id}"
```

Salvare e chiudere il file. 

`CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opzione 2: provisioning automatico

Per eseguire il provisioning automatico di un dispositivo, [configurare il servizio Device Provisioning e recuperare l'ID di registrazione del dispositivo](how-to-auto-provision-simulated-device-linux.md). Esistono diversi meccanismi di attestazione supportati da IoT Edge quando si usa il provisioning automatico, ma anche i requisiti hardware influiscano sulle scelte effettuate. I dispositivi Raspberry Pi, ad esempio, non sono dotati di un chip Trusted Platform Module (TPM) per impostazione predefinita.

Aprire il file di configurazione. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Trovare le configurazioni del provisioning del file e rimuovere il commento dalla sezione appropriata per il meccanismo di attestazione. Quando si usa l'attestazione TPM, ad esempio, aggiornare i valori di **scope_id** e **registration_id** con i valori del servizio Device provisioning in hub Internet e del dispositivo di IOT Edge con TPM, rispettivamente.

```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
```

Salvare e chiudere il file. 

`CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se è stata usata la **configurazione manuale** nella sezione precedente, il runtime IoT Edge deve essere correttamente sottoposto a provisioning e in esecuzione nel dispositivo. In alternativa, se è stata usata la procedura di **configurazione automatica** , è necessario completare alcuni passaggi aggiuntivi, in modo che il runtime possa registrare il dispositivo con l'hub Internet per conto dell'utente. Per i passaggi successivi, vedere [creare ed effettuare il provisioning di un TPM simulato IOT Edge dispositivo in una macchina virtuale Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

È possibile verificare lo stato del daemon IoT Edge con il comando seguente:

```bash
systemctl status iotedge
```

Esaminare i log del daemon con il comando seguente:

```bash
journalctl -u iotedge --no-pager --no-full
```

Elencare infine i moduli in esecuzione con il comando seguente:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Suggerimenti

Per eseguire comandi `iotedge` sono necessari privilegi elevati. Dopo aver installato il runtime, scollegarsi e ricollegarsi al computer per aggiornare automaticamente le autorizzazioni. Fino ad allora, usare **sudo** davanti ai comandi `iotedge`.

Nei dispositivi con risorse vincolate si consiglia vivamente di impostare la variabile di ambiente *OptimizeForPerformance* su *false*, come indicato nelle istruzioni della [Guida alla risoluzione dei problemi](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Se la rete è dotata di un server proxy, seguire i passaggi descritti in [Configurare il dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Disinstallazione di IoT Edge

Per rimuovere IoT Edge da un dispositivo Linux, usare i comandi seguenti dalla riga di comando. 

Rimuovere il runtime IoT Edge. 

```bash
sudo apt-get remove --purge iotedge
```

Quando il runtime IoT Edge viene rimosso, i contenitori creati vengono arrestati, ma rimangono sul dispositivo. Verificare tutti i contenitori per vedere quali vengono conservati. 

```bash
sudo docker ps -a
```

Eliminare i contenitori dal dispositivo, inclusi i due contenitori di runtime. 

```bash
sudo docker rm -f <container name>
```

Infine, rimuovere il runtime del contenitore dal dispositivo. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

Se si verificano problemi con il runtime di IoT Edge installato correttamente, vedere la pagina relativa alla [risoluzione dei](troubleshoot.md#stability-issues-on-resource-constrained-devices) problemi.

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
