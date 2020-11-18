---
title: Errore di arresto di Windows-guasto hardware
description: Questo articolo illustra la procedura per risolvere i problemi relativi all'arresto anomalo di macchine virtuali Windows Server 2008 con un messaggio di errore che indica che si è verificato un malfunzionamento dell'hardware.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: eb4e0a246d6a33c3fad5f44b99a37997e4462f05
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663866"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Errore di arresto di Windows-guasto hardware

Questo articolo illustra la procedura per risolvere i problemi relativi all'arresto anomalo di macchine virtuali Windows Server 2008 con un messaggio di errore che indica che si è verificato un malfunzionamento dell'hardware.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che nella schermata viene visualizzata una schermata blu con il messaggio:

**\*\*\* Malfunzionamento hardware**

**Contattare il fornitore per assistenza**

**\*\*\* Il sistema è stato interrotto \*\*\***

#### <a name="blue-screen"></a>Schermata blu

![Lo screenshot mostra un arresto anomalo del sistema hardware della schermata blu.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Console seriale

![La schermata mostra il messaggio "guasto hardware" nella funzionalità console seriale se la console seriale è stata abilitata.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Causa

Questa schermata viene visualizzata quando il sistema operativo guest non è stato configurato correttamente ed è stato inviato un interrupt non mascherabile (NMI). Il messaggio di errore indica che un programma in modalità kernel ha generato un'eccezione, che non è stata intercettata dal gestore. È possibile identificare quale eccezione è stata generata raccogliendo un dump della memoria.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo 

1. Configurare la chiave del registro di sistema di interrupt non mascherabile (NMI) 
2. Creare e accedere a una macchina virtuale di ripristino 
3. Abilitare la raccolta di immagini di memoria e console seriale 
4. Ricreare la macchina virtuale 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>Configurare la chiave del registro di sistema di interrupt non mascherabile (NMI)

1. Usando il portale di Azure, riavviare la macchina virtuale in modo che il sistema operativo guest venga avviato normalmente. 
2. Una volta ripristinato l'accesso alla macchina virtuale, aprire un prompt dei comandi con privilegi elevati (Esegui come amministratore). 
3. Configurare la chiave del registro di sistema NMI con il comando seguente:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [Visualizzare altre informazioni sul comando REG ADD](https://docs.microsoft.com/windows-server/administration/windows-commands/reg-add)
4. *(Facoltativo)* Configurare la raccolta di dump della memoria:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Facoltativo)* Configurare l'accesso alla console seriale:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [Visualizzare altre informazioni sul comando BCDEDIT](https://docs.microsoft.com/windows-server/administration/windows-commands/bcdedit)
6. Riavviare la macchina virtuale con il comando seguente:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [Visualizzare altre informazioni sul comando SHUTDOWN](https://docs.microsoft.com/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> Il problema è ora corretto.

> [!NOTE]
> Dopo il riavvio, testare la macchina virtuale per assicurarsi che funzioni come di consueto. Se si verificano ancora problemi, è possibile passare alla sezione successiva per altre istruzioni.

> [!TIP]
> Si consiglia di configurare la chiave del registro di sistema di interrupt non mascherabile nella sezione precedente, tuttavia, se la macchina virtuale non è stata avviata normalmente in seguito, è possibile che non si sia verificata una modifica prevista per il registro del sistema operativo guest. In tal caso, è possibile seguire le istruzioni riportate di seguito per aggiungere manualmente le impostazioni del registro di sistema.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
2. Connettersi alla macchina virtuale di ripristino usando Connessione Desktop remoto.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Abilitare la raccolta di immagini di memoria e console seriale

Prima di ricompilare la macchina virtuale, è consigliabile abilitare la raccolta dei dump della memoria e la console seriale. A tale scopo, eseguire lo script seguente: 

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore). 
2. Elencare i dati dell'archivio BCD e determinare l'identificatore del caricatore di avvio, che verrà usato nel passaggio successivo. 
    1. Per una macchina virtuale di prima generazione, immettere il comando seguente e annotare l'identificatore elencato: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        Nel comando sostituire `<BOOT PARTITON>` con la lettera della partizione nel disco collegato che contiene la cartella di avvio. 

        ![Lo screenshot mostra l'output dell'elenco dell'archivio BCD in una macchina virtuale di prima generazione, che elenca il numero dell'identificatore nel caricatore di avvio di Windows.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. Per una macchina virtuale di seconda generazione, immettere il comando seguente e annotare l'identificatore elencato:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * Nel comando sostituire `<LETTER OF THE EFI SYSTEM PARTITION>` con la lettera della partizione di sistema EFI.
        * Potrebbe essere utile avviare la console Gestione disco per identificare la partizione di sistema appropriata etichettata come *partizione di sistema EFI*.
        * L'identificatore può essere un GUID univoco oppure il *Bootmgr* predefinito.
3. Eseguire i comandi seguenti per abilitare la console seriale:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * Nel comando sostituire `<VOLUME LETTER WHERE THE BCD FOLDER IS>` con la lettera della cartella BCD.
    * Nel comando sostituire `<BOOT LOADER IDENTIFIER>` con l'identificatore trovato nel passaggio precedente.
4. Verificare che lo spazio disponibile sul disco del sistema operativo sia superiore alla dimensione della memoria (RAM) nella macchina virtuale. 
    1. Se lo spazio disponibile nel disco del sistema operativo non è sufficiente, è necessario modificare il percorso in cui verrà creato il file di dump della memoria. Anziché creare il file sul disco del sistema operativo, è possibile fare riferimento a qualsiasi altro disco dati collegato alla VM con spazio libero sufficiente. Per modificare il percorso, sostituire **% systemroot%** con la lettera di unità (ad esempio **F:**) del disco dati nei comandi elencati di seguito. 
    2. Immettere i comandi seguenti (configurazione del dump consigliata):

    **Caricare l'hive del Registro di sistema dal disco del sistema operativo non funzionante:**

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    **Abilitare su ControlSet001:**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Abilitare su ControlSet002:**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Scaricare il disco del sistema operativo non funzionante:**

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```
### <a name="rebuild-the-virtual-machine"></a>Ricompilare la macchina virtuale

* Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) per ricreare la macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Risolvere gli errori di avvio della macchina virtuale di Azure](./boot-error-troubleshoot.md)