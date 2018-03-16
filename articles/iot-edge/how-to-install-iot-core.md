---
title: Installare Azure IoT Edge in IoT Core | Microsoft Docs
description: Installare il runtime di Azure IoT Edge su un dispositivo Windows IoT Core
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 75d2b370ed6118a30153a001a4b654d7212b56cd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installare il runtime di IoT Edge su Windows IoT Core - anteprima

Azure IoT Edge e [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) interagiscono per consentire l'elaborazione perimetrale anche in dispositivi di piccole dimensioni. Il Runtime di Azure IoT Edge può essere eseguito anche in piccoli dispositivi Single Board Computer (SBC) molto diffusi nel settore IoT. 

In questo articolo vengono illustrati il provisioning del runtime in una scheda di sviluppo [MinnowBoard Turbot][lnk-minnow] che esegue Windows IoT Core. Windows IoT Core supporta Azure IoT Edge solo con processori Intel x64. 

## <a name="install-the-runtime"></a>Installare il runtime

1. Installare il [Dashboard Windows 10 IoT Core] [ lnk-core] in un sistema host.
1. Seguire i passaggi in [Set up your device (Impostare il dispositivo)] [ lnk-board] per configurare la scheda con l'immagine di MinnowBoard Turbot/MAX Build 16299. 
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

1. Installare il runtime IoT Edge e verificare la configurazione:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Lo script esegue queste operazioni: 
   * Python 3.6
   * Script di controllo di IoT Edge (iotedgectl.exe)

È possibile visualizzare l'output informativo dallo strumento iotedgectl.exe in verde nella finestra remota di PowerShell. Ciò non indica necessariamente la presenza di errori. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo che esegue il runtime di IoT Edge, scoprire come [distribuire e monitorare i moduli di IoT Edge su larga scala][lnk-deploy].

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers