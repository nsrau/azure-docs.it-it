---
title: Non è possibile stabilire una connessione remota a Macchine virtuali di Azure perché la macchina virtuale viene avviata in modalità provvisoria | Microsoft Docs
description: Informazioni su come risolvere un problema dovuto all'impossibilità di stabilire una connessione RDP a una macchina virtuale perché questa viene avviata in modalità provvisoria.| Microsoft Docs
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
ms.openlocfilehash: 0ef4aa988f4adc855051b213013636b4a04f1cca
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316982"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Non è possibile stabilire una connessione RDP a una macchina virtuale perché questa viene avviata in modalità provvisoria

Questo articolo illustra come risolvere un problema che impedisce di stabilire una connessione a macchine virtuali Windows di Azure perché la macchina virtuale è configurata per l'avvio in modalità provvisoria.

> [!NOTE]
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'utilizzo del modello di distribuzione Resource Manager, che Microsoft consiglia di usare per le nuove distribuzioni in sostituzione del modello di distribuzione classica.

## <a name="symptoms"></a>Sintomi

Non è possibile stabilire una connessione RDP o di altro tipo (ad esempio HTTP) a una macchina virtuale di Azure perché questa è configurata per l'avvio in modalità provvisoria. Controllando lo screenshot in [Diagnostica di avvio](../troubleshooting/boot-diagnostics.md) nel portale di Azure, si può vedere che la macchina virtuale viene avviata normalmente, ma l'interfaccia di rete non è disponibile:

![Immagine relativa all'interfaccia di rete in modalità provvisoria](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Causa

Il servizio RDP non è disponibile in modalità provvisoria. Quando la macchina virtuale viene avviata in modalità provvisoria, vengono caricati solo i servizi e i programmi di sistema essenziali. Questo vale per le due diverse versioni della modalità provvisoria, ovvero "Avvio in modalità provvisoria minima" e "Avvio in modalità provvisoria con connettività".


## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

Per risolvere questo problema, usare il controllo seriale per configurare la macchina virtuale per l'avvio in modalità normale o [riparare la macchina virtuale in modalità offline](#repair-the-vm-offline) usando una macchina virtuale di ripristino.

### <a name="use-serial-control"></a>Usare il controllo seriale

1. Connettersi alla [console seriale e aprire un'istanza CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se la console seriale non è abilitata sulla macchina virtuale, vedere [Riparare la macchina virtuale in modalità offline](#repair-the-vm-offline).
2. Controllare i dati della configurazione di avvio:

        bcdedit /enum

    Se la macchina virtuale è configurata per l'avvio in modalità provvisoria, nella sezione **Windows Boot Loader** verrà visualizzato un flag aggiuntivo denominato **safeboot**. Se il flag **safeboot** non viene visualizzato, la macchina virtuale non è in modalità provvisoria. Questo articolo non si applica allo specifico scenario.

    Il flag **safeboot** può avere uno dei valori seguenti:
    - Minime
    - Rete

    In entrambe queste modalità, la connessione RDP non viene avviata e pertanto la correzione è la stessa.

    ![Immagine del flag relativo alla modalità provvisoria](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Eliminare il flag **safemode** in modo che la macchina virtuale venga avviata in modalità normale:

        bcdedit /deletevalue {current} safeboot

4. Controllare i dati della configurazione di avvio per assicurarsi che il flag **safeboot** sia stato rimosso:

        bcdedit /enum

5. Riavviare la macchina virtuale e quindi verificare se il problema è stato risolto.

### <a name="repair-the-vm-offline"></a>Riparare la macchina virtuale in modalità offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Collegare il disco del sistema operativo alla macchina virtuale di ripristino

1. [Collegare il disco del sistema operativo alla macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md).
2. Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.
3. Verificare che il disco sia contrassegnato come **Online** nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco del sistema operativo collegato.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Abilitare il log del dump e la console seriale (facoltativo)

Il log del dump e la console seriale saranno utili per eseguire un altro tentativo di risoluzione del problema se questo non può essere risolto tramite la soluzione illustrata in questo articolo.

Per abilitare il log Dump e la console seriale, eseguire lo script seguente.

1. Aprire una sessione del prompt dei comandi con privilegi elevati (**Esegui come amministratore**).
2. Eseguire lo script seguente:

    In questo script si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la specifica macchina virtuale.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Configurare Windows per l'avvio in modalità normale

1. Aprire una sessione del prompt dei comandi con privilegi elevati (**Esegui come amministratore**).
2. Controllare i dati della configurazione di avvio. Nei comandi seguenti si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la specifica macchina virtuale.

        bcdedit /store F:\boot\bcd /enum
    Prendere nota del nome dell'identificatore della partizione che contiene la cartella **\windows**. Per impostazione predefinita, il nome dell'identificatore è "Default".

    Se la macchina virtuale è configurata per l'avvio in modalità provvisoria, nella sezione **Windows Boot Loader** verrà visualizzato un flag aggiuntivo denominato **safeboot**. Se non viene visualizzato il flag **safeboot**, questo articolo non si applica allo specifico scenario.

    ![Immagine relativa all'identificatore di avvio](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Rimuovere il flag **safeboot** in modo che la macchina virtuale venga avviata in modalità normale:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Controllare i dati della configurazione di avvio per assicurarsi che il flag **safeboot** sia stato rimosso:

        bcdedit /store F:\boot\bcd /enum
5. [Scollegare il disco del sistema operativo e ricreare la macchina virtuale](../windows/troubleshoot-recovery-disks-portal.md). Controllare quindi se il problema è stato risolto.
