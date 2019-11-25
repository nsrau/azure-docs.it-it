---
title: Installare Azure IoT Edge in Linux | Microsoft Docs
description: Azure IoT Edge installation instructions on Linux devices running Ubuntu or Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: ec463efb1282c311757bb90fd614e1247459c80f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457336"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Install the Azure IoT Edge runtime on Debian-based Linux systems

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud. To learn more, see [Understand the Azure IoT Edge runtime and its architecture](iot-edge-runtime.md).

This article lists the steps to install the Azure IoT Edge runtime on an X64, ARM32, or ARM64 Linux device. Installation packages are provided for Ubuntu Server 16.04, Ubuntu Server 18.04, and Raspbian Stretch. Refer to [Azure IoT Edge supported systems](support.md#operating-systems) for a list of supported Linux operating systems and architectures.

>[!NOTE]
>Support for ARM64 devices is in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> I pacchetti nei repository di software Linux sono soggetti alle condizioni di licenza indicate in ogni pacchetto (/usr/share/doc/*nome-pacchetto*). Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

## <a name="install-the-latest-runtime-version"></a>Install the latest runtime version

Use the following sections to install the most recent version of the Azure IoT Edge runtime onto your device. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrare la chiave e il feed del repository software Microsoft

Prepare your device for the IoT Edge runtime installation.

Install the repository configuration. Choose the **16.04** or **18.04** command that matches your device operating system:

* **Ubuntu Server 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:
   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copy the generated list.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Install Microsoft GPG public key

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installare il runtime per contenitori

Azure IoT Edge si basa su un runtime del contenitore [compatibile con OCI](https://www.opencontainers.org/). For production scenarios, we recommended that you use the [Moby-based](https://mobyproject.org/) engine provided below. È l'unico motore di contenitore supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono compatibili con il runtime di Moby.

Perform apt update.

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

If you encounter errors when installing the Moby container runtime, follow the steps to [Verify your Linux kernel for Moby compatibility](#verify-your-linux-kernel-for-moby-compatibility), provided later in this article. 

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di Azure IoT Edge

The **IoT Edge security daemon** provides and maintains security standards on the IoT Edge device. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge.

The installation command also installs the standard version of the **libiothsm** if not already present.

Perform apt update.

   ```bash
   sudo apt-get update
   ```

Installare il daemon di sicurezza. Il pacchetto è installato in `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Once IoT Edge is successfully installed, the output will prompt you to update the configuration file. Follow the steps in the [Configure the Azure IoT Edge security daemon](#configure-the-security-daemon) section to finish provisioning your device. 

## <a name="install-a-specific-runtime-version"></a>Install a specific runtime version

If you want to install a specific version of the Azure IoT Edge runtime, you can target the component files directly from the IoT Edge GitHub repository. Use the following steps to get all of the IoT Edge components onto your device: the Moby engine and CLI, the libiothsm, and finally the IoT Edge security daemon.

1. Navigate to the [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases), and find the release version that you want to target. 

2. Expand the **Assets** section for that version.

3. There may or may not be updates to the Moby engine in any given release. If you see files that start with **moby-engine** and **moby-cli**, use the following commands to update those components. If you don't see any Moby files, go back through the older release assets until you find the most recent version. 

   1. Find the **moby-engine** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address.

   2. Use the copied link in the following command to install that version of the Moby engine: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Find the **moby-cli** file that matches your IoT Edge device's architecture. The Moby CLI is an optional component, but can be helpful during development. Right-click on the file link and copy the link address. 

   4. Use the copied link in the following command to install that version of the Moby CLI: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Every release should have new files for the IoT Edge security daemon and the hsmlib. Use the following commands to update those components. 

   1. Find the **libiothsm-std** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address. 

   2. Use the copied link in the following command to install that version of the hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Find the **iotedge** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address. 

   4. Use the copied link in the following command to install that version of the IoT Edge security daemon. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Once IoT Edge is successfully installed, the output will prompt you to update the configuration file. Follow the steps in the next section to finish provisioning your device. 

## <a name="configure-the-security-daemon"></a>Configure the security daemon

Configurare il runtime IoT Edge per collegare il dispositivo fisico con un'identità del dispositivo esistente in un hub IoT di Azure.

Il daemon può essere configurato usando il file di configurazione in `/etc/iotedge/config.yaml`. Il file è protetto da scrittura per impostazione predefinita e possono essere necessarie autorizzazioni elevate per modificarlo.

È possibile effettuare il provisioning di un singolo dispositivo IoT Edge manualmente usando una stringa di connessione dispositivo fornita dall'hub IoT. In alternativa, è possibile usare il servizio di provisioning di dispositivi per effettuare il provisioning di dispositivi automaticamente. Ciò è utile quando si dispone di molti dispositivi di cui effettuare il provisioning. A seconda del provisioning, scegliere lo script di installazione appropriato.

### <a name="option-1-manual-provisioning"></a>Opzione 1: Provisioning manuale

Per eseguire manualmente il provisioning di un dispositivo, è necessario fornire una [stringa di connessione del dispositivo](how-to-register-device.md#register-in-the-azure-portal) che è possibile creare tramite la registrazione di un nuovo dispositivo nell'hub IoT.

Aprire il file di configurazione.

```bash
sudo nano /etc/iotedge/config.yaml
```

Find the provisioning configurations of the file and uncomment the **Manual provisioning configuration** section. Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge. Make sure any other provisioning sections are commented out.

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
To paste clipboard contents into Nano `Shift+Right Click` or press `Shift+Insert`.

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opzione 2: Provisioning automatico

Per eseguire il provisioning automatico di un dispositivo, [configurare il servizio Device Provisioning e recuperare l'ID di registrazione del dispositivo](how-to-auto-provision-simulated-device-linux.md). There are a number of attestation mechanisms supported by IoT Edge when using automatic provisioning but your hardware requirements also impact your choices. For example, Raspberry Pi devices do not come with a Trusted Platform Module (TPM) chip by default.

Aprire il file di configurazione.

```bash
sudo nano /etc/iotedge/config.yaml
```

Find the provisioning configurations of the file and uncomment the section appropriate for your attestation mechanism. When using TPM attestation, for example, update the values of **scope_id** and **registration_id** with the values from your IoT Hub Device Provisioning service and your IoT Edge device with TPM, respectively.

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

To paste clipboard contents into Nano `Shift+Right Click` or press `Shift+Insert`.

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se è stata usata la **configurazione manuale** nella sezione precedente, il runtime IoT Edge deve essere correttamente sottoposto a provisioning e in esecuzione nel dispositivo. Se è stata usata la **configurazione automatica**, è necessario completare alcuni passaggi aggiuntivi in modo che il runtime possa registrare il dispositivo con l'hub IoT per conto dell'utente. For next steps, see [Create and provision a simulated TPM IoT Edge device on a Linux virtual machine](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

You can check the status of the IoT Edge Daemon:

```bash
systemctl status iotedge
```

Examine daemon logs:

```bash
journalctl -u iotedge --no-pager --no-full
```

Run an automated check for the most common configuration and networking errors: 

```bash
sudo iotedge check
```

And, list running modules:

```bash
sudo iotedge list
```

After installing IoT Edge on your device, the only module you should see running is **edgeAgent**. Once you create your first deployment, the other system module **$edgeHub** will start on the device as well. For more information, see [deploy IoT Edge modules](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Suggerimenti e risoluzione dei problemi

Per eseguire comandi `iotedge` sono necessari privilegi elevati. Dopo aver installato il runtime, scollegarsi e ricollegarsi al computer per aggiornare automaticamente le autorizzazioni. Fino ad allora, usare **sudo** davanti ai comandi `iotedge`.

Nei dispositivi con risorse vincolate si consiglia vivamente di impostare la variabile di ambiente *OptimizeForPerformance* su *false*, come indicato nelle istruzioni della [Guida alla risoluzione dei problemi](troubleshoot.md).

Se la rete è dotata di un server proxy, seguire i passaggi descritti in [Configurare il dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verify your Linux kernel for Moby compatibility

Many embedded device manufacturers ship device images that contain custom Linux kernels without the features required for container runtime compatibility. If you encounter issues while installing the recommended Moby container runtime, you may be able to troubleshoot your Linux kernel configuration using the [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) script from the official [Moby GitHub repository](https://github.com/moby/moby). Run the following commands on the device to check your kernel configuration:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

This will provide a detailed output that contains the status of kernel features that are used by the Moby runtime. You will want to ensure that all items under `Generally Necessary` and  `Network Drivers` are enabled to ensure that your kernel is fully compatible with the Moby runtime.  If you have identified any missing features, enable them by rebuilding your kernel from source and selecting the associated modules for inclusion in the appropriate kernel .config.  Similarly, if you are using a kernel configuration generator like defconfig or menuconfig, find and enable the respective features and rebuild your kernel accordingly.  Once you have deployed your newly modified kernel, run the check-config script again to verify that all the required features were successfully enabled.


## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

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

If you are having problems with the IoT Edge runtime installing properly, check out the [troubleshooting](troubleshoot.md) page.

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
