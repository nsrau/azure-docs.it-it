---
title: Effettuare il provisioning automatico di un dispositivo Azure IoT Edge con il servizio Device Provisioning - Windows | Microsoft Docs
description: Usare un dispositivo simulato nel computer Windows per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e149886e1ade80d7751f58eb1f77031c4e432b75
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307944"
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

Quando si crea la singola registrazione, selezionare **Abilita** per dichiarare che la macchina virtuale è un **dispositivo IoT Edge**.

Guide relative ai dispositivi simulati e alle singole registrazioni: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Dopo aver creato la singola registrazione, salvare il valore di **ID registrazione**. Questo valore viene usato quando si configura il runtime IoT Edge. 

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale. Nei dispositivi che eseguono Windows, è possibile scegliere se usare contenitori Windows o Linux. Scegliere il tipo di contenitori da usare e seguire la procedura. Assicurarsi di configurare il runtime IoT Edge per il provisioning automatico, non manuale. 

Seguire le istruzioni per installare il runtime IoT Edge nel dispositivo che esegue il dispositivo TPM simulato della sezione precedente. 

Prima di iniziare gli articoli seguenti, assicurarsi di conoscere il valore di **Ambito ID** del servizio Device Provisioning e il valore di **ID registrazione** del dispositivo. 

* [Contenitori Windows](how-to-install-iot-edge-windows-with-windows.md)
* [Contenitori Linux](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>Creare una variabile di ambiente per il dispositivo TPM

Nel computer che esegue il dispositivo simulato, modificare il registro del servizio **iotedge** per impostare una variabile di ambiente.

1. Dal menu **Start** aprire **regedit**. 
2. Passare a **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge**. 
3. Selezionare **Modifica** > **Nuovo** > **Valore multistringa**. 
4. Immettere il nome **Environment**. 
5. Fare doppio clic sulla nuova variabile e impostare i dati del valore su **IOTEDGE_USE_TPM_DEVICE=ON**. 
6. Fare clic su **OK** per salvare le modifiche. 

## <a name="restart-the-iot-edge-runtime"></a>Riavviare il runtime IoT Edge

Riavviare il runtime IoT Edge in modo che accetti tutte le modifiche alla configurazione apportate nel dispositivo. 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime viene avviato correttamente, è possibile accedere all'hub IoT e verificare se è stato eseguito il provisioning automatico del nuovo dispositivo e se è pronto per l'esecuzione di moduli IoT Edge. 

Verificare lo stato del servizio IoT Edge.

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Elencare i moduli in esecuzione.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come [Distribuire e monitorare i moduli IoT Edge su larga scala tramite il portale di Azure](how-to-deploy-monitor.md) o usando l'[interfaccia della riga di comando di Azure](how-to-deploy-monitor-cli.md)