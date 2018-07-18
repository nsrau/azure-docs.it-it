---
title: Come installare Azure IoT Edge in Windows con contenitori Linux | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Windows con contenitori Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 503dfc0c7606d44a1b9ab635aa0d479df61f3820
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435474"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Installare il runtime di Azure IoT Edge in Windows per l'uso con contenitori Linux

Il runtime di Azure IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da tre componenti. Il **daemon di sicurezza di IoT Edge** fornisce e gestisce gli standard di sicurezza nel dispositivo Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando l'agente di IoT Edge. L'**agente di IoT Edge** semplifica la distribuzione e il monitoraggio di moduli nel dispositivo Edge, tra cui l'hub di IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT.

Questo articolo descrive i passaggi da eseguire per installare il runtime di Azure IoT Edge in un sistema Windows x64 (Intel o AMD). Il supporto per Windows è attualmente disponibile in versione di anteprima.

>[!NOTE]
Una configurazione con contenitori Linux su sistemi Windows non è una configurazione di produzione consigliata o supportata per Azure IoT Edge. È tuttavia possibile usarla a scopo di sviluppo e test.

## <a name="supported-windows-versions"></a>Versioni supportate di Windows
Quando si usano contenitori Linux, è possibile eseguire Azure IoT Edge per lo sviluppo e il test sulle versioni di Windows seguenti:
  * Windows 10 o sistemi operativi desktop più recenti.
  * Windows Server 2016 o sistemi operativi server più recenti.

## <a name="install-the-container-runtime"></a>Installare il runtime per contenitori 

Azure IoT Edge si basa su un runtime per contenitori [compatibile con OCI][lnk-oci], ad esempio Docker. 

È possibile usare [Docker per Windows][lnk-docker-for-windows] per attività di sviluppo e test. Verificare che Docker per Windows sia [configurato per l'uso di contenitori Linux][lnk-docker-config].

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di Azure IoT Edge

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

### <a name="download-the-edge-daemon-package-and-install"></a>Scaricare il pacchetto del daemon di Edge e installarlo

In una finestra di PowerShell per amministratore eseguire i comandi seguenti:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Installare vcruntime nel modo seguente:

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Creare e avviare il servizio *iotedge*:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Aggiungere le eccezioni del firewall per le porte usate dal servizio:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Creare un file **iotedge.reg** con il contenuto seguente e importarlo nel Registro di sistema di Windows facendovi doppio clic o usando il comando `reg import iotedge.reg`:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurare il daemon di sicurezza di Azure IoT Edge

Il daemon può essere configurato usando il file di configurazione in `C:\ProgramData\iotedge\config.yaml`.

Il dispositivo Edge può essere configurato manualmente tramite una [stringa di connessione del dispositivo][lnk-dcs] oppure [automaticamente tramite il servizio di provisioning di dispositivi][lnk-dps].

* Per la configurazione manuale, rimuovere il commento dalla modalità di provisioning **manuale**. Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge.

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

* Per la configurazione automatica, rimuovere il commento dalla modalità di provisioning **dps**. Aggiornare i valori di **scope_id** e **registration_id** con i valori dell'istanza DPS dell'hub IoT e aggiornare il dispositivo IoT Edge con TPM. 

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

Ottenere il nome del dispositivo Edge usando il comando `hostname` in PowerShell e impostarlo come valore per **hostname** nel file yaml di configurazione. Ad esempio: 

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

Sarà quindi necessario specificare l'indirizzo IP e la porta per **workload_uri** e **management_uri** nella sezione **connect:** e **listen:** della configurazione.

Per recuperare l'indirizzo IP, immettere `ipconfig` nella finestra di PowerShell. Copiare l'indirizzo IP dell'interfaccia  **vEthernet (DockerNAT)**, come illustrato nell'esempio seguente (l'indirizzo IP nel sistema in uso può essere diverso):

![DockerNat][img-docker-nat]

Aggiornare **workload_uri** e **management_uri** nella sezione **connect:** del file di configurazione. Sostituire **\<GATEWAY_ADDRESS\>** con l'indirizzo IP che è stato copiato. 

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

Immettere gli stessi indirizzi nella sezione **listen:** della configurazione, usando l'indirizzo IP come l'indirizzo del gateway.

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

Nella finestra di PowerShell creare una variabile di ambiente **IOTEDGE_HOST** con l'indirizzo **management_uri**.

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

Rendere persistente la variabile di ambiente nei vari riavvii.

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

Verificare infine che l'impostazione **network** in **moby_runtime** non sia commentata e sia impostata su **azure-iot-edge**.

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Salvare il file di configurazione e riavviare il servizio:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se è stata usata la **configurazione manuale** nella sezione precedente, il runtime di IoT Edge deve essere correttamente sottoposto a provisioning e in esecuzione nel dispositivo. Se è stata usata la **configurazione automatica**, è necessario completare alcuni passaggi aggiuntivi in modo che il runtime possa registrare il dispositivo con l'hub IoT per tuo conto. Per i passaggi successivi, vedere [Creare ed effettuare il provisioning di un dispositivo simulato TPM Edge in Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).


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
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Elencare infine i moduli in esecuzione con il comando seguente:

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi durante l'installazione del runtime di Edge, vedere la pagina relativa alla [risoluzione dei problemi][lnk-trouble].


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

