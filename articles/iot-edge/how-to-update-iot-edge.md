---
title: Versione di aggiornamento IoT Edge nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Come aggiornare i dispositivi IoT Edge per eseguire le versioni più recenti del daemon di sicurezza e del runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58171d2fc8e7e1563bd83689d1cd91c55fea239a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456539"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aggiornare il daemon di sicurezza e il runtime di IoT Edge

As the IoT Edge service releases new versions, you'll want to update your IoT Edge devices for the latest features and security improvements. Questo articolo fornisce informazioni su come aggiornare i dispositivi IoT Edge quando è disponibile una nuova versione. 

Se si vuole passare a una versione più recente, devono essere aggiornati due componenti in un dispositivo IoT Edge. Il primo è il daemon di sicurezza, che viene eseguito nel dispositivo e avvia i moduli runtime quando il dispositivo viene avviato. Attualmente, il daemon di sicurezza può essere aggiornato solo dal dispositivo stesso. Il secondo componente è il runtime, costituito dai moduli dell'agente IoT Edge e dell'hub di IoT Edge. A seconda del modo in cui si struttura la distribuzione, il runtime può essere aggiornato dal dispositivo o in modalità remota. 

Per trovare la versione più recente di Azure IoT Edge, fare riferimento alle [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aggiornare il daemon di sicurezza

Il daemon di sicurezza di IoT Edge è un componente nativo che deve essere aggiornato usando la gestione pacchetti nel dispositivo IoT Edge. 

Controllare la versione del daemon di sicurezza in esecuzione nel dispositivo tramite il comando `iotedge version`. 

### <a name="linux-devices"></a>Dispositivi Linux

On Linux x64 devices, use apt-get or your appropriate package manager to update the security daemon. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

On Linux ARM32 devices, use the steps in [Install Azure IoT Edge runtime on Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) to install the latest version of the security daemon. 

### <a name="windows-devices"></a>Dispositivi Windows

On Windows devices, use the PowerShell script to update the security daemon. The script automatically pulls the latest version of the security daemon. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Running the Update-IoTEdge command removes the security daemon from your device, along with the two runtime container images. The config.yaml file is kept on the device, as well as data from the Moby container engine (if you're using Windows containers). Keeping the configuration information means that you don't have to provide the connection string or Device Provisioning Service information for your device again during the update process. 

If you want to install a specific version of the security daemon, download the appropriate Microsoft-Azure-IoTEdge.cab file from [IoT Edge releases](https://github.com/Azure/azure-iotedge/releases). Usare quindi il parametro `-OfflineInstallationPath` in modo che punti al percorso del file. Per altre informazioni, vedere [Installazione offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Aggiornare i contenitori del runtime

The way that you update the IoT Edge agent and IoT Edge hub containers depends on whether you use rolling tags (like 1.0) or specific tags (like 1.0.7) in your deployment. 

Controllare la versione dei moduli dell'agente IoT Edge e dell'hub di IoT Edge attualmente presenti nel dispositivo usando i comandi `iotedge logs edgeAgent` o `iotedge logs edgeHub`. 

  ![Trovare la versione contenitore nei log](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Informazioni sui tag di IoT Edge

Le immagini dell'agente IoT Edge e dell'hub di IoT Edge vengono contrassegnate con la versione di IoT Edge a cui sono associate. Esistono due diversi modi per usare i tag con le immagini di runtime: 

* **Tag di versioni**. Vengono usati solo i primi due valori del numero di versione per ottenere l'immagine più recente corrispondente a tali cifre. Ad esempio, il tag 1.0 viene aggiornato ogni volta che è disponibile una nuova versione in modo da puntare alla versione 1.0.x più recente. Se il runtime del contenitore nel dispositivo IoT Edge esegue nuovamente il pull dell'immagine, i moduli del runtime vengono aggiornati alla versione più recente. Questo approccio è consigliato per finalità di sviluppo. Nelle distribuzioni dal portale di Azure vengono usati i tag di versioni per impostazione predefinita. 
* **Tag specifici**. Vengono usati tutti e tre i valori del numero di versione per impostare in modo esplicito la versione dell'immagine. For example, 1.0.7 won't change after its initial release. Quando si è pronti per l'aggiornamento, è possibile dichiarare un nuovo numero di versione nel manifesto della distribuzione. Questo approccio è consigliato per finalità di produzione.

### <a name="update-a-rolling-tag-image"></a>Aggiornare un'immagine con tag di versioni

Se si usano i tag di versioni nella distribuzione (ad esempio, mcr.microsoft.com/azureiotedge-hub:**1.0**), è necessario forzare il runtime del contenitore nel dispositivo per eseguire il pull della versione più recente dell'immagine. 

Eliminare la versione locale dell'immagine dal dispositivo IoT Edge. Nei computer Windows la disinstallazione del daemon di sicurezza rimuove anche le immagini di runtime, pertanto non è necessario eseguire nuovamente questo passaggio. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Potrebbe essere necessario usare il flag force `-f` per rimuovere le immagini. 

Il servizio IoT Edge eseguirà il pull delle versioni più recenti delle immagini di runtime e le riavvierà automaticamente nel dispositivo. 

### <a name="update-a-specific-tag-image"></a>Aggiornare un'immagine con tag specifici

If you use specific tags in your deployment (for example, mcr.microsoft.com/azureiotedge-hub:**1.0.7**) then all you need to do is update the tag in your deployment manifest and apply the changes to your device. 

Nel portale di Azure le immagini di distribuzione del runtime vengono dichiarate nella sezione **Configura impostazioni avanzate per il runtime di IoT Edge**. 

![Configure advanced edge runtime settings](./media/how-to-update-iot-edge/configure-runtime.png)

In un manifesto della distribuzione JSON aggiornare le immagini dei moduli nella sezione **systemModules**. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>Update to a release candidate version

Azure IoT Edge regularly releases new versions of the IoT Edge service. Before each stable release, there is one or more release candidate (RC) versions. RC versions include all the planned features for the release, but are still going through the testing and validation processes required for a stable release. If you want to test a new feature early, you can install the RC version and provide feedback through GitHub. 

Release candidate versions follow the same numbering convention of releases, but have **-rc** plus an incremental number appended to the end. You can see the release candidates in the same list of [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) as the stable versions. For example, find **1.0.7-rc1** and **1.0.7-rc2**, the two release candidates that came before **1.0.7**. You can also see that RC versions are marked with **pre-release** labels. 

As previews, release candidate versions aren't included as the latest version that the regular installers target. Instead, you need to manually target the assets for the RC version that you want to test. Depending on your IoT Edge device operating system, use the following sections to update IoT Edge to a specific version:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Passaggi successivi

Visualizzare le [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) più recenti.

Rimanere aggiornati grazie agli annunci e agli aggiornamenti recenti nel [blog su Internet delle cose](https://azure.microsoft.com/blog/topics/internet-of-things/). 