---
title: Installazione di Azure IoT Edge per Linux in Windows Documenti Microsoft
description: Istruzioni di installazione di Azure IoT Edge per i contenitori Linux in Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133170"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Usare IoT Edge in Windows per eseguire contenitori LinuxUse IoT Edge on Windows to run Linux containers

Testare i moduli IoT Edge per i dispositivi Linux usando una macchina Windows.Test IoT Edge modules for Linux devices using a Windows machine.

In uno scenario di produzione, i dispositivi Windows devono eseguire solo contenitori Windows.In a production scenario, Windows devices should only run Windows containers. Tuttavia, uno scenario di sviluppo comune consiste nell'utilizzare un computer Windows per creare moduli IoT Edge per dispositivi Linux.However, a common development scenario is to use a Windows computer to build IoT Edge modules for Linux devices. Il runtime IoT Edge per Windows consente di eseguire contenitori Linux a scopo di **test e sviluppo.**

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge usando contenitori Linux nel sistema Windows x64 (AMD/Intel). Per altre informazioni sul programma di installazione del runtime di IoT Edge, inclusi i dettagli su tutti i parametri di installazione, vedere Installare il runtime di [Azure IoT Edge in Windows.](how-to-install-iot-edge-windows.md)

Per informazioni sugli elementi inclusi nella versione più recente di IoT Edge, vedere Versioni di [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Prerequisiti

Usare questa sezione per verificare se il dispositivo Windows in uso può supportare IoT Edge e per prepararlo per un motore del contenitore prima dell'installazione.

### <a name="supported-windows-versions"></a>Versioni supportate di Windows

Azure IoT Edge con contenitori Linux può essere eseguito in qualsiasi versione di Windows che soddisfi i [requisiti per Docker DesktopAzure](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) IoT Edge with Linux containers can run on any version of Windows that meets the requirements for Docker Desktop

Se si desidera installare IoT Edge in una macchina virtuale, abilitare la virtualizzazione annidata e allocare almeno 2 GB di memoria. La modalità di abilitazione della virtualizzazione annidata è diversa a seconda dell'hypervisor utilizzato. Per Hyper-V, le macchine virtuali di generazione 2 hanno la virtualizzazione annidata abilitata per impostazione predefinita. Per VMWare, è disponibile un interruttore per abilitare la funzionalità nella macchina virtuale.

### <a name="prepare-the-container-engine"></a>Preparare il motore del contenitorePrepare the container engine

Azure IoT Edge si basa su un motore del contenitore [compatibile con OCI](https://www.opencontainers.org/). La differenza di configurazione più grande tra l'esecuzione di contenitori Windows e Linux in un computer Windows è che l'installazione di IoT Edge include un runtime del contenitore di Windows, ma è necessario fornire il runtime per i contenitori Linux prima di installare IoT Edge.

Per configurare una macchina Windows per sviluppare e testare contenitori per dispositivi Linux, è possibile usare [Docker Desktop](https://www.docker.com/docker-windows) come motore contenitore. È necessario installare Docker e configurarlo per [l'utilizzo](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) di contenitori Linux prima di installare IoT Edge.  

Se il dispositivo IoT Edge è un computer Windows, assicurarsi che soddisfi i [requisiti di sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) per Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installare IoT Edge in un nuovo dispositivo

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

Uno script di PowerShell scarica e installa il daemon di sicurezza di Azure IoT Edge. Il daemon di sicurezza avvia quindi il primo di due moduli di runtime, l'agente IoT Edge, che consente distribuzioni remote di altri moduli.

Quando si installa il runtime di IoT Edge per la prima volta in un dispositivo, è necessario effettuare il provisioning del dispositivo con un'identità da un hub IoT. È possibile eseguire manualmente il provisioning di un singolo dispositivo IoT Edge usando una stringa di connessioni del dispositivo fornita dall'hub IoT.A single IoT Edge device can be provisioned manually using a device connections string provided by your IoT hub. In alternativa, è possibile usare il servizio Device Provisioning per effettuare automaticamente il provisioning dei dispositivi. Ciò è utile quando si dispone di molti dispositivi da configurare.

Per altre informazioni sui diversi parametri e opzioni di installazione, vedere l'articolo Installare il runtime di [Azure IoT Edge in Windows.](how-to-install-iot-edge-windows.md) Dopo aver installato e configurato Docker Desktop per i contenitori Linux, la differenza principale di installazione consiste nel dichiarare Linux con il parametro **-ContainerOs.** Ad esempio:

1. Se non l'hai già fatto, registra un nuovo dispositivo IoT Edge e recupera la stringa di connessione del dispositivo. Copiare la stringa di connessione da utilizzare più avanti in questa sezione. È possibile completare questo passaggio utilizzando gli strumenti seguenti:You can complete this step using the following tools:

   * [Portale di Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [Interfaccia della riga di comando di AzureAzure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Eseguire PowerShell come amministratore.

   >[!NOTE]
   >Usare una sessione AMD64 di PowerShell per installare IoT Edge, non PowerShell (x86). Se non si conosce il tipo di sessione in uso, eseguire questo comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Il comando **Deploy-IoTEdge** controlla che il computer Windows sia in una versione supportata, attiva la funzionalità dei contenitori e quindi scarica il runtime moby (che non viene utilizzato per i contenitori Linux) e il runtime di IoT Edge. Il comando predefinito per i contenitori di Windows, in modo da dichiarare Linux come il sistema operativo contenitore desiderato.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. A questo punto, i dispositivi IoT Core potrebbero riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server potrebbero richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

5. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. Il comando viene eseguito per impostazione predefinita al provisioning manuale con una stringa di connessione del dispositivo. Dichiarare Linux come sistema operativo contenitore desiderato.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando richiesto, fornire la stringa di connessione del dispositivo recuperata nel passaggio 1.When prompted, provide the device connection string that you retrieved in step 1. The device connection string associates the physical device with a device ID in IoT Hub.

   La stringa di connessione del dispositivo assume il formato seguente e non deve includere virgolette:The device connection string takes the following format, and should not include quotationmarks:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Controllare lo stato del servizio IoT Edge:

```powershell
Get-Service iotedge
```

Esaminare i registri di servizio degli ultimi 5 minuti:Examine service logs from the last 5 minutes:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Eseguire un controllo automatico per individuare gli errori di configurazione e di rete più comuni:Run an automated check for the most common configuration and networking errors:

```powershell
iotedge check
```

Elencare i moduli in esecuzione. Dopo una nuova installazione, l'unico modulo che dovrebbe essere visualizzato in esecuzione è **edgeAgent**. Dopo aver distribuito i [moduli IoT Edge](how-to-deploy-modules-portal.md) per la prima volta, l'altro modulo di sistema, **edgeHub**, verrà avviato anche sul dispositivo.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

In caso di problemi che impediscono di installare correttamente IoT Edge, vedere la pagina relativa alla [risoluzione dei problemi](troubleshoot.md).

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
