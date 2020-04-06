---
title: Eseguire automaticamente il provisioning dei dispositivi Windows con DPS - Azure IoT Edge Documenti Microsoft
description: Usare un dispositivo simulato nel computer Windows per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fc051e2a0ebbdae7c62ff8a249747d118d3c2ce4
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668683"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Creare ed eseguire il provisioning di un dispositivo IoT Edge simulato con un TPM virtuale in Windows

I dispositivi Azure IoT Edge possono essere sottoposti a provisioning automatico usando il [servizio Device Provisioning](../iot-dps/index.yml) proprio come i dispositivi non abilitati per l'edge. Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

DPS supporta l'attestazione a chiave simmetrica per i dispositivi IoT Edge nella registrazione individuale e di gruppo. Per la registrazione di gruppo, se si seleziona l'opzione "è dispositivo IoT Edge" per essere true nell'attestazione della chiave simmetrica, tutti i dispositivi registrati in tale gruppo di registrazione verranno contrassegnati come dispositivi IoT Edge.

Questo articolo illustra come testare il provisioning automatico in un dispositivo IoT Edge simulato con i passaggi seguenti:This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare un dispositivo simulato nel computer Windows con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

> [!TIP]
> Questo articolo descrive il test del provisioning automatico tramite l'attestazione TPM su dispositivi virtuali, ma gran parte di esso si applica anche quando si usa l'hardware TPM fisico.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows. In questo articolo viene usato Windows 10.
* Un hub IoT attivo.

> [!NOTE]
> Il TPM 2.0 è necessario quando si utilizza l'attestazione TPM con DPS e può essere utilizzato solo per creare registrazioni singole, non di gruppo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

> [!TIP]
> Se si utilizza un dispositivo TPM fisico, è necessario determinare la **chiave di verifica,** che è univoca per ogni chip TPM e viene ottenuta dal produttore del chip TPM associato. È possibile derivare un **ID di registrazione** univoco per il dispositivo TPM, ad esempio creando un hash SHA-256 della chiave di approvazione.
>
> Seguire le istruzioni nell'articolo [Come gestire le registrazioni](../iot-dps/how-to-manage-enrollments.md) dei dispositivi con il portale di Azure per creare la registrazione in DPS e quindi procedere con la sezione [Installare il runtime di IoT Edge](#install-the-iot-edge-runtime) in questo articolo per continuare.

## <a name="simulate-a-tpm-device"></a>Simulare un dispositivo TPM

Creare un dispositivo TPM simulato nel computer di sviluppo Windows. Recuperare **l'ID di registrazione** e la **chiave di approvazione** per il dispositivo e utilizzarli per creare una singola voce di registrazione in DPS.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

Scegliere il linguaggio di SDK che si vuole usare per creare il dispositivo simulato e seguire i passaggi per creare la singola registrazione.

Quando si crea la registrazione singola, selezionare **True** per dichiarare che il dispositivo TPM simulato nel computer di sviluppo Windows è un **dispositivo IoT Edge**.

> [!TIP]
> Nell'interfaccia della riga di comando di Azure è possibile creare una registrazione o un gruppo di registrazione e usare il flag abilitato per Edge per specificare che un dispositivo o un gruppo di dispositivi è un dispositivo IoT Edge.In the Azure CLI, you can create an [enrollment](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) or [an enrollment group](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) and use the **edge-enabled** flag to specify that a device, or group of devices, is an IoT Edge device.

Guide relative ai dispositivi simulati e alle singole registrazioni:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Dopo aver creato la singola registrazione, salvare il valore di **ID registrazione**. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.

Quando si esegue il provisioning del dispositivo, sono necessarie le informazioni seguenti:You'll need the following information when provisioning your device:

* Il valore **dell'ambito ID** DPS
* ID **di registrazione** del dispositivo creato

Installare il runtime IoT Edge nel dispositivo che esegue il TPM simulato. Il runtime Di IoT Edge verrà configurato per il provisioning automatico, non manuale.

> [!TIP]
> Mantenere aperta la finestra di esecuzione del simulatore TPM durante l'installazione e il test.

Per informazioni più dettagliate sull'installazione di Edge In Windows, inclusi prerequisiti e istruzioni per attività quali la gestione dei contenitori e l'aggiornamento di IoT Edge, vedere Installare il runtime di [Azure IoT Edge in Windows.](how-to-install-iot-edge-windows.md)

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'installazione di IoT Edge, non powerShell (x86).

1. Il comando **Deploy-IoTEdge** controlla che il computer Windows sia in una versione supportata, attiva la funzionalità dei contenitori e quindi scarica il runtime moby e il runtime di IoT Edge. Il comando utilizza per impostazione predefinita i contenitori di Windows.The default command defaults to using Windows containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. A questo punto, i dispositivi IoT Core potrebbero riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server potrebbero richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. Utilizzare `-Dps` il flag per usare il servizio Device Provisioning anziché il provisioning manuale.

   Sostituire i valori `{scope_id}` `{registration_id}` segnaposto per e con i dati raccolti in precedenza.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime è stato avviato correttamente, è possibile passare all'hub IoT e iniziare la distribuzione di moduli IoT Edge nel dispositivo. Usare i comandi seguenti sul dispositivo per verificare che il runtime sia stato installato e avviato correttamente.  

Verificare lo stato del servizio IoT Edge.

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come [Distribuire e monitorare i moduli IoT Edge su larga scala tramite il portale di Azure](how-to-deploy-monitor.md) o usando l'[interfaccia della riga di comando di Azure](how-to-deploy-monitor-cli.md)
