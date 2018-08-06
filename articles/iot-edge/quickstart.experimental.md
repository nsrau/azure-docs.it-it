---
title: Simulare Azure IoT Edge in Windows | Microsoft Docs
description: Installare il runtime di Azure IoT Edge su un dispositivo simulato in Windows e distribuire il primo modulo
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 28d50ac3a4c080062c12c11977eebb61b0e52eed
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412536"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Guida introduttiva: Distribuire il primo modulo di IoT Edge dal portale di Azure in un dispositivo Windows - Anteprima

Azure IoT Edge consente di eseguire operazioni di analisi ed elaborazione dei dati direttamente nei dispositivi, invece di dover eseguire il push di tutti i dati nel cloud. Le esercitazioni su IoT Edge mostrano come distribuire tipi diversi di moduli, ma prima di tutto è necessario un dispositivo da testare. 

In questa guida introduttiva si apprende come:

1. Creare un hub IoT
2. Registrare un dispositivo IoT Edge
3. Avviare il runtime di IoT Edge
4. Distribuire un modulo

![Architettura dell'esercitazione][2]

Il modulo distribuito in questa guida introduttiva è un sensore simulato che genera dati di temperatura, umidità e pressione. Le altre esercitazioni su Azure IoT Edge si basano sulle operazioni eseguite qui tramite la distribuzione di moduli che analizzano dati simulati per ottenere informazioni aziendali accurate. 

>[!NOTE]
>Il runtime IoT Edge in Windows è disponibile in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito][lnk-account] prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva presuppone che si usi un computer o una macchina virtuale che esegue Windows per simulare un dispositivo IoT. Se si esegue Windows in una macchina virtuale, abilitare la [virtualizzazione annidata][lnk-nested] e allocare almeno 2 GB di memoria. 

Predisporre i requisiti seguenti nel computer usato per un dispositivo IoT Edge:

1. Assicurarsi di usare una versione di Windows supportata:
   * Windows 10 o versioni successive
   * Windows Server 2016 o versioni successive
2. Installare [Docker per Windows][lnk-docker] e assicurarsi che sia in esecuzione.
3. Configurare Docker per l'uso di [contenitori Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Iniziare la guida introduttiva creando un hub IoT nel portale di Azure.
![Creare l'hub IoT][3]

Creare l'hub IoT in un gruppo di risorse che può essere usato per la manutenzione e la gestione di tutte le risorse create in questa guida introduttiva. Specificare un nome facile da ricordare, come **IoTEdgeResources**. Inserendo tutte le risorse per le guide introduttive e le esercitazioni in un gruppo, è possibile gestirle insieme e rimuoverle con facilità al termine dei test. 

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.
![Registrare un dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Installare e avviare il runtime di IoT Edge

Installare e avviare il runtime Azure IoT Edge nel dispositivo IoT Edge. 
![Registrare un dispositivo][5]

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da tre componenti. Il **daemon di sicurezza IoT Edge** si avvia a ogni avvio di un dispositivo Edge ed esegue l' avvio del dispositivo avviando l'agente IoT Edge. L'**agente IoT Edge** semplifica la distribuzione e il monitoraggio di moduli nel dispositivo IoT Edge, tra cui l'hub di IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT. 

>[!NOTE]
>I passaggi di installazione in questa sezione sono attualmente manuali, finché non verrà completato lo sviluppo di uno script di installazione. 

Le istruzioni in questa sezione permettono di configurare il runtime IoT Edge con contenitori Linux. Per usare i contenitori Windows, vedere [Install Azure IoT Edge runtime on Windows to use with Windows containers](how-to-install-iot-edge-windows-with-windows.md) (Installare il runtime Azure IoT Edge in Windows per usarlo con contenitori Windows).

### <a name="download-and-install-the-iot-edge-service"></a>Scaricare e installare il servizio IoT Edge

1. Nel dispositivo IoT Edge eseguire PowerShell come amministratore.

2. Scaricare il pacchetto del servizio IoT Edge.

   ```powershell
   Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
   Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
   Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
   rmdir C:\ProgramData\iotedge\iotedged-windows
   $sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
   $path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
   Set-ItemProperty -Path $sysenv -Name Path -Value $path
   ```

3. Installare vcruntime.

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. Creare e avviare il servizio IoT Edge.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. Aggiungere le eccezioni del firewall per le porte usate dal servizio IoT Edge.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. Creare un nuovo file denominato **iotedge.reg** e aprirlo con un editor di testo. 

7. Aggiungere il contenuto seguente e salvare il file. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. Passare al file in Esplora file e farvi doppio clic sopra per importare le modifiche da apportare al Registro di sistema di Windows. 

### <a name="configure-the-iot-edge-runtime"></a>Configurare il runtime di IoT Edge 

Configurare il runtime con la stringa di connessione del dispositivo IoT Edge copiata quando è stato registrato un nuovo dispositivo. Configurare quindi la rete di runtime. 

1. Aprire il file di configurazione di IoT Edge, che si trova in `C:\ProgramData\iotedge\config.yaml`. Poiché questo file è protetto, eseguire un editor di testo come Blocco note come amministratore, quindi usare l'editor per aprire il file. 

2. Individuare la sezione **provisioning** e aggiornare il valore di **device_connection_string** con la stringa copiata dai dettagli del dispositivo IoT Edge. 

3. Nella finestra di PowerShell dell'amministratore recuperare il nome host per il dispositivo IoT Edge e copiare l'output. 

   ```powershell
   hostname
   ```

4. Nel file di configurazione individuare la sezione **Edge device hostname**. Aggiornare il valore di **hostname** con il nome host copiato da PowerShell.

3. Nella finestra di PowerShell dell'amministratore recuperare l'indirizzo IP del dispositivo IoT Edge. 

   ```powershell
   ipconfig
   ```

4. Copiare il valore per **Indirizzo IPv4** nella sezione **vEthernet (DockerNAT)** dell'output. 

5. Creare una variabile di ambiente denominata **IOTEDGE_HOST**, sostituendo *\<ip_address\>*  con l'indirizzo IP del dispositivo IoT Edge. 

  ```powershell
  [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
  ```

  Rendere persistente la variabile di ambiente nei vari riavvii.

  ```powershell
  SETX /M IOTEDGE_HOST "http://<ip_address>:15580"
  ```

6. Nel file `config.yaml` individuare la sezione **Connect settings**. Aggiornare i valori di **management_uri** e **workload_uri** con l'indirizzo IP e le porte che sono state aperte nella sezione precedente. Sostituire **\<GATEWAY_ADDRESS\>** con l'indirizzo IP DockerNAT che è stato copiato.

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. Individuare la sezione **Listen settings** e aggiungere gli stessi valori di **management_uri** e **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. Individuare la sezione **Moby Container Runtime settings** e verificare che il valore di **network** sia privo di commenti e impostato su **azure-iot-edge**

   ```yaml
   moby_runtime:
     docker_uri: "npipe://./pipe/docker_engine"
     network: "azure-iot-edge"
   ```
   
9. Salvare il file di configurazione. 

10. In PowerShell riavviare il servizio IoT Edge.

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>Visualizzare lo stato del runtime IoT Edge

Verificare che il runtime sia stato installato e configurato correttamente.

1. Verificare lo stato del servizio IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Se è necessario risolvere problemi del servizio, recuperare i log di servizio. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false} |
    format-table -autosize -wrap
   ```

3. Visualizzare tutti i moduli in esecuzione nel dispositivo IoT Edge. Poiché il servizio è stato avviato per la prima volta, verrà visualizzato solo il modulo **edgeAgent** in esecuzione. Il modulo edgeAgent viene eseguito per impostazione predefinita e permette di installare e avviare tutti i moduli aggiuntivi distribuiti nel dispositivo. 

   ```powershell
   iotedge list
   ```

   ![Visualizzare un modulo nel dispositivo](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invierà dati di telemetria all'hub IoT.
![Registrare un dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per eseguire il push di un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso. In questo caso il modulo di cui è stato eseguito il push crea dati ambientali che è possibile usare per le esercitazioni. 

Verificare che il modulo distribuito dal cloud sia in esecuzione nel dispositivo IoT Edge. 

```powershell
iotedge list
```

   ![Visualizzare tre moduli nel dispositivo](./media/quickstart/iotedge-list-2.png)

Visualizzare i messaggi inviati dal modulo tempSensor al cloud. 

```powershell
iotedge logs tempSensor -f
```

  ![Visualizzare i dati dal modulo](./media/quickstart/iotedge-logs.png)

È anche possibile visualizzare i messaggi ricevuti dall'hub IoT usando lo [strumento IoT Hub Explorer][lnk-iothub-explorer] o l'[estensione Azure IoT Toolkit per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare con le esercitazioni su IoT Edge, è possibile usare il dispositivo registrato e configurato in questa guida introduttiva. In caso contrario, è possibile eliminare le risorse di Azure create e rimuovere il runtime IoT Edge dal dispositivo. 

### <a name="delete-azure-resources"></a>Eliminare le risorse di Azure

Se la macchina virtuale e l'hub IoT sono stati creati in un nuovo gruppo di risorse, è possibile eliminare il gruppo e tutte le risorse associate. Se nel gruppo sono presenti risorse che si vuole conservare, eliminare solo le singole risorse che si vuole pulire. 

Per rimuovere un gruppo di risorse, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.
2. Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse che contiene l'hub IoT. 
3. A destra del gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.
4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere di nuovo il nome del gruppo di risorse per confermare e fare clic su **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

### <a name="remove-the-iot-edge-runtime"></a>Rimuovere il runtime IoT Edge

Se si prevede di usare il dispositivo IoT Edge per test futuri, ma si vuole interrompere l'invio di dati da parte del modulo tempSensor all'hub IoT quando non è in uso, usare il comando seguente per arrestare il servizio IoT Edge. 

   ```powershell
   Stop-Service iotedge -NoWait
   ```

È possibile riavviare il servizio quando si è pronti per riavviare i test

   ```powershell
   Start-Service iotedge
   ```

Se si vuole rimuovere le installazioni dal dispositivo, usare i comandi seguenti.  

Rimuovere il runtime IoT Edge.

   ```powershell
   cmd /c sc delete iotedge
   rm -r c:\programdata\iotedge
   ```

Quando il runtime IoT Edge viene rimosso, i contenitori creati vengono arrestati, ma rimangono sul dispositivo. Visualizzare tutti i contenitori.

   ```powershell
   docker ps -a
   ```

Eliminare i contenitori creati nel dispositivo dal runtime IoT Edge. Modificare il nome del contenitore tempSensor se gli è stato assegnato un nome diverso. 

   ```powershell
   docker rm -f tempSensor
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. A questo punto, è disponibile un dispositivo di test che genera dati non elaborati relativi al proprio ambiente. 

È ora possibile continuare con una delle altre esercitazioni per scoprire in che modo trasformare questi dati in informazioni aziendali accurate tramite Azure IoT Edge.

> [!div class="nextstepaction"]
> [Filtrare dati dei sensori tramite una funzione di Azure](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
