---
title: L'arresto di macchine virtuali di Azure è bloccato durante il riavvio, l'arresto o l'arresto dei servizi | Microsoft Docs
description: Questo articolo consente di risolvere gli errori del servizio in Azure macchine virtuali Windows.
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
ms.openlocfilehash: e5ab1262def78da4971ea6e5535f3ac915a38ec8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526759"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>L'arresto della macchina virtuale Windows di Azure è bloccato durante il riavvio, l'arresto o l'arresto dei servizi

Questo articolo illustra i passaggi per risolvere i problemi relativi ai messaggi di "riavvio", "arresto" o "arresto dei servizi" che possono verificarsi quando si riavvia una macchina virtuale (VM) Windows in Microsoft Azure.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, è possibile che nella schermata venga visualizzato il messaggio "riavvio", "arresto in corso" o "arresto dei servizi".

![Riavvio, arresto e arresto delle schermate dei servizi](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Causa

Windows utilizza il processo di arresto per eseguire operazioni di manutenzione del sistema ed elaborare modifiche come aggiornamenti, ruoli e funzionalità. Non è consigliabile interrompere questo processo critico finché non viene completato. A seconda del numero di aggiornamenti/modifiche e delle dimensioni della macchina virtuale, il processo può richiedere molto tempo. Se il processo viene arrestato, è possibile che il sistema operativo venga danneggiato. Interrompi solo il processo se sta richiedendo troppo tempo.

## <a name="solution"></a>Soluzione

### <a name="collect-a-process-memory-dump"></a>Raccogli dump memoria processo

1. Scaricare [lo strumento ProcDump](http://download.sysinternals.com/files/Procdump.zip) in un disco dati nuovo o esistente, collegato a una macchina virtuale funzionante dalla stessa area.

2. Scollegare il disco contenente i file necessari dalla macchina virtuale funzionante e connettere il disco alla macchina virtuale interruppe. Viene chiamato il disco dell' **utilità**.

Usare la [console seriale](./serial-console-windows.md) per completare i passaggi seguenti:

1. Aprire una finestra di PowerShell amministrativa e verificare se il servizio smette di rispondere dopo l'arresto.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. In un CMD amministrativo, ottenere il PID del servizio che non risponde.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Ottenere un esempio di dump della memoria dal processo che non risponde <STOPPING SERVICE> .

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. A questo punto, terminare il processo che non risponde per sbloccare il processo di arresto.

   ``
   taskkill /PID <PID> /t /f
   ``

Quando il sistema operativo viene riavviato, se viene avviato normalmente, assicurarsi che la coerenza del sistema operativo sia accettabile. Se viene segnalato un danneggiamento, eseguire il comando seguente finché il disco non è danneggiato:

``
dism /online /cleanup-image /restorehealth
``

Se non si riesce a raccogliere un dump della memoria del processo o se questo problema è ricorsivo ed è necessaria un'analisi della causa radice, procedere con la raccolta di un dump di memoria del sistema operativo riportato di seguito, il continuare ad aprire una richiesta di supporto.

### <a name="collect-an-os-memory-dump"></a>Raccogliere un dump di memoria del sistema operativo

Se il problema non si risolve dopo l'attesa dell'elaborazione delle modifiche, è necessario raccogliere un file di dump della memoria e contattare il supporto tecnico. Per raccogliere il file dump, seguire questa procedura:

**Collegare il disco del sistema operativo alla macchina virtuale di ripristino**

1. Creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

2. [Alleghi il disco del sistema operativo a una macchina virtuale di ripristino](./troubleshoot-recovery-disks-portal-windows.md).

3. Connettersi tramite Desktop remoto alla macchina virtuale di ripristino.

4. Se il disco del sistema operativo è crittografato, è necessario disattivare la crittografia prima di procedere al passaggio successivo. Per altre informazioni, vedere [decrittografare il disco del sistema operativo crittografato nella macchina virtuale che non può essere avviato](./troubleshoot-bitlocker-boot-error.md#solution).

**Individuare il file dump e inviare un ticket di supporto**

1. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di unità assegnata al disco del sistema operativo collegato è F, è necessario passare a F:\Windows.

2. Individuare il file Memory. dmp, quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) al file dump.

Se non è possibile individuare il file dump, procedere al passaggio successivo per abilitare il log Dump e la console seriale.

**Abilitare il log Dump e la console seriale**

Per abilitare il log Dump e la console seriale, eseguire lo script seguente.

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).

2. Eseguire lo script riportato di seguito:

   In questo script si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. sostituirla con il valore appropriato nella macchina virtuale.

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

4. Se lo spazio disponibile non è sufficiente o se la macchina virtuale è di grandi dimensioni (serie G, GS o E), è possibile modificare il percorso in cui verrà creato il file e fare riferimento a qualsiasi altro disco dati collegato alla macchina virtuale. Per modificare il percorso, è necessario modificare la chiave seguente:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Scollegare il disco del sistema operativo e quindi riconnettere il disco del sistema operativo alla macchina virtuale interessata](./troubleshoot-recovery-disks-portal-windows.md).

6. Avviare la macchina virtuale e accedere alla console seriale.

7. Selezionare Invia interrupt non mascherabile (NMI) per attivare il dump della memoria.

   ![Invia interrupt non mascherabile](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Alleghi nuovamente il disco del sistema operativo a una macchina virtuale di ripristino, Raccogli file dump.

## <a name="contact-microsoft-support"></a>Contattare il supporto Microsoft

Dopo aver raccolto il file dump, contattare il supporto tecnico Microsoft per determinare la causa principale.
