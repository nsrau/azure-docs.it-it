---
title: Servizi Desktop remoto non si avvia in una macchina virtuale di Azure| Microsoft Docs
description: Informazioni su come risolvere i problemi di Servizi Desktop remoto quando ci si connette a una macchina virtuale | Microsoft Docs
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989024"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Servizi Desktop remoto non si avvia in una macchina virtuale di Azure

Questo articolo descrive come risolvere i problemi di connessione a una macchina virtuale (VM) di Azure quando Servizi Desktop remoto (TermService) non si avvia oppure se si verifica un errore di avvio.

>[!NOTE]
>Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Resource Manager e la distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione Resource Manager. Invece del modello di distribuzione classica, per le nuove distribuzioni è consigliabile usare questo modello.

## <a name="symptoms"></a>Sintomi

Quando si prova a connettersi a una macchina virtuale, si verificano gli scenari seguenti:

- Lo screenshot della macchina virtuale indica che il sistema operativo è completamente caricato ed è in attesa delle credenziali.
- Tutte le applicazioni nella macchina virtuale funzionano come previsto e sono accessibili.
- La macchina virtuale sta rispondendo alle richieste di connettività TCP sulla porta Microsoft RDP (Remote Desktop Protocol) (impostazione predefinita 3389).
- Non vengono chieste le credenziali quando si prova a stabilire una connessione RDP.

## <a name="cause"></a>Causa

Questo problema si verifica perché Servizi Desktop remoto non è in esecuzione nella macchina virtuale. La causa dipende dagli scenari seguenti:

- Il servizio TermService è stato impostato su **Disattivato**.
- Il servizio TermService si arresta in modo anomalo o si blocca.

## <a name="solution"></a>Soluzione

Per risolvere questo problema, usare una delle soluzioni seguenti o provare le soluzioni a una a una:

### <a name="solution-1-using-the-serial-console"></a>Soluzione 1: uso della console seriale

1. Accedere alla [console seriale](serial-console-windows.md) selezionando **Supporto e risoluzione dei problemi** > **Console seriale (anteprima)**. Se la funzionalità è abilitata nella macchina virtuale, è possibile connettere correttamente la macchina virtuale.

2. Creare un nuovo canale per un'istanza CMD. Digitare **CMD** per avviare il canale per ottenere il relativo nome.

3. Passare al canale che esegue l'istanza CMD, in questo caso il canale 1.

   ```
   ch -si 1
   ```

4. Premere **INVIO** di nuovo e digitare un nome utente valido e una password (ID locale o di dominio) per la macchina virtuale.

5. Eseguire una query sullo stato del servizio TermService.

   ```
   sc query TermService
   ```

6. Se lo stato del servizio è indicato come **Arrestato**, provare ad avviare il servizio.

   ```
   sc start TermService
   ```

7. Eseguire di nuovo una query sul servizio per assicurarsi che il servizio sia stato avviato correttamente.

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>Soluzione 2: uso di una macchina virtuale di ripristino per abilitare il servizio

[Eseguire il backup del disco del sistema operativo](../windows/snapshot-copy-managed-disk.md) e [collegare il disco del sistema operativo a una macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md). Aprire quindi un'istanza di CMD con privilegi elevati ed eseguire gli script seguenti nella macchina virtuale di ripristino:

>[!NOTE]
>Si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la macchina virtuale in uso. Dopo avere eseguito questa operazione, scollegare il disco dalla macchina virtuale di ripristino e [ricreare la macchina virtuale](../windows/create-vm-specialized.md). Per altre operazioni di risoluzione dei problemi, è possibile usare la **soluzione 1**, perché la console seriale è stata abilitata.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
