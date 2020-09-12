---
title: La macchina virtuale non risponde durante l'applicazione dei criteri di configurazione dei criteri di controllo
description: Questo articolo illustra la procedura per risolvere i problemi in cui la macchina virtuale (VM) smette di rispondere applicando i criteri di configurazione dei criteri di controllo, impedendo l'avvio di una macchina virtuale di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: bc41783bf977806b5f9bba5b953f1f581ad07f18
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299525"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>La macchina virtuale non risponde durante l'applicazione dei criteri di configurazione dei criteri di controllo

Questo articolo illustra la procedura per risolvere i problemi in cui la macchina virtuale (VM) smette di rispondere applicando i criteri di configurazione dei criteri di controllo, impedendo l'avvio di una macchina virtuale di Azure.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare lo screenshot della macchina virtuale, si noterà che lo screenshot Visualizza che il sistema operativo non risponde durante un avvio con il messaggio applicazione dei criteri di configurazione dei criteri di **controllo**.

  ![Avvio del sistema operativo con il messaggio: "applicazione dei criteri di configurazione dei criteri di controllo"](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Avvio del sistema operativo in Windows Server 2012 con il messaggio: "applicazione dei criteri di configurazione dei criteri di controllo"](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Causa

Sono presenti blocchi in conflitto quando i criteri tentano di pulire i vecchi profili utente.

> [!NOTE]
> Questo è valido solo per Windows Server 2012 e Windows Server 2012 R2.

Ecco i criteri problematici: *computer Configurazione computer\Modelli \ System/User Profiles\Delete profili utente più vecchi di un numero specificato di giorni al riavvio del sistema.*

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. Creare e accedere a una macchina virtuale di ripristino.
1. Disabilitare il criterio.
1. Abilitare la console seriale e la raccolta di dump della memoria.
1. Ricreare la macchina virtuale.
1. Raccogliere il file di dump della memoria e inviare un ticket di supporto.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) per preparare una macchina virtuale di ripristino.
1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="disable-the-policy"></a>Disabilitare il criterio

1. Nella macchina virtuale di ripristino aprire l' **Editor del registro di sistema**.
1. Individuare la chiave **HKEY_LOCAL_MACHINE** e selezionare **file > Load hive** dal menu.

   ![Navigazione all'interno dell'editor del registro di sistema per caricare un hive.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - È possibile usare Load hive per caricare chiavi del registro di sistema da un sistema offline. In questo caso, il sistema è il disco rotto collegato alla macchina virtuale di ripristino.
   - Le impostazioni a livello di sistema vengono archiviate in **HKEY_LOCAL_MACHINE** e possono essere abbreviate come **HKLM**.

1. Nel disco collegato aprire il `\windows\system32\config\SOFTWARE` file.

   - Quando viene richiesto un nome, immettere **BROKENSOFTWARE**.
   - Per verificare che **BROKENSOFTWARE** sia stato caricato, espandere **HKEY_LOCAL_MACHINE** e cercare la chiave **BROKENSOFTWARE** aggiunta.

1. Passare a **BROKENSOFTWARE** e verificare se la chiave **CleanupProfiles** esiste nell'hive caricato.

   - Se la chiave esiste, viene impostato il criterio **CleanupProfiles** . Il valore rappresenta i criteri di conservazione misurati in giorni.
   - Se la chiave non esiste, il criterio **CleanupProfiles** non è impostato. In questa situazione, passare a [inviare un ticket di supporto con un file di dump della memoria](#collect-the-memory-dump-file-and-submit-a-support-ticket).

1. Eliminare la chiave **CleanupProfiles** usando il comando seguente:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Scaricare l'hive **BROKENSOFTWARE** usando il comando seguente:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Abilitare la console seriale e la raccolta di dump della memoria

**Consigliato**: Prima di ricreare la macchina virtuale, abilitare la console seriale e la raccolta di dump della memoria eseguendo lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati come amministratore.
1. Elencare i dati dell'archivio BCD e determinare l'identificatore del caricatore di avvio, che verrà usato nel passaggio successivo.

   1. Per una macchina virtuale di prima generazione, immettere il comando seguente e annotare l'identificatore elencato:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - Nel comando sostituire `<BOOT PARTITON>` con la lettera della partizione nel disco collegato che contiene la cartella di avvio.

        ![Nella figura 4 viene illustrato l'output di un elenco dell'archivio BCD in una macchina virtuale di prima generazione, che elenca il numero dell'identificatore nel caricatore di avvio di Windows.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. Per una macchina virtuale di seconda generazione, immettere il comando seguente e annotare l'identificatore elencato:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - Nel comando sostituire `<LETTER OF THE EFI SYSTEM PARTITION>` con la lettera della partizione di sistema EFI.
      - Potrebbe essere utile avviare la console Gestione disco per identificare la partizione di sistema appropriata etichettata come **partizione di sistema EFI**.
      - L'identificatore può essere un GUID univoco oppure il **Bootmgr**predefinito.

1. Eseguire i comandi seguenti:

   **Abilitare la console seriale**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Verificare che lo spazio disponibile sul disco del sistema operativo sia superiore alla dimensione della memoria (RAM) nella macchina virtuale.

   Se lo spazio disponibile nel disco del sistema operativo non è sufficiente, modificare il percorso in cui verrà creato il file di dump della memoria e fare in modo che quella posizione faccia riferimento a qualsiasi disco dati collegato alla macchina virtuale con spazio libero sufficiente. Per modificare il percorso, sostituire **%SystemRoot%** con la lettera di unità del disco dati, ad esempio **F:** , nei comandi seguenti.

   Configurazione consigliata per abilitare il dump del sistema operativo:

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

1. Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per ricreare la macchina virtuale.

1. Verificare se la macchina virtuale viene avviata normalmente per verificare se il problema è stato risolto.

   - Se il problema non è stato risolto, continuare a [raccogliere un file di dump e inviare un ticket di supporto](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Se il problema è stato risolto, non sono necessari altri passaggi.

Se il problema è stato risolto, il criterio è ora disabilitato localmente. Per una soluzione permanente, non usare il criterio CleanupProfiles nelle VM perché eliminerà automaticamente i profili utente. Usare un metodo diverso per eseguire le pulizie del profilo, ad esempio uno script o un'attività pianificata.

**Non usare questo criterio:** 
 *Machine\Admin amministrativi\Sistema\Profili utente Profiles\Delete i profili utente più vecchi di un numero specificato di giorni al riavvio del sistema.*

### <a name="the-issue-should-now-be-fixed"></a>Il problema è ora corretto

Testare la VM per assicurarsi che funzioni come di consueto. Se si verificano ancora problemi, è possibile continuare con la sezione successiva per ulteriore assistenza.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>Raccogli il file di dump della memoria e invia un ticket di supporto

Per risolvere il problema, è necessario innanzitutto raccogliere il file di dump della memoria per l'arresto anomalo e quindi contattare il supporto tecnico con il file di dump della memoria. Per raccogliere il file di dump, attenersi alla procedura seguente:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Usare i passaggi 1-3 dei [comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) per preparare una nuova macchina virtuale di ripristino.
1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino andare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di driver assegnata al disco del sistema operativo collegato è denominata *F*, è necessario passare a `F:\Windows` .
1. Individuare il `memory.dmp` file e quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria.
1. Se si verificano problemi durante l'individuazione del `memory.dmp` file, usare invece le [chiamate di interrupt non mascherabili (NMI) nella console seriale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Seguire la guida per [generare un file di dump di arresto anomalo del sistema usando le chiamate a NMI qui](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).
