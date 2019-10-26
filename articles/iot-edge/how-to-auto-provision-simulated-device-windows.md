---
title: Eseguire automaticamente il provisioning di dispositivi Windows con DPS-Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo simulato nel computer Windows per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5842d6fcb5f03754fc8f5922e299d0d9c30d21db
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900824"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Creare ed effettuare il provisioning di un dispositivo IoT Edge simulato con un TPM virtuale in Windows

È possibile effettuare il provisioning automatico di dispositivi Azure IoT Edge tramite il [servizio Device Provisioning](../iot-dps/index.yml) esattamente come per i dispositivi non abilitati per Edge. Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

DPS supporta l'attestazione della chiave simmetrica per i dispositivi IoT Edge sia nella registrazione singola che nella registrazione di gruppo. Per la registrazione di gruppo, se si seleziona l'opzione "IoT Edge dispositivo" come true nell'attestazione della chiave simmetrica, tutti i dispositivi registrati nel gruppo di registrazioni verranno contrassegnati come IoT Edge dispositivi. 

Questo articolo illustra come testare il provisioning automatico in un dispositivo IoT Edge simulato con i passaggi seguenti:

* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare un dispositivo simulato nel computer Windows con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

> [!NOTE]
> Il TPM 2,0 è obbligatorio quando si usa l'attestazione TPM con DPS e può essere usato solo per creare registrazioni individuali, non di gruppo.

> [!TIP]
> Questo articolo descrive il test del provisioning automatico usando l'attestazione TPM nei dispositivi virtuali, ma la maggior parte di esso si applica anche quando si usa l'hardware TPM fisico.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows. In questo articolo viene usato Windows 10.
* Un hub IoT attivo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

> [!TIP]
> Se si usa un dispositivo TPM fisico, è necessario determinare la **chiave**di verifica dell'autenticità, che è univoca per ogni chip TPM ed è ottenuta dal produttore del chip TPM associato. È possibile derivare un **ID di registrazione** univoco per il dispositivo TPM, ad esempio creando un hash SHA-256 della chiave di verifica dell'autenticità.
>
> Seguire le istruzioni riportate nell'articolo [come gestire le registrazioni dei dispositivi con il portale di Azure](../iot-dps/how-to-manage-enrollments.md) per creare la registrazione in DPS e quindi procedere con la sezione [Install the IOT Edge Runtime](#install-the-iot-edge-runtime) in questo articolo per continuare.

## <a name="simulate-a-tpm-device"></a>Simulare un dispositivo TPM

Creare un dispositivo TPM simulato nel computer di sviluppo Windows. Recuperare l' **ID registrazione** e la **chiave** di verifica dell'autenticità per il dispositivo e usarli per creare una singola voce di registrazione in DPS.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

Scegliere il linguaggio di SDK che si vuole usare per creare il dispositivo simulato e seguire i passaggi per creare la singola registrazione.

Quando si crea la registrazione singola, selezionare **true** per dichiarare che il dispositivo TPM simulato nel computer di sviluppo Windows è un **dispositivo IOT Edge**.

Guide relative ai dispositivi simulati e alle singole registrazioni:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Dopo aver creato la singola registrazione, salvare il valore di **ID registrazione**. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.

Quando si esegue il provisioning del dispositivo, sono necessarie le informazioni seguenti:

* Valore dell' **ambito dell'ID** DPS
* ID di **registrazione** del dispositivo creato

Installare il runtime di IoT Edge nel dispositivo che esegue il TPM simulato. Il runtime di IoT Edge verrà configurato per il provisioning automatico, non manuale.

> [!TIP]
> Mantenere aperta la finestra di esecuzione del simulatore TPM durante l'installazione e il test.

Per informazioni più dettagliate sull'installazione di IoT Edge in Windows, inclusi i prerequisiti e le istruzioni per attività come la gestione dei contenitori e l'aggiornamento IoT Edge, vedere [installare il Azure IOT Edge Runtime in Windows](how-to-install-iot-edge-windows.md).

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'installazione di IoT Edge, non di PowerShell (x86).

1. Il comando **deploy-IoTEdge** verifica che il computer Windows si trovi in una versione supportata, attiva la funzionalità contenitori e quindi Scarica il runtime di Moby e il IOT Edge Runtime. Per impostazione predefinita, il comando usa i contenitori di Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. A questo punto, i dispositivi core per le cose possono riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server possono richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. Usare il flag `-Dps` per usare il servizio Device provisioning anziché il provisioning manuale.

   Sostituire i valori segnaposto per `{scope_id}` e `{registration_id}` con i dati raccolti in precedenza.

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
