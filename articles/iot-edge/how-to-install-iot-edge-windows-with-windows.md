---
title: Come installare Azure IoT Edge in Windows con contenitori Windows | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Windows con contenitori Windows
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: kgremban
ms.openlocfilehash: 39e0de6b378ed61ab375c6468b58c8c4a87b5fb9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575965"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Installare il runtime di Azure IoT Edge in Windows per l'uso con contenitori Windows

Il runtime di Azure IoT Edge è distribuito in tutti i dispositivi IoT Edge. È costituito da tre componenti. Il **daemon di sicurezza di IoT Edge** fornisce e gestisce gli standard di sicurezza nel dispositivo Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando l'agente di IoT Edge. L'**agente di IoT Edge** semplifica la distribuzione e il monitoraggio di moduli nel dispositivo Edge, tra cui l'hub di IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT.

Questo articolo descrive i passaggi da eseguire per installare il runtime di Azure IoT Edge in un sistema Windows x64 (Intel o AMD). 

Il supporto per Windows è attualmente disponibile in versione di anteprima.

## <a name="supported-windows-versions"></a>Versioni supportate di Windows
Azure IoT Edge con i contenitori Windows può essere usato con:
  * Windows 10/IoT Enterprise/IoT Core con aggiornamento di aprile 2018 (build 17134).
  * Windows Server 1803

## <a name="install-the-container-runtime"></a>Installare il runtime per contenitori 

>[!NOTE]
>Per l'installazione del motore dei contenitori in Windows IoT Core, seguire la procedura in [Effettuare il provisioning di un dispositivo IoT Core][lnk-iot-core] e quindi continuare con le istruzioni riportate di seguito.

Azure IoT Edge si basa su un runtime per contenitori [compatibile con OCI][lnk-oci], ad esempio Docker. È possibile usare [Docker per Windows][lnk-docker-for-windows] per attività di sviluppo e test. 

Configurare Docker per Windows [per l'uso di contenitori Windows][lnk-docker-config].

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di Azure IoT Edge

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

È possibile effettuare il provisioning di un singolo dispositivo IoT Edge manualmente usando una stringa di connessione dispositivo fornita dall'hub IoT. In alternativa, è possibile usare il servizio di provisioning di dispositivi per effettuare il provisioning di dispositivi automaticamente. Ciò è utile quando si dispone di molti dispositivi di cui effettuare il provisioning. A seconda del provisioning, scegliere lo script di installazione appropriato. 

### <a name="install-and-manually-provision"></a>Effettuare il provisioning e l'installazione manualmente

1. Seguire i passaggi descritti in [Register a new Azure IoT Edge device][lnk-dcs] (Registrare un nuovo dispositivo Azure IoT Edge) per registrare il dispositivo e recuperare la stringa di connessione. 

2. Nel dispositivo IoT Edge eseguire PowerShell come amministratore. 

3. Scaricare e installare il runtime IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. Quando viene chiesto un elemento **DeviceConnectionString**, fornire la stringa di connessione recuperata dall'hub IoT. Non racchiudere la stringa di connessione tra virgolette. 

### <a name="install-and-automatically-provision"></a>Installare ed effettuare automaticamente il provisioning

1. Seguire i passaggi descritti in [Creare ed effettuare il provisioning di un dispositivo simulato TPM Edge in Windows][lnk-dps] per configurare il servizio di provisioning di dispositivi e recuperare il relativo **ID ambito**, simulare un dispositivo TPM e recuperare il relativo **ID registrazione**, quindi creare una registrazione singola. Dopo aver registrato il dispositivo nell'hub IoT, continuare con l'installazione.  

   >[!TIP]
   >Mantenere aperta la finestra di esecuzione del simulatore TPM durante l'installazione e il test. 

2. Nel dispositivo IoT Edge eseguire PowerShell come amministratore. 

3. Scaricare e installare il runtime IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. Quando richiesto, specificare l'**ID ambito** e l'**ID registrazione** per il servizio di provisioning e il dispositivo. 

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

È possibile verificare lo stato del servizio IoT Edge nel modo seguente: 

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti nel modo seguente:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Elencare infine i moduli in esecuzione con il comando seguente:

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge][lnk-modules].

In caso di problemi durante l'installazione del runtime di Edge, vedere la pagina relativa alla [risoluzione dei problemi][lnk-trouble].


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md
[lnk-modules]: how-to-deploy-modules-portal.md
