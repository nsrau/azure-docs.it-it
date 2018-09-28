---
title: Come installare Azure IoT Edge in Linux | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: f515ddc58a9f9b434e40f44ca7cc50d738ed69f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993118"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installare il runtime di Azure IoT Edge in Linux (x64)

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud. 

Per altre informazioni sul funzionamento del runtime di IoT Edge e sui componenti inclusi, vedere [Informazioni sul runtime Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo descrive i passaggi da eseguire per installare il runtime di Azure IoT Edge in un dispositivo Edge Linux x64 (Intel o AMD). Fare riferimento al [supporto di Azure IoT Edge](support.md#operating-systems) per un elenco dei sistemi operativi AMD64 attualmente supportati. 

>[!NOTE]
>I pacchetti disponibili nei repository di software Linux sono soggetti alle condizioni di licenza indicate in ogni pacchetto (/usr/share/doc/*nome-pacchetto*). Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Registrare la chiave e il feed del repository software Microsoft

A seconda del sistema operativo, scegliere gli script appropriati per preparare il dispositivo per l'installazione del runtime IoT Edge. 

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>Installare il runtime per contenitori 

Azure IoT Edge si basa su un runtime per contenitori [compatibile con OCI][lnk-oci]. Per gli scenari di produzione è consigliabile usare il motore [basato su Moby][lnk-moby] fornito di seguito. È l'unico motore di contenitore supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono compatibili con il runtime di Moby.

Aggiornare apt-get.

```bash
sudo apt-get update
```

Installare il motore Moby. 

```bash
sudo apt-get install moby-engine
```

Installare l'interfaccia della riga di comando di Moby. L'interfaccia della riga di comando è utile per lo sviluppo, ma è facoltativa per le distribuzioni di produzione.

```bash
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di Azure IoT Edge

Il **daemon di sicurezza di IoT Edge** fornisce e gestisce gli standard di sicurezza nel dispositivo Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge. 

Il comando di installazione installa anche la versione standard di **iothsmlib** se non già presente.

Aggiornare apt-get.

```bash
sudo apt-get update
```

Installare il daemon di sicurezza. Il pacchetto è installato in `/etc/iotedge/`.

```bash
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurare il daemon di sicurezza di Azure IoT Edge

Configurare il runtime di IoT Edge per collegare il dispositivo fisico con un'identità del dispositivo esistente in un hub IoT di Azure. 

Il daemon può essere configurato usando il file di configurazione in `/etc/iotedge/config.yaml`. Il file è protetto da scrittura per impostazione predefinita e possono essere necessarie autorizzazioni elevate per modificarlo.

È possibile effettuare il provisioning di un singolo dispositivo IoT Edge manualmente usando una stringa di connessione dispositivo fornita dall'hub IoT. In alternativa, è possibile usare il servizio di provisioning di dispositivi per effettuare il provisioning di dispositivi automaticamente. Ciò è utile quando si dispone di molti dispositivi di cui effettuare il provisioning. A seconda del provisioning, scegliere lo script di installazione appropriato. 

### <a name="option-1-manual-provisioning"></a>Opzione 1: Provisioning manuale

Per eseguire manualmente il provisioning di un dispositivo, è necessario fornire una [stringa di connessione del dispositivo][lnk-dcs] che è possibile creare tramite la registrazione di un nuovo dispositivo nell'hub IoT.


Aprire il file di configurazione. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Individuare la sezione di provisioning del file e rimuovere il commento relativo alla modalità di provisioning **manuale**. Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

Salvare e chiudere il file. 

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opzione 2: Provisioning automatico

Per eseguire il provisioning automatico di un dispositivo, [configurare il servizio di provisioning di dispositivi e recuperare l'ID di registrazione del dispositivo][lnk-dps] (DPS). Il provisioning automatico funziona solo con i dispositivi che dispongono di un chip Trusted Platform Module (TPM). Ad esempio, i dispositivi Raspberry Pi non sono dotati di TPM per impostazione predefinita. 

Aprire il file di configurazione. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Individuare la sezione di provisioning del file e rimuovere il commento relativo alla modalità di provisioning **dps**. Aggiornare i valori di **scope_id** e **registration_id** con i valori del servizio Device Provisioning in hub IoT e aggiornare il dispositivo IoT Edge con TPM. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Salvare e chiudere il file. 

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se è stata usata la **configurazione manuale** nella sezione precedente, il runtime IoT Edge deve essere correttamente sottoposto a provisioning e in esecuzione nel dispositivo. Se è stata usata la **configurazione automatica**, è necessario completare alcuni passaggi aggiuntivi in modo che il runtime possa registrare il dispositivo con l'hub IoT per conto dell'utente. Per i passaggi successivi, vedere [Creare ed effettuare il provisioning di un dispositivo simulato TPM Edge in una macchina virtuale Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

Nei dispositivi con risorse vincolate, si consiglia vivamente di impostare la variabile di ambiente *OptimizeForPerformance* su *false*, come indicato nelle istruzioni della [Guida alla risoluzione dei problemi][lnk-trouble].

Se la rete è dotata di un server proxy, seguire i passaggi descritti in [Configurare il dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi durante l'installazione del runtime di Edge, vedere la pagina relativa alla [risoluzione dei problemi][lnk-trouble].

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
