---
title: Installare Azure IoT Edge in Windows | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.openlocfilehash: 401e988e41a25a999c047bf93b6794388d38461e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456798"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installare il runtime di Azure IoT Edge in Windows

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud. 

Per altre informazioni sul funzionamento del runtime di IoT Edge, vedere [Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura](iot-edge-runtime.md).

This article lists the steps to install the Azure IoT Edge runtime on your Windows x64 (AMD/Intel) system using Windows containers.

> [!NOTE]
> A known Windows operating system issue prevents transition to sleep and hibernate power states when IoT Edge modules (process-isolated Windows Nano Server containers) are running. This issue impacts battery life on the device.
>
> As a workaround, use the command `Stop-Service iotedge` to stop any running IoT Edge modules before using these power states. 

Una configurazione con contenitori Linux su sistemi Windows non è una configurazione di produzione consigliata o supportata per Azure IoT Edge. È tuttavia possibile usarla a scopo di sviluppo e test. To learn more, see [Use IoT Edge on Windows to run Linux containers](how-to-install-iot-edge-windows-with-linux.md).

For information about what's included in the latest version of IoT Edge, see [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Prerequisiti

Usare questa sezione per verificare se il dispositivo Windows in uso può supportare IoT Edge e per prepararlo per un motore del contenitore prima dell'installazione. 

### <a name="supported-windows-versions"></a>Versioni supportate di Windows

For development and test scenarios, Azure IoT Edge with Windows containers can be installed on any version of Windows 10 or Windows Server 2019 (build 17763) that supports the containers feature. For information about which operating systems are currently supported for production scenarios, see [Azure IoT Edge supported systems](support.md#operating-systems). 

IoT Core devices must include the IoT Core- Windows Containers optional feature to support the IoT Edge runtime. Use the following command in a [remote PowerShell session](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) to check that Windows containers are supported on your device: 

```powershell
Get-Service vmcompute
```

If the service is present, you should get a successful response with the service status listed as **running**. If the vmcompute service is not found, then your device does not meet the requirements for IoT Edge. Contact your hardware provider to ask about support for this feature. 

### <a name="prepare-for-a-container-engine"></a>Preparare il dispositivo per un motore del contenitore 

Azure IoT Edge si basa su un motore del contenitore [compatibile con OCI](https://www.opencontainers.org/). Per gli scenari di produzione, usare il motore Moby incluso nello script di installazione per eseguire contenitori Windows nel dispositivo Windows in uso. 

## <a name="install-iot-edge-on-a-new-device"></a>Installare IoT Edge in un nuovo dispositivo

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

Uno script di PowerShell scarica e installa il daemon di sicurezza di Azure IoT Edge. Il daemon di sicurezza avvia quindi il primo di due moduli di runtime, l'agente IoT Edge, che consente distribuzioni remote di altri moduli. 

>[!TIP]
>For IoT Core devices, we recommend running the installation commands using a RemotePowerShell session. For more information, see [Using PowerShell for Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Quando si installa il runtime di IoT Edge per la prima volta in un dispositivo, è necessario effettuare il provisioning del dispositivo con un'identità da un hub IoT. A single IoT Edge device can be provisioned manually using a device connection string provided by IoT Hub. Or, you can use the Device Provisioning Service (DPS) to automatically provision devices, which is helpful when you have many devices to set up. A seconda del provisioning, scegliere lo script di installazione appropriato. 

Le sezioni seguenti descrivono i casi d'uso comuni e i parametri per lo script di installazione di IoT Edge in un nuovo dispositivo. 

### <a name="option-1-install-and-manually-provision"></a>Opzione 1: eseguire l'installazione e ed eseguire l'installazione ed effettuare manualmente il provisioning

In this first option, you provide a **device connection string** generated by IoT Hub to provision the device. 

This example demonstrates a manual installation with Windows containers:

1. If you haven't already, register a new IoT Edge device and retrieve the **device connection string**. Copy the connection string to use later in this section. You can complete this step using the following tools:

   * [Azure portal](how-to-register-device.md#register-in-the-azure-portal)
   * [interfaccia della riga di comando di Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Eseguire PowerShell come amministratore.

   >[!NOTE]
   >Usare una sessione AMD64 di PowerShell per installare IoT Edge, non PowerShell (x86). Se non si conosce il tipo di sessione in uso, eseguire questo comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. The **Deploy-IoTEdge** command checks that your Windows machine is on a supported version, turns on the containers feature, and then downloads the moby runtime and the IoT Edge runtime. The command defaults to using Windows containers. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. At this point, IoT Core devices may restart automatically. Other Windows 10 or Windows Server devices may prompt you to restart. If so, restart your device now. Once your device is ready, run PowerShell as an administrator again.

5. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. When prompted, provide the device connection string that you retrieved in step 1. The device connection string associates the physical device with a device ID in IoT Hub. 

   The device connection string takes the following format, and should not include quotation marks: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Use the steps in [Verify successful installation](#verify-successful-installation) to check the status of IoT Edge on your device. 

Quando si installa e si effettua il provisioning di un dispositivo manualmente, è possibile usare ulteriori parametri per modificare l'installazione tra cui:

* Indirizzare il traffico attraverso un server proxy
* Fare in modo che il programma di installazione punti a una directory offline
* Dichiarare un'immagine del contenitore di un agente specifico e fornire le credenziali se si trova in un registro privato

For more information about these installation options, skip ahead to learn about [all installation parameters](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opzione 2: installare ed effettuare automaticamente il provisioning

In questa seconda opzione viene effettuato il provisioning del dispositivo con il servizio Device Provisioning in hub IoT. Provide the **Scope ID** from a Device Provisioning Service instance along with any other information specific to your preferred [attestation mechanism](../iot-dps/concepts-security.md#attestation-mechanism):

* [Create and provision a simulated IoT Edge device with a virtual TPM on Windows](how-to-auto-provision-simulated-device-windows.md)
* [Create and provision an IoT Edge device using symmetric key attestation](how-to-auto-provision-symmetric-keys.md)

When you install and provision a device automatically, you can use additional parameters to modify the installation including:

* Indirizzare il traffico attraverso un server proxy
* Fare in modo che il programma di installazione punti a una directory offline
* Dichiarare un'immagine del contenitore di un agente specifico e fornire le credenziali se si trova in un registro privato

Per altre informazioni su queste opzioni di installazione, continuare a leggere questo articolo oppure passare direttamente a [Tutti i parametri di installazione](#all-installation-parameters).

## <a name="offline-installation"></a>Installazione offline

During installation two files are downloaded:

* Microsoft Azure IoT Edge cab, which contains the IoT Edge security daemon (iotedged), Moby container engine, and Moby CLI.
* MSI di Visual C++ Redistributable Package (runtime di VC)

You can download one or both of these files ahead of time to the device, then point the installation script at the directory that contains the files. Il programma di installazione verifica innanzitutto il contenuto della directory e quindi scarica solo i componenti che non sono già presenti. If all the files are available offline, you can install with no internet connection. You can also use this feature to install a specific version of the components.  

For the latest IoT Edge installation files along with previous versions, see [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

To install with offline components, use the `-OfflineInstallationPath` parameter as part of the Deploy-IoTEdge command and provide the absolute path to the file directory. Ad esempio,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

You can also use the offline installation path parameter with the Update-IoTEdge command, introduced later in this article. 

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Verificare lo stato del servizio IoT Edge. It should be listed as running.  

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti. If you just finished installing the IoT Edge runtime, you may see a list of errors from the time between running **Deploy-IoTEdge** and **Initialize-IoTEdge**. These errors are expected, as the service is trying to start before being configured. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Run an automated check for the most common configuration and networking errors.

```powershell
iotedge check
```

Elencare i moduli in esecuzione. After a new installation, the only module you should see running is **edgeAgent**. After you [deploy IoT Edge modules](how-to-deploy-modules-portal.md) for the first time, the other system module, **edgeHub**, will start on the device too. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Manage module containers

The IoT Edge service requires a container engine running on your device. When you deploy a module to a device, the IoT Edge runtime uses the container engine to pull the container image from a registry in the cloud. The IoT Edge service enables you to interact with your modules and retrieve logs, but sometimes you may want to use the container engine to interact with the container itself. 

For more information about module concepts, see [Understand Azure IoT Edge modules](iot-edge-modules.md). 

If you're running Windows containers on your Windows IoT Edge device, then the IoT Edge installation included the Moby container engine. The Moby engine was based on the same standards as Docker, and was designed to run in parallel on the same machine as Docker Desktop. For that reason, if you want to target containers managed by the Moby engine, you have to specifically target that engine instead of Docker. 

For example, to list all Docker images, use the following command:

```powershell
docker images
```

To list all Moby images, modify the same command with a pointer to the Moby engine: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

The engine URI is listed in the output of the installation script, or you can find it in the container runtime settings section for the config.yaml file. 

![moby_runtime uri in config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

For more information about commands you can use to interact with containers and images running on your device, see [Docker command-line interfaces](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Aggiornare un'installazione esistente

If you've already installed the IoT Edge runtime on a device before and provisioned it with an identity from IoT Hub, then you can update the runtime without having to reenter your device information. 

Per altre informazioni, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Questo esempio mostra un'installazione che punta a un file di configurazione esistente e usa i contenitori Windows: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

When you update IoT Edge, you can use additional parameters to modify the update, including:

* Direct traffic to go through a proxy server, or
* Fare in modo che il programma di installazione punti a una directory offline 
* Restarting without a prompt if necessary

You can't declare an IoT Edge agent container image with script parameters because that information is already set in the configuration file from the previous installation. Se si vuole modificare l'immagine del contenitore dell'agente, eseguire questa operazione nel file config.yaml. 

For more information about these update options, use the command `Get-Help Update-IoTEdge -full` or refer to [all installation parameters](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Per rimuovere l'installazione di IoT Edge da un dispositivo Windows, usare il comando seguente da una finestra amministrativa di PowerShell. Questo comando rimuove il runtime di IoT Edge, insieme alla configurazione esistente e ai dati del motore Moby. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

The Uninstall-IoTEdge command does not work on Windows IoT Core. To remove IoT Edge from Windows IoT Core devices, you need to redeploy your Windows IoT Core image. 

For more information about uninstallation options, use the command `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Tutti i parametri di installazione

Nelle sezioni precedenti sono stati introdotti alcuni scenari di installazione comuni con esempi di come usare i parametri per modificare lo script di installazione. This section provides reference tables of the common parameters used to install, update, or uninstall IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

The Deploy-IoTEdge command downloads and deploys the IoT Edge Security Daemon and its dependencies. The deployment command accepts these common parameters, among others. For the full list, use the command `Get-Help Deploy-IoTEdge -full`.  

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | If no container operating system is specified, Windows is the default value.<br><br>For Windows containers, IoT Edge uses the moby container engine included in the installation. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | If this parameter is included, the installer will check the listed directory for the IoT Edge cab and VC Runtime MSI files required for installation. Any files not found in the directory are downloaded. If both files are in the directory, you can install IoT Edge without an internet connection. You can also use this parameter to use a specific version. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | None | This flag allows the deployment script to restart the machine without prompting, if necessary. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

The Initialize-IoTEdge command configures IoT Edge with your device connection string and operational details. Much of the information generated by this command is then stored in the iotedge\config.yaml file. The initialization command accepts these common parameters, among others. For the full list, use the command `Get-Help Initialize-IoTEdge -full`. 

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Manuale** | Nessuno | **Parametro opzionale**. If no provisioning type is specified, manual is the default value.<br><br>Dichiara che si fornirà una stringa di connessione del dispositivo per effettuare il provisioning del dispositivo manualmente. |
| **Dps** | Nessuno | **Parametro opzionale**. If no provisioning type is specified, manual is the default value.<br><br>Dichiara che si forniranno un ID ambito del servizio Device Provisioning (DPS) e l'ID registrazione del dispositivo per effettuare il provisioning tramite DPS.  |
| **DeviceConnectionString** | Una stringa di connessione da un dispositivo IoT Edge registrato in un hub IoT, tra virgolette singole | **Obbligatorio** per l'installazione manuale. Se non si specifica una stringa di connessione nei parametri di script, ne verrà richiesta una durante l'installazione. |
| **ScopeId** | Un ID ambito da un'istanza del servizio Device Provisioning associata all'hub IoT. | **Obbligatorio** per l'installazione di DPS. Se non si specifica un ID ambito nei parametri di script, ne verrà richiesto uno durante l'installazione. |
| **RegistrationId** | Un ID registrazione generato dal dispositivo | **Required** for DPS installation if using TPM or symmetric key attestation. |
| **SymmetricKey** | The symmetric key used to provision the IoT Edge device identity when using DPS | **Required** for DPS installation if using symmetric key attestation. |
| **ContainerOs** | **Windows** o **Linux** | If no container operating system is specified, Windows is the default value.<br><br>For Windows containers, IoT Edge uses the moby container engine included in the installation. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI dell'immagine dell'agente IoT Edge | Per impostazione predefinita, una nuova installazione di IoT Edge usa il tag di versioni più recente per l'immagine dell'agente IoT Edge. Usare questo parametro per impostare un tag specifico per la versione dell'immagine o per fornire la propria immagine dell'agente. Per altre informazioni, vedere [Informazioni sui tag di IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome utente** | Nome utente del registro contenitori | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare un nome utente con accesso al registro. |
| **Password** | Stringa della password di protezione | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare la password per accedere al registro. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | If no container OS is specified, Windows is the default value. Per i contenitori Windows, nell'installazione verrà incluso un motore del contenitore. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | If this parameter is included, the installer will check the listed directory for the IoT Edge cab and VC Runtime MSI files required for installation. Any files not found in the directory are downloaded. If both files are in the directory, you can install IoT Edge without an internet connection. You can also use this parameter to use a specific version. |
| **RestartIfNeeded** | None | This flag allows the deployment script to restart the machine without prompting, if necessary. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Force** | None | This flag forces the uninstallation in case the previous attempt to uninstall was unsuccessful. 
| **RestartIfNeeded** | None | This flag allows the uninstall script to restart the machine without prompting, if necessary. |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

In caso di problemi che impediscono di installare correttamente IoT Edge, vedere la pagina relativa alla [risoluzione dei problemi](troubleshoot.md).

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
