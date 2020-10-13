---
title: Risoluzione dei problemi relativi all'agente guest di Microsoft Azure
description: Risolvere i problemi relativi all'agente guest di Windows Azure non funziona
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 738c2a240ad6c88186357e69b02d33b40d366d7f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977066"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Risoluzione dei problemi relativi all'agente guest di Microsoft Azure

Agente guest di Microsoft Azure è un agente di macchine virtuali (VM). Consente alla macchina virtuale di comunicare con il controller di infrastruttura (il server fisico sottostante in cui è ospitata la macchina virtuale) nell'indirizzo IP 168.63.129.16. Si tratta di un indirizzo IP pubblico virtuale che facilita la comunicazione. Per ulteriori informazioni, vedere la pagina relativa all' [indirizzo IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

 Per la macchina virtuale di cui è stata eseguita la migrazione in Azure da locale o creato tramite un'immagine personalizzata non è installato l'agente guest di Microsoft Azure. In questi scenari è necessario installare manualmente l'agente di macchine virtuali. Per altre informazioni su come installare l'agente di [macchine virtuali, vedere Panoramica dell'agente di macchine virtuali di Azure](../extensions/agent-windows.md).

Dopo aver installato correttamente l'agente guest di Windows Azure, è possibile visualizzare i servizi seguenti elencati in Services. msc nella macchina virtuale:
 
- Servizio agente guest di Microsoft Azure
- Servizio di telemetria
- Servizio agente desktop remoto

**Servizio agente guest di Microsoft Azure**: questo servizio è responsabile di tutta la registrazione in WAppAgent. log. Questo servizio è responsabile della configurazione di varie estensioni e della comunicazione da Guest a host. 

**Servizio di telemetria**: questo servizio è responsabile dell'invio dei dati di telemetria della macchina virtuale al server back-end.

**Servizio agente desktop remoto**: questo servizio è responsabile dell'installazione dell'agente guest. Il programma di installazione trasparente è anche un componente dell'agente desktop remoto che consente di aggiornare altri componenti e servizi dell'agente guest. RDAgent è anche responsabile dell'invio di heartbeat dalla macchina virtuale Guest all'agente host nel server fisico.

> [!NOTE]
> A partire dalla versione 2.7.41491.971 dell'agente guest della macchina virtuale, il componente di telemetria è incluso nel servizio RDAgent, pertanto è possibile che questo servizio di telemetria non sia elencato nelle VM appena create.

## <a name="checking-agent-status-and-version"></a>Verifica dello stato e della versione dell'agente

Passare alla pagina delle proprietà della macchina virtuale in portale di Azure e controllare lo **stato dell'agente**. Se l'agente guest di Windows Azure funziona correttamente, lo stato viene visualizzato **pronto**. Se lo stato dell'agente di macchine virtuali **non è pronto** , le estensioni e il **comando Esegui** nel portale di Azure non funzioneranno.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Risoluzione dei problemi relativi all'agente di macchine virtuali in stato non pronto

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>Passaggio 1 controllare se il servizio agente guest di Windows Azure è installato

- Verifica il pacchetto

    Individuare la cartella C:\WindowsAzure. Se viene visualizzata la cartella GuestAgent che Visualizza il numero di versione, significa che l'agente guest di Microsoft Azure è stato installato nella macchina virtuale. È anche possibile cercare il pacchetto installato.  Se l'agente guest di Windows Azure è installato nella macchina virtuale, il pacchetto verrà salvato nel percorso seguente: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    È possibile eseguire il comando di PowerShell seguente per verificare se l'agente di macchine virtuali è stato distribuito nella macchina virtuale:
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    Nell'output individuare la proprietà **ProvisionVMAgent** e verificare se il valore è impostato su **true**. In caso contrario, significa che l'agente è installato nella macchina virtuale.
    
- Controllare i servizi e i processi

   Passare alla console dei servizi (Services. msc) e controllare lo stato dei servizi seguenti: servizio agente guest di Windows Azure, servizio RDAgent, servizio di telemetria di Windows Azure e servizio agente di rete di Microsoft Azure.
 
    È inoltre possibile controllare se i servizi sono in esecuzione esaminando gestione attività per i processi seguenti:
       
    - WindowsAzureGuestAgent.exe: servizio agente guest di Microsoft Azure
    - WaAppAgent.exe: servizio RDAgent
    - WindowsAzureNetAgent.exe: servizio agente di rete di Microsoft Azure
    - WindowsAzureTelemetryService.exe: servizio di telemetria di Microsoft Azure

   Se non è possibile trovare questi processi e servizi, significa che non è installato l'agente guest di Microsoft Azure.

- Verificare il programma e la funzionalità

    Nel pannello di controllo passare a **programmi e funzionalità** per determinare se il servizio agente guest di Windows Azure è installato.

Se non vengono trovati pacchetti, servizi e processi in esecuzione e non è possibile vedere l'agente guest di Microsoft Azure installato in programmi e funzionalità, provare a [installare il servizio agente guest di Microsoft Azure](../extensions/agent-windows.md). Se l'agente guest non viene installato correttamente, è possibile [installare l'agente di macchine virtuali offline](./install-vm-agent-offline.md).

Se è possibile visualizzare i servizi e sono in esecuzione, riavviare il servizio per verificare se il problema è stato risolto. Se i servizi vengono arrestati, avviarli e attendere alcuni minuti. Controllare quindi se lo **stato dell'agente** sta segnalando come **pronto**. Se si riscontrano arresti anomali di questi servizi, è possibile che alcuni processi di terze parti causino l'arresto anomalo di questi servizi. Per ulteriori informazioni sulla risoluzione di questi problemi, contattare [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-working"></a>Passaggio 2 controllare se l'aggiornamento automatico funziona

L'agente guest di Windows Azure dispone di una funzionalità di aggiornamento automatico. Verificherà automaticamente la disponibilità di nuovi aggiornamenti e li installerà. Se la funzionalità di aggiornamento automatico non funziona correttamente, provare a disinstallare e installare l'agente guest di Windows Azure attenendosi alla procedura seguente:

1. Se l'agente guest di Windows Azure viene visualizzato in **programmi e funzionalità**, disinstallare l'agente guest di Windows Azure.

2. Aprire una finestra del prompt dei comandi con privilegi di amministratore.

3. Arrestare i servizi dell'agente guest. Se i servizi non vengono arrestati, è necessario impostare i servizi su **avvio manuale** e quindi riavviare la macchina virtuale.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. Eliminare i servizi dell'agente guest:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. In `C:\WindowsAzure` creare una cartella denominata precedente.

1. Spostare tutte le cartelle denominate Packages o GuestAgent nella cartella precedente.

1. Scaricare e installare la versione più recente del pacchetto di installazione dell'agente da [qui](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409). Per completare l'installazione sono necessari i diritti di amministratore.

1. Installare l'agente guest usando il comando seguente:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Controllare quindi se i servizi dell'agente guest vengono avviati correttamente.
 
    In rari casi in cui l'agente guest non viene installato correttamente, è possibile [installare l'agente di macchine virtuali offline](./install-vm-agent-offline.md).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Passaggio 3 controllare se la macchina virtuale è in grado di connettersi al controller di infrastruttura

Usare uno strumento come PsPing per verificare se la macchina virtuale è in grado di connettersi a 168.63.129.16 sulle porte 80, 32526 e 443. Se la macchina virtuale non si connette come previsto, controllare se la comunicazione in uscita sulle porte 80, 443 e 32526 è aperta nel firewall locale della macchina virtuale. Se l'indirizzo IP è bloccato, l'agente VM potrebbe visualizzare un comportamento imprevisto in diversi scenari.

## <a name="advanced-troubleshooting"></a>Risoluzione dei problemi avanzata

Gli eventi per la risoluzione dei problemi dell'agente guest di Microsoft Azure vengono registrati nei file di log seguenti:

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Di seguito sono riportati alcuni scenari comuni in cui l'agente guest di Windows Azure può immettere lo stato **non pronto** o smettere di funzionare come previsto.

### <a name="agent-stuck-on-starting"></a>Agente bloccato in "avvio"

Nel registro WaAppAgent è possibile notare che l'agente è bloccato al processo di avvio e non può essere avviato.

**Informazioni sui log**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent a partire da. Versione 2.7.41491.901

**Analisi**
 
La macchina virtuale sta ancora eseguendo la versione precedente dell'agente guest di Windows Azure. Nella cartella C:\WindowsAzure è possibile notare che sono installate più istanze dell'agente guest di Windows Azure, incluse alcune della stessa versione. Poiché sono installate più istanze di Agent, la macchina virtuale non avvia la versione più recente dell'agente guest di Microsoft Azure.

**Soluzione**

Disinstallare manualmente l'agente guest di Windows Azure e quindi reinstallarlo attenendosi alla procedura seguente:

1. Aprire il pannello di controllo > programmi e funzionalità e disinstallare l'agente guest di Windows Azure.
1. Aprire Gestione attività e arrestare i servizi seguenti: servizio agente guest di Windows Azure, servizio RDAgent, servizio di telemetria di Windows Azure e servizio agente di rete di Microsoft Azure.
1. In C:\WindowsAzure creare una cartella denominata OBSOLETa.
1. Spostare tutte le cartelle denominate Packages o GuestAgent nella cartella precedente. Spostare inoltre le cartelle GuestAgent in C:\WindowsAzure\logs che iniziano come GuestAgent_x. x. xxxxx nella cartella precedente.
1. Scaricare e installare la versione più recente dell'agente MSI. Per completare l'installazione, è necessario disporre dei diritti di amministratore.
1. Installare l'agente guest usando il comando MSI seguente:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Verificare che il RDAgent, l'agente guest di Windows Azure e i servizi di telemetria di Windows Azure siano ora in esecuzione.
 
1. Controllare WaAppAgent. log per assicurarsi che sia in esecuzione la versione più recente dell'agente guest di Microsoft Azure.
 
1. Elimina la cartella precedente in C:\WindowsAzure.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Impossibile connettersi a WireServer IP (host IP) 

Si notano le voci di errore seguenti in WaAppAgent. log e telemetria. log:

**Informazioni sui log**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Analisi**

La macchina virtuale non è in grado di raggiungere il server host wireserver.

**Soluzione**

1. Poiché wireserver non è raggiungibile, connettersi alla macchina virtuale usando Desktop remoto e quindi provare ad accedere all'URL seguente da un browser Internet: http://168.63.129.16/?comp=versions 
1. Se non è possibile raggiungere l'URL del passaggio 1, controllare l'interfaccia di rete per determinare se è impostata come abilitata per DHCP e con DNS. Per controllare lo stato DHCP, dell'interfaccia di rete, eseguire il comando seguente:  `netsh interface ip show config` .
1. Se DHCP è disabilitato, eseguire il comando seguente assicurandosi di modificare il valore in giallo con il nome dell'interfaccia: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Verificare la presenza di problemi che potrebbero essere causati da un firewall, da un proxy o da un'altra origine che potrebbe bloccare l'accesso all'indirizzo IP 168.63.129.16.
1. Controllare se Windows Firewall o un firewall di terze parti sta bloccando l'accesso alle porte 80, 443 e 32526. Per ulteriori informazioni sui motivi per cui l'indirizzo non deve essere bloccato, vedere la pagina relativa all' [indirizzo IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

### <a name="guest-agent-is-stuck-stopping"></a>L'agente guest è bloccato "arresto"  

In WaAppAgent. log si notano le voci di errore seguenti:

**Informazioni sui log** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Analisi**

L'agente guest di Microsoft Azure è bloccato durante il processo di arresto.

**Soluzione**

1. Assicurarsi che WaAppAgent.exe sia in esecuzione nella macchina virtuale. Se non è in esecuzione, riavviare il servizio rdgagent e attendere cinque minuti. Quando WaAppAgent.exe è in esecuzione, terminare il processo di WindowsAzureGuest.exe.
2. Se il passaggio 1 non risolve il problema, rimuovere la versione attualmente installata e installare manualmente la versione più recente dell'agente.

### <a name="npcap-loopback-adapter"></a>Scheda loopback Npcap 

In WaAppAgent. log si notano le voci di errore seguenti:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Analisi**

La scheda loopback Npcap è installata nella macchina virtuale da Wireshark. Wireshark è uno strumento open source per la profilatura del traffico di rete e l'analisi dei pacchetti. Uno strumento di questo tipo è spesso definito analizzatore di rete, analizzatore protocollo di rete o sniffer.

**Soluzione**

È probabile che la scheda loopback Npcap sia installata da WireShark. Provare a disabilitarlo, quindi controllare se il problema è stato risolto.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione del problema relativo all'agente guest di Windows Azure non funzionante, [contattare il supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).