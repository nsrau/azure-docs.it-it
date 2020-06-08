---
title: La macchina virtuale di Azure non risponde durante l'applicazione dei criteri
description: Questo articolo illustra la procedura da seguire per risolvere i problemi in cui la schermata di caricamento si blocca quando si applica un criterio durante l'avvio in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748730"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>La macchina virtuale smette di rispondere quando si applica il criterio "Utenti e gruppi locali di Criteri di gruppo"

Questo articolo illustra la procedura da seguire per risolvere i problemi in cui la schermata di caricamento si blocca quando si applica un criterio durante l'avvio in una macchina virtuale di Azure.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare uno screenshot della macchina virtuale, il caricamento della schermata si interrompe e un messaggio indica che *viene applicato il criterio Utenti e gruppi locali di Criteri di gruppo*.

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Screenshot dell'applicazione del caricamento del criterio Utenti e gruppi locali di Criteri di gruppo (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Screenshot dell'applicazione del caricamento del criterio Utenti e gruppi locali di Criteri di gruppo (Windows Server 2012).":::

## <a name="cause"></a>Causa

Sono presenti blocchi in conflitto quando il criterio tenta di pulire i vecchi profili utente.

> [!NOTE]
> Questo è valido solo per Windows Server 2012 e Windows Server 2012 R2.

Questo è il criterio problematico:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Risoluzione

### <a name="process-overview"></a>Panoramica del processo

1. [Creare e accedere a una macchina virtuale di ripristino](#step-1-create-and-access-a-repair-vm)
2. [Disabilitare il criterio](#step-2-disable-the-policy)
3. [Abilitare la console seriale e la raccolta di dump della memoria](#step-3-enable-serial-console-and-memory-dump-collection)
4. [Ricreare la macchina virtuale](#step-4-rebuild-the-vm)

> [!NOTE]
> Se si verifica questo errore di avvio, il sistema operativo guest non è operativo. Il problema deve essere risolto in modalità offline.

### <a name="step-1-create-and-access-a-repair-vm"></a>Passaggio 1: Creare e accedere a una macchina virtuale di ripristino

1. Usare i [passaggi da 1 a 3 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per preparare una macchina virtuale di ripristino.
2. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="step-2-disable-the-policy"></a>Passaggio 2: Disabilitare il criterio

1. Nella macchina virtuale di ripristino aprire l'editor del Registro di sistema.
2. Individuare la chiave **HKEY_LOCAL_MACHINE** e selezionare **File** > **Carica hive** dal menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Screenshot con HKEY_LOCAL_MACHINE evidenziato e il menu che contiene Carica hive.":::

    - Carica hive consente di caricare le chiavi del Registro di sistema da un sistema offline, in questo caso il disco danneggiato collegato alla macchina virtuale di ripristino.
    - Le impostazioni a livello di sistema vengono archiviate in `HKEY_LOCAL_MACHINE` e possono essere abbreviate in "HKLM".
3. Nel disco collegato andare al file `\windows\system32\config\SOFTWARE` e aprirlo.

    1. Verrà richiesto di indicare un nome. Immettere BROKENSOFTWARE.<br/>
    2. Per verificare che BROKENSOFTWARE sia stato caricato, espandere **HKEY_LOCAL_MACHINE** e cercare la chiave BROKENSOFTWARE aggiunta.
4. Passare a BROKENSOFTWARE e verificare se la chiave CleanupProfile esiste nell'hive caricato.

    1. Se la chiave esiste, viene impostato il criterio CleanupProfile, il cui valore rappresenta il criterio di conservazione in giorni. Continuare a eliminare la chiave.<br/>
    2. Se la chiave non esiste, il criterio CleanupProfile non viene impostato. [Inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), includendo il file memory.dmp che si trova nella directory Windows del disco del sistema operativo collegato.

5. Eliminare la chiave CleanupProfiles usando il comando seguente:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Scaricare l'hive BROKENSOFTWARE usando questo comando:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Passaggio 3: Abilitare la console seriale e la raccolta di dump della memoria

Per abilitare la raccolta di dump della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti.

    **Abilitare la console seriale**: 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Verificare se lo spazio disponibile sul disco del sistema operativo è almeno uguale alla dimensione della memoria (RAM) della macchina virtuale.

    Se lo spazio disponibile nel disco del sistema operativo non è sufficiente, modificare il percorso dei dump della memoria e fare riferimento a un disco dati collegato con spazio libero sufficiente. Per modificare il percorso, sostituire "%SystemRoot%" con la lettera di unità (ad esempio "F:") del disco dati nei comandi seguenti.

    **Configurazione consigliata per abilitare il dump del sistema operativo**:

    Caricare disco del sistema operativo non funzionante:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Abilitare su ControlSet001:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Abilitare su ControlSet002:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Scaricare disco del sistema operativo non funzionante:
    
    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>Passaggio 4: Ricreare la macchina virtuale

Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per riassemblare la macchina virtuale.

Se il problema è stato risolto, il criterio è stato disabilitato localmente. Per una soluzione permanente, non usare i criteri di CleanupProfiles nelle macchine virtuali. Usare un metodo diverso per eseguire la pulizia del profilo.

Non usare questo criterio:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi quando si applica Windows Update, vedere l'articolo sulla [mancata risposta della macchina virtuale con errore "C01A001D" durante l'applicazione di Windows Update](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).