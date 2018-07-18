---
title: Come installare Azure IoT Edge in Linux | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 43f82341a3cc9d2163afd35e42864aaa7866b1b2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035170"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installare il runtime di Azure IoT Edge in Linux (x64)

Il runtime di Azure IoT Edge è distribuito in tutti i dispositivi IoT Edge. È costituito da tre componenti. Il **daemon di sicurezza di IoT Edge** fornisce e gestisce gli standard di sicurezza nel dispositivo Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando l'agente di IoT Edge. L'**agente di IoT Edge** semplifica la distribuzione e il monitoraggio di moduli nel dispositivo Edge, tra cui l'hub di IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT.

Questo articolo descrive i passaggi da eseguire per installare il runtime di Azure IoT Edge in un dispositivo Edge Linux x64 (Intel o AMD).

>[!NOTE]
>I pacchetti disponibili nei repository di software Linux sono soggetti alle condizioni di licenza indicate in ogni pacchetto (/usr/share/doc/*nome-pacchetto*). Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Registrare la chiave e il feed del repository software Microsoft

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>Installare il runtime per contenitori 

Azure IoT Edge si basa su un runtime per contenitori [compatibile con OCI][lnk-oci], ad esempio Docker. Se Docker CE/EE è già installato nel dispositivo Edge, è possibile continuare a usarlo per le attività di sviluppo e test con Azure IoT Edge. 

Per gli scenari di produzione è consigliabile usare il motore [basato su Moby][lnk-moby] fornito di seguito. È l'unico motore di contenitore supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono completamente compatibili con il runtime di Moby.

*Le istruzioni riportate di seguito installano sia il motore Moby sia l'interfaccia della riga di comando. L'interfaccia della riga di comando è utile per le attività di sviluppo, ma è facoltativa per le distribuzioni di produzione.*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di Azure IoT Edge

Con i comandi riportati di seguito verrà installata anche la versione standard di **iothsmlib** se non già presente.

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurare il daemon di sicurezza di Azure IoT Edge

Il daemon può essere configurato tramite il file di configurazione in `/etc/iotedge/config.yaml`. Il dispositivo Edge può essere configurato <!--[automatically via Device Provisioning Service][lnk-dps] or--> manualmente tramite una [stringa di connessione del dispositivo][lnk-dcs].

Per la configurazione manuale, immettere la stringa di connessione del dispositivo nella sezione **provisioning** di **config.yaml**.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*Il file è protetto da scrittura per impostazione predefinita e potrebbe essere necessario usare `sudo` per modificarlo. Ad esempio: `sudo nano /etc/iotedge/config.yaml`*

Dopo aver immesso le informazioni di provisioning nella configurazione, riavviare il daemon:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

È possibile verificare lo stato del daemon IoT Edge con il comando seguente:

```cmd/sh
systemctl status iotedge
```

Esaminare i log del daemon con il comando seguente:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Elencare infine i moduli in esecuzione con il comando seguente:

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi durante l'installazione del runtime di Edge, vedere la pagina relativa alla [risoluzione dei problemi][lnk-trouble].

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
