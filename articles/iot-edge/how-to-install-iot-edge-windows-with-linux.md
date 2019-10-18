---
title: Installare Azure IoT Edge per Linux in Windows | Microsoft Docs
description: Azure IoT Edge istruzioni di installazione per i contenitori Linux in Windows 10, Windows Server e Windows Internet Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 3cf30b53f950ff18dd6dcde332b7e97e332133aa
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516561"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Usare IoT Edge in Windows per eseguire i contenitori Linux

Testare i moduli IoT Edge per i dispositivi Linux usando un computer Windows. 

In uno scenario di produzione, i dispositivi Windows devono eseguire solo i contenitori di Windows. Tuttavia, uno scenario di sviluppo comune è quello di usare un computer Windows per compilare moduli IoT Edge per i dispositivi Linux. Il runtime di IoT Edge per Windows consente di eseguire contenitori Linux a scopo di **test e sviluppo** . 

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge usando i contenitori Linux nel sistema Windows x64 (AMD/Intel). Per ulteriori informazioni sul programma di installazione di IoT Edge Runtime, inclusi i dettagli su tutti i parametri di installazione, vedere [Install the Azure IOT Edge Runtime on Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Prerequisiti

Usare questa sezione per verificare se il dispositivo Windows in uso può supportare IoT Edge e per prepararlo per un motore del contenitore prima dell'installazione. 

### <a name="supported-windows-versions"></a>Versioni supportate di Windows

Azure IoT Edge con i contenitori Linux possono essere eseguiti in qualsiasi versione di Windows che soddisfi i [requisiti per desktop Docker](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Per altre informazioni sui componenti inclusi nella versione più recente di IoT Edge, fare riferimento alle [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Se si desidera installare IoT Edge in una macchina virtuale, abilitare la virtualizzazione annidata e allocare almeno 2 GB di memoria. La modalità di abilitazione della virtualizzazione annidata è diversa a seconda dell'hypervisor usato. Per Hyper-V, per le macchine virtuali di seconda generazione la virtualizzazione nidificata è abilitata per impostazione predefinita. Per VMWare, è disponibile un interruttore per abilitare la funzionalità nella macchina virtuale. 

### <a name="prepare-the-container-engine"></a>Preparare il motore del contenitore 

Azure IoT Edge si basa su un motore del contenitore [compatibile con OCI](https://www.opencontainers.org/). La differenza di configurazione più grande tra l'esecuzione di contenitori Windows e Linux in un computer Windows è che l'installazione di IoT Edge include un runtime di contenitori di Windows, ma è necessario fornire il proprio Runtime per i contenitori Linux prima di installare IoT Edge. 

Per configurare un computer Windows per sviluppare e testare i contenitori per i dispositivi Linux, è possibile usare [Docker desktop](https://www.docker.com/docker-windows) come motore di contenitori. È necessario installare Docker e configurarlo per l' [uso di contenitori Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) prima di installare IOT Edge.  

Se il dispositivo IoT Edge è un computer Windows, assicurarsi che soddisfi i [requisiti di sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) per Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installare IoT Edge in un nuovo dispositivo

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

Uno script di PowerShell scarica e installa il daemon di sicurezza di Azure IoT Edge. Il daemon di sicurezza avvia quindi il primo di due moduli di runtime, l'agente IoT Edge, che consente distribuzioni remote di altri moduli. 

Quando si installa il runtime di IoT Edge per la prima volta in un dispositivo, è necessario effettuare il provisioning del dispositivo con un'identità da un hub IoT. È possibile eseguire manualmente il provisioning di un singolo dispositivo IoT Edge usando una stringa di connessione del dispositivo fornita dall'hub. In alternativa, è possibile usare il servizio Device Provisioning per effettuare automaticamente il provisioning dei dispositivi. Ciò è utile quando si dispone di molti dispositivi da configurare. 

Per altre informazioni sui diversi parametri e opzioni di installazione, vedere l'articolo [installare il runtime di Azure IOT Edge in Windows](how-to-install-iot-edge-windows.md). Dopo aver installato e configurato desktop Docker per i contenitori Linux, la differenza di installazione principale è la dichiarazione di Linux con il parametro **-containeros** . ad esempio: 

1. Se non è già stato fatto, registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione del dispositivo. Copiare la stringa di connessione da usare più avanti in questa sezione. Per completare questo passaggio, è possibile usare gli strumenti seguenti:

   * [Azure portal](how-to-register-device-portal.md)
   * [interfaccia della riga di comando di Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Eseguire PowerShell come amministratore.

   >[!NOTE]
   >Usare una sessione AMD64 di PowerShell per installare IoT Edge, non PowerShell (x86). Se non si conosce il tipo di sessione in uso, eseguire questo comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Il comando **deploy-IoTEdge** verifica che il computer Windows si trovi in una versione supportata, attiva la funzionalità contenitori e quindi Scarica il runtime di Moby (che non viene usato per i contenitori Linux) e il runtime di IOT Edge. Per impostazione predefinita, il comando viene impostato sui contenitori di Windows, quindi dichiarare Linux come sistema operativo del contenitore desiderato. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. A questo punto, i dispositivi core per le cose possono riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server possono richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

5. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. Per impostazione predefinita, il comando esegue il provisioning manuale con una stringa di connessione del dispositivo. Dichiarare di nuovo Linux come sistema operativo del contenitore desiderato. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando richiesto, specificare la stringa di connessione del dispositivo recuperata nel passaggio 1. La stringa di connessione del dispositivo associa il dispositivo fisico a un ID dispositivo nell'hub. 

   La stringa di connessione del dispositivo ha il formato seguente e non deve includere virgolette: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Verificare lo stato del servizio IoT Edge: 

```powershell
Get-Service iotedge
```

Esaminare i log dei servizi degli ultimi 5 minuti: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Eseguire un controllo automatizzato per la configurazione e gli errori di rete più comuni: 

```powershell
iotedge check
```

Elencare i moduli in esecuzione. Dopo una nuova installazione, l'unico modulo che dovrebbe essere in esecuzione è **edgeAgent**. Dopo aver [distribuito i moduli IOT Edge](how-to-deploy-modules-portal.md) per la prima volta, l'altro modulo di sistema, **edgeHub**, si avvierà anche sul dispositivo. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

In caso di problemi che impediscono di installare correttamente IoT Edge, vedere la pagina relativa alla [risoluzione dei problemi](troubleshoot.md).

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
