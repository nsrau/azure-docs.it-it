---
title: Errore CRITICAL SERVICE FAILED all'avvio di una macchina virtuale di Azure | Microsoft Docs
description: Informazioni su come risolvere l'errore "0x0000005A-CRITICAL SERVICE FAILED" che si verifica durante l'avvio
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: f038e56fe4b1e6ad2737217674706eef77a39fd6
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058063"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Visualizzazione dell'errore "CRITICAL SERVICE FAILED" su schermata blu all'avvio di una macchina virtuale di Azure
Questo articolo descrive l'errore "CRITICAL SERVICE FAILED", dovuto alla mancata esecuzione di un servizio critico, che può verificarsi all'avvio di una macchina virtuale Windows in Microsoft Azure. Illustra inoltre i passaggi da seguire per risolvere i problemi. 

> [!NOTE] 
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra il modello di distribuzione Resource Manager, che Microsoft consiglia di usare per le nuove distribuzioni in sostituzione del modello di distribuzione classica.

## <a name="symptom"></a>Sintomo 

La macchina virtuale Windows non si avvia. Quando si controllano gli screenshot di avvio in [Diagnostica di avvio](./boot-diagnostics.md), viene visualizzato un messaggio di errore simile ai seguenti su una schermata blu:

- "Si è verificato un problema ed è necessario riavviare il PC. È possibile riavviare. Per altre informazioni su questo problema e sulle possibili correzioni, visitare https://windows.com/stopcode. Se si chiama un addetto del supporto tecnico, fornire queste informazioni: Codice di arresto: CRITICAL SERVICE FAILED" 
- "Si è verificato un problema ed è necessario riavviare il PC. È in corso la raccolta di alcune informazioni sull'errore, quindi il riavvio verrà eseguito automaticamente. Per saperne di più, è possibile cercare online informazioni su questo errore in un secondo momento: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Causa

Gli errori irreversibili possono avere varie cause. Le cause più comuni sono:
- Problemi relativi a un driver
- File di sistema o memoria danneggiati
- Accesso di un'applicazione a un settore non consentito della memoria

## <a name="solution"></a>Soluzione 

Per risolvere questo problema, [contattare il supporto tecnico e inviare un file di dump](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), che consentirà di isolare il problema più rapidamente, oppure provare la soluzione di self-help seguente.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Collegare il disco del sistema operativo alla macchina virtuale di ripristino

1. Creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).
2. [Collegare il disco del sistema operativo alla macchina virtuale di ripristino](./troubleshoot-recovery-disks-portal-windows.md). 
3. Stabilire una connessione Desktop remoto alla macchina virtuale di ripristino.

### <a name="enable-dump-logs-and-serial-console"></a>Abilitare i log di dump e la console seriale

Il log di dump e la [console seriale](./serial-console-windows.md) sono utili per una risoluzione dei problemi più approfondita.

Per abilitare i log di dump e la console seriale, eseguire lo script seguente.

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire lo script seguente:

    In questo script si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la specifica macchina virtuale.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Sostituire i driver non firmati

1. Nella macchina virtuale di ripristino eseguire il comando seguente da un prompt dei comandi con privilegi elevati. Questo comando imposta il disco del sistema operativo interessato in modo da essere avviato in modalità provvisoria al prossimo avvio:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Se ad esempio il disco del sistema operativo collegato corrisponde all'unità F, eseguire il comando seguente:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Scollegare il disco del sistema operativo e quindi ricollegarlo alla macchina virtuale interessata](troubleshoot-recovery-disks-portal-windows.md). La macchina virtuale verrà avviata in modalità provvisoria. Se l'errore persiste, eseguire il passaggio facoltativo.
3. Aprire la casella **Esegui** ed eseguire **verifier** per avviare Driver Verifier Manager.
4. Fare clic su **Seleziona automaticamente i driver non firmati** e quindi su **Avanti**.
5. Si otterrà l'elenco dei file di driver non firmati. Prendere nota dei nomi dei file.
6. Copiare le stesse versioni di questi file da una macchina virtuale in esecuzione e quindi sostituire questi file non firmati. 

7. Rimuovere le impostazioni di avvio in modalità provvisoria:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Riavviare la VM. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Facoltativo: Analizzare i log di dump in modalità di dump di arresto anomalo

Per analizzare i log di dump manualmente, attenersi alla procedura seguente:

1. Collegare il disco del sistema operativo alla macchina virtuale di ripristino.
2. Nel disco del sistema operativo collegato passare alla cartella **\windows\system32\config**. Copiare tutti i file come backup, nel caso risulti necessario un ripristino dello stato precedente.
3. Avviare **Editor del Registro di sistema** (regedit.exe).
4. Selezionare la chiave **HKEY_LOCAL_MACHINE**. Scegliere **File** > **Carica hive** dal menu.
5. Passare alla cartella **\windows\system32\config\SYSTEM** nel disco del sistema operativo collegato. Immettere il nome di hive **BROKENSYSTEM**. Il nuovo hive del Registro di sistema viene visualizzato per la chiave **HKEY_LOCAL_MACHINE**.
6. Passare a **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** e apportare le modifiche seguenti:

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  Selezionare **BROKENSYSTEM**. Scegliere **File** > **Scarica hive** dal menu.
8.  Modificare i dati della configurazione di avvio in modo da avviare il sistema in modalità di debug. Eseguire i comandi seguenti da un prompt dei comandi con privilegi elevati:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Scollegare il disco del sistema operativo e quindi ricollegarlo alla macchina virtuale interessata](troubleshoot-recovery-disks-portal-windows.md).
10. Avviare la macchina virtuale per vedere se mostra l'analisi del dump. Trovare il file che non viene caricato. È necessario sostituire questo file con un file della macchina virtuale in esecuzione. 

    Di seguito è riportato un esempio di analisi del dump. È possibile notare la presenza di **FAILURE** in filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Non appena la macchina virtuale è in esecuzione e viene avviata normalmente, rimuovere le impostazioni della modalità di dump di arresto anomalo:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
