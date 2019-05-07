---
title: Installare Azure IoT Edge per Linux in Windows | Microsoft Docs
description: Istruzioni di installazione di Azure IoT Edge per i contenitori Linux in Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: bb47a1b828084673961a6d2c5657793b4437f294
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160576"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Usare IoT Edge in Windows per eseguire contenitori Linux

Testare i moduli di IoT Edge per i dispositivi Linux Usa un computer Windows. 

In uno scenario di produzione, i dispositivi Windows devono solo eseguire i contenitori di Windows. Tuttavia, uno scenario di sviluppo comune consiste nell'utilizzare un computer Windows per compilare i moduli per i dispositivi Linux IoT Edge. Il runtime di IoT Edge per Windows consente di eseguire contenitori Linux per **test e sviluppo** scopi. 

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge usano contenitori di Linux su x64 (Intel/AMD) di Windows system. Per altre informazioni sul programma di installazione runtime di IoT Edge, incluse informazioni dettagliate su tutti i parametri di installazione, vedere [installare il runtime di Azure IoT Edge su Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Prerequisiti

Usare questa sezione per verificare se il dispositivo Windows in uso può supportare IoT Edge e per prepararlo per un motore del contenitore prima dell'installazione. 

### <a name="supported-windows-versions"></a>Versioni supportate di Windows

Azure IoT Edge con i contenitori Linux possono eseguire nelle versioni seguenti di Windows: 
* Aggiornamento dell'anniversario di Windows 10 (build 14393) o versione successiva
* Windows Server 2016 o versioni successive

Per altre informazioni sui componenti inclusi nella versione più recente di IoT Edge, fare riferimento alle [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Se si desidera installare IoT Edge in una macchina virtuale, abilitare la virtualizzazione annidata e allocare almeno 2 GB di memoria. Come si abilita la virtualizzazione nidificata è diversa a seconda dell'hypervisor all'utilizzo. Per Hyper-V, macchine virtuali di generazione 2 sono annidati virtualizzazione abilitata per impostazione predefinita. Per VMWare, è disponibile un elemento toggle per abilitare la funzionalità nella macchina virtuale. 

### <a name="prepare-the-container-engine"></a>Preparare il motore del contenitore 

Azure IoT Edge si basa su un motore del contenitore [compatibile con OCI](https://www.opencontainers.org/). La differenza di configurazione principale tra l'esecuzione di contenitori Windows e Linux in un Windows macchina è che l'installazione di IoT Edge include un runtime di contenitore di Windows, ma è necessario fornire il proprio runtime per i contenitori Linux prima di installare IoT Edge. 

Per configurare un computer Windows per sviluppare e testare i contenitori per i dispositivi Linux, è possibile usare [Desktop Docker](https://www.docker.com/docker-windows) come motore di contenitore. È necessario installare Docker e configurarlo per il [usare i contenitori Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) prima di installare IoT Edge.  

Se il dispositivo IoT Edge è un computer Windows, assicurarsi che soddisfi i [requisiti di sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) per Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installare IoT Edge in un nuovo dispositivo

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

Uno script di PowerShell scarica e installa il daemon di sicurezza di Azure IoT Edge. Il daemon di sicurezza avvia quindi il primo di due moduli di runtime, l'agente IoT Edge, che consente distribuzioni remote di altri moduli. 

Quando si installa il runtime di IoT Edge per la prima volta in un dispositivo, è necessario effettuare il provisioning del dispositivo con un'identità da un hub IoT. Un singolo dispositivo IoT Edge può eseguirne manualmente usando una stringa di connessione dispositivo fornita dall'hub IoT. In alternativa, è possibile usare il servizio Device Provisioning per effettuare automaticamente il provisioning dei dispositivi. Ciò è utile quando si dispone di molti dispositivi da configurare. 

Altre informazioni sulle opzioni di installazione e i parametri nell'articolo [installare il runtime di Azure IoT Edge su Windows](how-to-install-iot-edge-windows.md). Dopo aver creato Desktop Docker installato e configurato per i contenitori Linux, la differenza principale sta dichiarando Linux con il **- ContainerOs** parametro. Ad esempio:  

1. Se già stato fatto, registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione. Copiare la stringa di connessione da usare in seguito in questa sezione. È possibile completare questo passaggio con gli strumenti seguenti:

   * [Portale di Azure](how-to-register-device-portal.md)
   * [Interfaccia della riga di comando di Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Eseguire PowerShell come amministratore.

3. Il **Distribuisci IoTEdge** comando verifica che nel computer Windows si trova in una versione supportata, viene attivata la funzionalità del contenitore e quindi scarica il runtime moby (che non viene usato per i contenitori Linux) e il runtime di IoT Edge. Le impostazioni predefinite di comandi ai contenitori Windows, quindi dichiarare Linux come sistema operativo contenitore desiderato. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. A questo punto, i dispositivi IoT Core potrebbe essere riavviato automaticamente. Altri dispositivi Windows 10 o Windows Server possono essere necessario riavviare. In questo caso, riavviare il dispositivo. Quando il dispositivo è pronto, eseguire PowerShell come amministratore di nuovo.

5. Il **Initialize-IoTEdge** comando Configura il runtime di IoT Edge nel computer. Il comando viene impostato su provisioning manuale con una stringa di connessione del dispositivo. Dichiarare nuovamente Linux come sistema operativo contenitore desiderato. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando richiesto, specificare la stringa di connessione dispositivo recuperato nel passaggio 1. La stringa di connessione consente di associare il dispositivo fisico con un ID dispositivo nell'IoT Hub. 

   La stringa di connessione viene espresso nel formato seguente e non deve includere le virgolette: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Verificare lo stato del servizio IoT Edge. Si dovrebbe essere elencato come in esecuzione.  

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione. Dopo una nuova installazione, il modulo solo dovrebbe essere in esecuzione **edgeAgent**. Dopo aver [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md), si noterà ad altri utenti. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

In caso di problemi che impediscono di installare correttamente IoT Edge, vedere la pagina relativa alla [risoluzione dei problemi](troubleshoot.md).

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
