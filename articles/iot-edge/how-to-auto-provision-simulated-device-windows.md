---
title: Effettuare il provisioning automatico di dispositivi Windows con il servizio Device Provisioning - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo simulato nel computer Windows per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 01247dfc0046ef722d70fe48f7ab8ee63f685962
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153562"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Creare ed effettuare il provisioning di un dispositivo simulato TPM Edge in Windows

È possibile effettuare il provisioning di dispositivi Azure IoT Edge tramite il [servizio Device Provisioning](../iot-dps/index.yml) esattamente come per i dispositivi non abilitati per Edge. Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

Questo articolo illustra come testare il provisioning automatico in un dispositivo Edge simulato con i passaggi seguenti:

* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare un dispositivo simulato nel computer Windows con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows. In questo articolo viene usato Windows 10.
* Un hub IoT attivo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="simulate-a-tpm-device"></a>Simulare un dispositivo TPM

Creare un dispositivo TPM simulato nel computer di sviluppo Windows. Recuperare l'**ID registrazione** e la **chiave di approvazione** per il dispositivo e usarli per creare una singola voce di registrazione nel servizio Device Provisioning.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

Scegliere il linguaggio di SDK che si vuole usare per creare il dispositivo simulato e seguire i passaggi per creare la singola registrazione.

Quando si crea la singola registrazione, selezionare **Abilita** per dichiarare che il dispositivo TPM simulato nel computer di sviluppo Windows è un **dispositivo IoT Edge**.

Guide relative ai dispositivi simulati e alle singole registrazioni:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Dopo aver creato la singola registrazione, salvare il valore di **ID registrazione**. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Dopo aver completato la sezione precedente, verrà visualizzato il nuovo dispositivo, elencato come dispositivo IoT Edge nell'hub IoT. Sarà quindi necessario installare il runtime IoT Edge nel dispositivo.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.  

Seguire le istruzioni per installare il runtime IoT Edge nel dispositivo che esegue il dispositivo TPM simulato della sezione precedente. Assicurarsi di configurare il runtime IoT Edge per il provisioning automatico, non manuale.

Prima di installare IoT Edge sul dispositivo, assicurarsi di conoscere il valore di **Ambito ID** del servizio Device Provisioning e il valore di **ID registrazione** del dispositivo.

[Installare ed effettuare automaticamente il provisioning di IoT Edge](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

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
