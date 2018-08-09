---
title: Installare Azure IoT Edge in IoT Core | Microsoft Docs
description: Installare il runtime di Azure IoT Edge su un dispositivo Windows IoT Core
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575999"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installare il runtime di IoT Edge su Windows IoT Core - anteprima

Azure IoT Edge e [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) interagiscono per consentire l'elaborazione perimetrale anche in dispositivi di piccole dimensioni. Il Runtime di Azure IoT Edge può essere eseguito anche in piccoli dispositivi Single Board Computer (SBC) molto diffusi nel settore IoT. 

Questo articolo illustra il provisioning del runtime su una scheda di sviluppo che esegue Windows IoT Core. 

**Windows IoT Core supporta attualmente Azure IoT Edge solo con processori Intel x64.**

## <a name="install-the-container-runtime"></a>Installare il runtime per contenitori

1. Configurare la scheda con l'immagine di IoT Core **Build 17134 (RS4)**. 
1. Accendere il dispositivo, quindi [effettuare l'accesso in remoto con PowerShell][lnk-powershell].
1. Nella console di PowerShell installare il runtime del contenitore: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Questo runtime del contenitore proviene dal server di compilazione del progetto Moby ed è destinato solo a fini di valutazione. Non è stato pertanto sottoposto a test, approvato o supportato da Docker.

## <a name="finish-installing"></a>Completare l'installazione

Installare il daemon di sicurezza di IoT Edge e configurarlo seguendo le istruzioni riportate in [questo articolo][lnk-install-windows-on-windows].

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo che esegue il runtime di IoT Edge, scoprire come [distribuire e monitorare i moduli di IoT Edge su larga scala][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
