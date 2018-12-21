---
title: Non è possibile stabilire una connessione remota a Macchine virtuali di Azure perché il DHCP è disabilitato | Microsoft Docs
description: Informazioni su come risolvere un problema di connessione RDP causato dal servizio Client DHCP disabilitato in Microsoft Azure.| Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 2299dd6c723aa3059c293170c655918e5236ca0e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138161"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Non è possibile stabilire una connessione RDP a Macchine virtuali di Azure perché il servizio Client DHCP è disabilitato

Questo articolo descrive un problema che impedisce di stabilire una connessione Desktop remoto a macchine virtuali Windows di Azure dopo che il servizio Client DHCP è stato disabilitato nella macchina virtuale.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Sintomi

Non è possibile stabilire una connessione RDP a una macchina virtuale in Azure perché il servizio Client DHCP è disabilitato nella macchina virtuale. Controllando lo screenshot in [Diagnostica di avvio](../troubleshooting/boot-diagnostics.md) nel portale di Azure, si vede che la macchina virtuale viene avviata normalmente e attende le credenziali nella schermata di accesso. Si visualizzano in modalità remota i log eventi nella macchina virtuale usando il Visualizzatore eventi. Si noterà che il servizio Client DHCP non è avviato o restituisce un errore di avvio. Di seguito è riportato un log di esempio:

**Nome del log**: Sistema </br>
**Origine**: Gestione controllo servizi </br>
**Data**: 16/12/2015 11:19:36 </br>
**ID evento**: 7022 </br>
**Categoria attività**: Nessuna </br>
**Livello**: Tipi di errore </br>
**Parole chiave**: Classico</br>
**Utente**: N/D </br>
**Computer**: myvm.cosotos.com</br>
**Descrizione**: Il servizio DHCP Client si è bloccato all'avvio.</br>

Per le macchine virtuali di Resource Manager, è possibile usare la console seriale di accesso per eseguire query relative ai log eventi 7022 usando il comando seguente:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Per le macchine virtuali classiche, sarà necessario lavorare in modalità OFFLINE e raccogliere manualmente i log.

## <a name="cause"></a>Causa

Il servizio Client DHCP non è in esecuzione sulla macchina virtuale.

> [!NOTE]
> Questo articolo si applica solo al servizio Client DHCP e non a Server DHCP.

## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

Per risolvere il problema, usare il controllo seriale per abilitare il DHCP o [reimpostare l'interfaccia di rete](reset-network-interface.md) per la macchina virtuale.

### <a name="use-serial-control"></a>Usare il controllo seriale

1. Connettersi alla [console seriale e aprire un'istanza CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se la console seriale non è abilitata nella macchina virtuale, vedere [Come reimpostare l'interfaccia di rete per la VM Windows di Azure](reset-network-interface.md).
2. Verificare se il DHCP è disabilitato nell'interfaccia di rete:

        sc query DHCP
3. Se il DHCP è arrestato, provare ad avviare il servizio.

        sc start DHCP

4. Eseguire di nuovo una query sul servizio per assicurarsi che il servizio sia stato avviato correttamente.

        sc query DHCP

    Provare a connettersi alla macchina virtuale e verificare se il problema è stato risolto.
5. Se il servizio non si avvia, usare una delle soluzioni seguenti, in base al messaggio di errore ricevuto:

    | Tipi di errore  |  Soluzione |
    |---|---|
    | 5- ACCESS DENIED  | Vedere [Il servizio Client DHCP è arrestato a causa di un errore di accesso negato](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Vedere [Il servizio Client DHCP si arresta in modo anomalo o è bloccato](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Vedere [Il servizio Client DHCP è disabilitato](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per una rapida risoluzione del problema.   |
    | 1067 - ERROR_PROCESS_ABORTED |Vedere [Il servizio Client DHCP si arresta in modo anomalo o è bloccato](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per una rapida risoluzione del problema.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Vedere [Il servizio Client DHCP non funziona a causa di un errore di accesso](#dhcp-client-service-fails-because-of-logon-failure). |
    | 1070 - ERROR_SERVICE_START_HANG  | Vedere [Il servizio Client DHCP si arresta in modo anomalo o è bloccato](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Vedere [Il servizio Client DHCP è disabilitato](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per una rapida risoluzione del problema.  |
    |1053 | [Contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per una rapida risoluzione del problema.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Il servizio Client DHCP è arrestato a causa di un errore di accesso negato

1. Connettersi alla [console seriale](serial-console-windows.md#) e aprire un'istanza di PowerShell.
2. Scaricare lo strumento di monitoraggio del processo eseguendo lo script seguente:

   ```
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Avviare una traccia **procmon**:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Riprodurre il problema avviando il servizio che genera il messaggio di errore di **accesso negato**:

   ```
   sc start DHCP
   ```

   Quando si verifica l'errore, terminare la traccia di monitoraggio del processo:

   ```
   procmon /Terminate
   ```
5. Raccogliere il file **c:\temp\ProcMonTrace.PML**:

    1. [Collegare un disco dati alla macchina virtuale](../windows/attach-managed-disk-portal.md
).
    2. Con la console seriale è possibile copiare il file nella nuova unità. Ad esempio: `copy C:\temp\ProcMonTrace.PML F:\`. In questo comando F è la lettera di unità del disco dati collegato. Sostituire la lettera con il valore corretto.
    3. Scollegare l'unità dati e collegarla a una macchina virtuale in esecuzione in cui è installato lo strumento di monitoraggio del processo.

6. Aprire **ProcMonTrace.PML** usando lo strumento di monitoraggio del processo nella macchina virtuale in esecuzione. Applicare quindi il filtro  **Result is ACCESS DENIED** (Risultato è ACCESSO NEGATO), come illustrato nello screenshot seguente：

    ![Filtrare in base al risultato nello strumento di monitoraggio del processo](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Correggere le chiavi del Registro di sistema, le cartelle o i file che sono riportati nell'output. In genere, questo problema è dovuto al fatto che l'account di accesso usato per il servizio non ha l'autorizzazione ACL per accedere a questi oggetti. Per determinare l'autorizzazione ACL corretta per l'account di accesso, è possibile controllarla in una macchina virtuale integra.

#### <a name="dhcp-client-service-is-disabled"></a>Il servizio Client DHCP è disabilitato

1. Ripristinare il valore di avvio predefinito per il servizio:

   ```
   sc config DHCP start= auto
   ```

2. Avviare il servizio:

   ```
   sc start DHCP
   ```

3. Eseguire nuovamente una query sullo stato del servizio per verificare che sia in esecuzione:

   ```
   sc query DHCP
   ```

4. Provare a connettersi alla macchina virtuale usando Desktop remoto.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Il servizio Client DHCP non funziona a causa di un errore di accesso

1. Dato che questo problema si verifica se l'account di avvio del servizio è stato modificato, ripristinare lo stato predefinito dell'account:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Avviare il servizio:

        sc start DHCP
3. Provare a connettersi alla macchina virtuale usando Desktop remoto.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Il servizio Client DHCP si arresta in modo anomalo o è bloccato
1. Se il servizio è bloccato in stato di **avvio** o di **arresto**, provare ad arrestare il servizio:

        sc stop DHCP
2. Isolare il servizio nel contenitore "svchost":

        sc config DHCP type= own
3. Avviare il servizio:

        sc start DHCP
4. Se non è ancora possibile avviare il servizio, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Riparare la macchina virtuale in modalità offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Collegare il disco del sistema operativo alla macchina virtuale di ripristino

1. [Collegare il disco del sistema operativo alla macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md).
2. Avviare una connessione Desktop remoto alla macchina virtuale di ripristino. Verificare che il disco collegato sia contrassegnato come **Online** nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco del sistema operativo collegato.
3.  Aprire un'istanza del prompt dei comandi con privilegi elevati (**Esegui come amministratore**). Eseguire quindi lo script seguente. Questo script presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia **F**. Sostituire la lettera con il valore corretto nella macchina virtuale.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Scollegare il disco del sistema operativo e ricreare la macchina virtuale](../windows/troubleshoot-recovery-disks-portal.md). Controllare quindi se il problema è stato risolto.

## <a name="next-steps"></a>Passaggi successivi

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per la risoluzione del problema.


