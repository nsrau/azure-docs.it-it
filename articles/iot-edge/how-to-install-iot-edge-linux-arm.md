---
title: Come installare Azure IoT Edge in Linux | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Linux su ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062599"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installare il runtime di Azure IoT Edge in Linux (ARM32v7/armhf)

Il runtime di Azure IoT Edge è distribuito in tutti i dispositivi IoT Edge. È costituito da tre componenti. Il **daemon di sicurezza di IoT Edge** fornisce e gestisce gli standard di sicurezza nel dispositivo Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando l'agente di IoT Edge. L'**agente di IoT Edge** semplifica la distribuzione e il monitoraggio di moduli nel dispositivo Edge, tra cui l'hub IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT.

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge in un dispositivo Linux ARM32v7/armhf Edge (ad esempio Raspberry Pi).

>[!NOTE]
>I pacchetti nei repository di software Linux sono soggetti alle condizioni di licenza indicate in ogni pacchetto (/usr/share/doc/*nome-pacchetto*). Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

## <a name="install-the-container-runtime"></a>Installare il runtime per contenitori

Azure IoT Edge si basa su un runtime per contenitori [compatibile con OCI][lnk-oci], ad esempio Docker. Se Docker CE/EE è già installato sul dispositivo Edge, è possibile continuare a usarlo per lo sviluppo e il test con Azure IoT Edge. 

Per gli scenari di produzione è consigliabile usare il motore [Moby-based][lnk-moby] fornito di seguito. È l'unico motore di contenitore supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore docker CE/EE sono completamente compatibili con il runtime di Moby.

I comandi riportati di seguito installano sia il motore moby che l'interfaccia della riga di comando (CLI). L'interfaccia della riga di comando è utile per lo sviluppo, ma è facoltativa per le distribuzioni di produzione.

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di IoT Edge

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurare il daemon di sicurezza di Azure IoT Edge

Il daemon può essere configurato tramite il file di configurazione in `/etc/iotedge/config.yaml`. Il dispositivo Edge può essere configurato <!--[automatically via Device Provisioning Service][lnk-dps] or--> manualmente tramite una [stringa di connessione del dispositivo][lnk-dcs].

Per la configurazione manuale, immettere la stringa di connessione del dispositivo nella sezione **provisioning** di **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*Il file è protetto da scrittura per impostazione predefinita e potrebbe essere necessario usare `sudo` per modificarlo. Ad esempio `sudo nano /etc/iotedge/config.yaml`*

Dopo aver immesso le informazioni di provisioning nella configurazione, riavviare il daemon:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

È possibile verificare lo stato del daemon IoT Edge nel modo seguente:

```cmd/sh
systemctl status iotedge
```

Esaminare i log del daemon tramite:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Ed elencare i moduli in esecuzione con:

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Se si riscontrano problemi durante l'installazione del runtime di Edge, vedere la pagina relativa alla [risoluzione dei problemi][lnk-trouble].

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md