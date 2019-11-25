---
title: Automatically provision Windows devices with DPS - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo simulato nel computer Windows per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457146"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Create and provision a simulated IoT Edge device with a virtual TPM on Windows

È possibile effettuare il provisioning automatico di dispositivi Azure IoT Edge tramite il [servizio Device Provisioning](../iot-dps/index.yml) esattamente come per i dispositivi non abilitati per Edge. Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

DPS supports symmetric key attestation for IoT Edge devices in both individual enrollment and group enrollment. For group enrollment, if you check “is IoT Edge device” option to be true in symmetric key attestation, all the devices that are registered under that enrollment group will be marked as IoT Edge devices. 

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare un dispositivo simulato nel computer Windows con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes testing auto-provisioning by using TPM attestation on virtual devices, but much of it applies when using physical TPM hardware as well.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows. In questo articolo viene usato Windows 10.
* Un hub IoT attivo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

> [!TIP]
> If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.
>
> Follow the instructions in the article [How to manage device enrollments with Azure Portal](../iot-dps/how-to-manage-enrollments.md) to create your enrollment in DPS and then proceed with the [Install the IoT Edge runtime](#install-the-iot-edge-runtime) section in this article to continue.

## <a name="simulate-a-tpm-device"></a>Simulare un dispositivo TPM

Creare un dispositivo TPM simulato nel computer di sviluppo Windows. Retrieve the **Registration ID** and **Endorsement key** for your device, and use them to create an individual enrollment entry in DPS.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

Scegliere il linguaggio di SDK che si vuole usare per creare il dispositivo simulato e seguire i passaggi per creare la singola registrazione.

When you create the individual enrollment, select **True** to declare that the simulated TPM device on your Windows development machine is an **IoT Edge device**.

Guide relative ai dispositivi simulati e alle singole registrazioni:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Dopo aver creato la singola registrazione, salvare il valore di **ID registrazione**. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.

You'll need the following information when provisioning your device:

* The DPS **ID Scope** value
* The device **Registration ID** you created

Install the IoT Edge runtime on the device that is running the simulated TPM. You'll configure the IoT Edge runtime for automatic, not manual, provisioning.

> [!TIP]
> Mantenere aperta la finestra di esecuzione del simulatore TPM durante l'installazione e il test.

For more detailed information about installing IoT Edge on Windows, including prerequisites and instructions for tasks like managing containers and updating IoT Edge, see [Install the Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

1. Aprire una finestra di PowerShell in modalità amministratore. Be sure to use an AMD64 session of PowerShell when installing IoT Edge, not PowerShell (x86).

1. The **Deploy-IoTEdge** command checks that your Windows machine is on a supported version, turns on the containers feature, and then downloads the moby runtime and the IoT Edge runtime. The command defaults to using Windows containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. At this point, IoT Core devices may restart automatically. Other Windows 10 or Windows Server devices may prompt you to restart. If so, restart your device now. Once your device is ready, run PowerShell as an administrator again.

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. Use the `-Dps` flag to use the Device Provisioning Service instead of manual provisioning.

   Replace the placeholder values for `{scope_id}` and `{registration_id}` with the data you collected earlier.

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
