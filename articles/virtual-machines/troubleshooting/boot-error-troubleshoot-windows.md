---
title: L'arresto delle macchine virtuali di Azure è bloccato in Riavvio, arresto o arresto dei servizi. Documenti Microsoft
description: Questo articolo consente di risolvere gli errori del servizio in Macchine virtuali Windows di Azure.This article helps you troubleshoot service errors in Azure Windows Virtual Machines.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371365"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Arresto della macchina virtuale Windows di Azure è bloccato su "Riavvio", "Arresto" o "Arresto dei servizi"

In questo articolo viene illustrata la procedura per risolvere i problemi di messaggi "Riavvio", "Arresto" o "Arresto dei servizi" che possono verificarsi quando si riavvia una macchina virtuale Windows (VM) in Microsoft Azure.

## <a name="symptoms"></a>Sintomi

Quando si utilizza la diagnostica di [avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare la schermata della macchina virtuale, è possibile che nella schermata venga visualizzato il messaggio "Riavvio", "Arresto" o "Arresto dei servizi".

![Riavvio, arresto e arresto delle schermate dei servizi](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Causa

Windows utilizza il processo di arresto per eseguire operazioni di manutenzione del sistema e le modifiche del processo, ad esempio aggiornamenti, ruoli e funzionalità. Non è consigliabile interrompere questo processo critico fino al completamento. A seconda del numero di aggiornamenti/modifiche e delle dimensioni della macchina virtuale, il processo potrebbe richiedere molto tempo. Se il processo viene arrestato, è possibile che il sistema operativo venga danneggiato. Interrompi il processo solo se richiede troppo tempo.

## <a name="solution"></a>Soluzione

### <a name="collect-a-process-memory-dump"></a>Raccogliere un'immagine della memoria del processoCollect a Process memory dump

1. Scaricare [lo strumento Procdump](http://download.sysinternals.com/files/Procdump.zip) in un disco dati nuovo o esistente, collegato a una macchina virtuale funzionante dalla stessa area.

2. Scollegare il disco contenente i file necessari dalla macchina virtuale funzionante e collegare il disco alla macchina virtuale interrotta. Stiamo chiamando questo disco il **disco di utilità**.

Utilizzare [la console seriale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) per completare i seguenti passaggi:

1. Aprire un Powershell amministrativo e controllare il servizio bloccato al momento dell'arresto.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Su un CMD amministrativo, ottenere il PID del servizio hung.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Ottenere un campione di immagine <STOPPING SERVICE>della memoria dal processo bloccato .

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Ora uccidere il processo appeso per sbloccare il processo di arresto.

   ``
   taskkill /PID <PID> /t /f
   ``

Una volta che il sistema operativo ricomincia, se si avvia normalmente, assicurarsi che la coerenza del sistema operativo sia ok. Se viene segnalato un danneggiamento, eseguire il comando seguente fino a quando il disco non è danneggiato:

``
dism /online /cleanup-image /restorehealth
``

Se non è possibile raccogliere un dump della memoria del processo o questo problema è ricorsivo e si richiede un'analisi della causa principale, procedere con la raccolta di un dump della memoria del sistema operativo riportato di seguito, procedere all'apertura di una richiesta di supporto.

### <a name="collect-an-os-memory-dump"></a>Raccogliere un dump di memoria del sistema operativo

Se il problema non si risolve dopo aver atteso il processo delle modifiche, è necessario raccogliere un file di immagine della memoria e contattare il supporto tecnico. Per raccogliere il file dump, seguire questa procedura:

**Collegare il disco del sistema operativo alla macchina virtuale di ripristino**

1. Creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Collegare il disco del sistema operativo a una macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)di ripristino.

3. Connettersi tramite Desktop remoto alla macchina virtuale di ripristino.

4. Se il disco del sistema operativo è crittografato, è necessario disattivare la crittografia prima di passare al passaggio successivo. Per altre informazioni, vedere [Decrittografare il disco del sistema operativo crittografato nella macchina virtuale che non è in grado di avviare.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)

**Individuare il file dump e inviare un ticket di supporto**

1. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di unità assegnata al disco del sistema operativo collegato è F, è necessario passare a F:\Windows.

2. Individuare il file memory.dmp e quindi [inviare un ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) di supporto con il file dump.

Se non è possibile individuare il file dump, procedere al passaggio successivo per abilitare il log Dump e la console seriale.

**Abilitare il log Dump e la console seriale**

Per abilitare il log Dump e la console seriale, eseguire lo script seguente.

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).

2. Eseguire lo script riportato di seguito:

   In questo script si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato nella macchina virtuale.

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
   
   REM Enable Serial Console
   bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
   bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
   bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
   bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
   bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   
   REM Suggested configuration to enable OS Dump
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Verificare che lo spazio sul disco sia sufficiente per allocare la quantità di memoria della RAM, che dipende dalle dimensioni selezionate per questa macchina virtuale.

4. Se lo spazio disponibile non è sufficiente o la macchina virtuale è di grandi dimensioni (serie G, GS o E), è possibile modificare il percorso in cui verrà creato il file e fare riferimento a qualsiasi altro disco dati collegato alla macchina virtuale. Per modificare la posizione, è necessario modificare la seguente chiave:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Scollegare il disco del sistema operativo e quindi ricollegare il disco del sistema operativo alla macchina virtuale interessata.](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

6. Avviare la macchina virtuale e accedere alla Console seriale.

7. Selezionare Invia interrupt non mascherabile (NMI) per attivare l'immagine della memoria.

   ![Invia interrupt non mascherabile](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Collegare nuovamente il disco del sistema operativo a una macchina virtuale di ripristino, raccogliere il file di dump.

## <a name="contact-microsoft-support"></a>Contattare il supporto Microsoft

Dopo aver raccolto il file dump, contattare il supporto tecnico Microsoft per determinare la causa principale.
