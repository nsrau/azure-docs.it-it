---
title: Risolvere un errore generale RDP in una VM Windows in Azure| Microsoft Docs
description: Informazioni su come risolvere un errore generale RDP in una VM Windows in Azure| Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f996ffa864fb4178ddedecde7c5511d5d9cf39a1
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985807"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Risolvere un errore generale RDP in una VM di Azure

Questo articolo descrive un errore generale che può verificarsi quando si effettua una connessione RDP (Remote Desktop Protocol) a una macchina virtuale Windows (VM) in Azure.

## <a name="symptom"></a>Sintomo

Quando si effettua una connessione RDP a una macchina virtuale Windows in Azure, è possibile che venga visualizzato il messaggio di errore generale seguente:

**Il desktop remoto non può connettersi al computer remoto per uno di questi motivi:**

1. **L'accesso remoto al server non è abilitato**

2. **Il computer remoto è stato disattivato**

3. **Il computer remoto non è disponibile sulla rete**

**Verificare che il computer remoto sia acceso e connesso alla rete e che l'accesso remoto sia abilitato.**

## <a name="cause"></a>Causa

Questo problema può verificarsi per le cause seguenti:

### <a name="cause-1"></a>Causa 1

Il componente RDP è stato disabilitato come segue:

- A livello di componente
- A livello di listener
- Sul server terminal
- Sul ruolo host sessione Desktop remoto

### <a name="cause-2"></a>Causa 2

Servizi Desktop remoto (TermService) non è in esecuzione.

### <a name="cause-3"></a>Causa 3

Il listener RDP non è configurato correttamente.

## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per risolvere il problema, usare il controllo seriale o ripristinare la macchina virtuale offline.

### <a name="serial-console"></a>Console seriale

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Passaggio 1: Aprire un'istanza di CMD nella console seriale

1. Accedere alla [console seriale](serial-console-windows.md) selezionando **Supporto e risoluzione dei problemi** > **Console seriale (anteprima)**. Se la funzionalità è abilitata nella macchina virtuale, è possibile connettere correttamente la macchina virtuale.

2. Creare un nuovo canale per un'istanza CMD. Digitare **CMD** per avviare il canale per ottenere il relativo nome.

3. Passare al canale che esegue l'istanza CMD, in questo caso il canale 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Passaggio 2: Verificare i valori delle chiavi del Registro di sistema di RDP:

1. Controllare se il protocollo RDP è disabilitato da criteri di gruppo.

    ```
    REM Get the group policy 
    reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
    ```
    Se il criterio di gruppo indica che il protocollo RDP è disabilitato (il valore di fDenyTSConnections è 0x1), eseguire il comando seguente per abilitare il servizio TermService. Se la chiave del registro di sistema non viene trovata, non sono presenti criteri di gruppo configurati per disabilitare il protocollo RDP. È possibile passare al passaggio successivo.

    ```
    REM update the fDenyTSConnections value to enable TermService service
    reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f
    ```
    > [!NOTE]
    > Questo passaggio abilita temporaneamente il servizio TermService. La modifica verrà reimpostata quando vengono aggiornate le impostazioni di criteri di gruppo. Per risolvere il problema, è necessario controllare se il servizio TermService è disabilitato in base ai criteri di gruppo locali o ai criteri di gruppo del dominio e quindi aggiornare le impostazioni dei criteri corrispondenti.
    
2. Controllare la configurazione della connessione remota corrente.
    ```
    REM Get the local remote connection setting
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections
    ```
    Se il comando restituisce 0x1, la macchina virtuale non consente la connessione remota. Quindi, consentire la connessione remota usando il comando seguente:
     ```
     reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
     ```
    
1. Controllare la configurazione corrente del server terminal.

    ```
    REM Get the local remote connection setting
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
    ```

      Se il comando restituisce 0, il server terminal è disabilitato. Quindi, abilitare il server terminal come segue:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Il modulo Server terminal è impostato sulla modalità di svuotamento se il server si trova in una server farm (RDS o Citrix). Controllare la configurazione corrente del modulo Server Terminal.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Se il comando restituisce 1, il modulo Server Terminal è impostato sulla modalità di svuotamento. Impostare quindi il modulo sulla modalità di lavoro come indicato di seguito:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Verificare se è possibile connettersi al server terminal.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Se il comando restituisce 1, non è possibile connettersi al server terminal. Abilitare quindi la connessione come indicato di seguito:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Controllare la configurazione corrente del listener RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Se il comando restituisce 0, il listener RDP è disabilitato. Abilitare quindi il listener nel modo seguente:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Verificare se è possibile connettersi al listener RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Se il comando restituisce 1, non è possibile connettersi al listener RDP. Abilitare quindi la connessione come indicato di seguito:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Riavviare la VM.

8. Uscire dall'istanza CMD digitando `exit` e quindi premere **INVIO** due volte.

9. Riavviare la macchina virtuale digitando `restart` e quindi connettersi alla macchina virtuale.

Se il problema persiste, procedere al passaggio 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Passaggio 2: Abilitare i servizi desktop remoto

Per altre informazioni vedere [Servizi Desktop remoto non si avvia in una macchina virtuale di Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Passaggio 3: Reimpostare il listener RDP

Per altre informazioni vedere [Remote Desktop disconnects frequently in Azure VM](troubleshoot-rdp-intermittent-connectivity.md) (Servizi Desktop remoto non si avvia in una macchina virtuale di Azure).

### <a name="offline-repair"></a>Riparazione non in linea

#### <a name="step-1-turn-on-remote-desktop"></a>Passaggio 1: Attivare il desktop remoto

1. [Alleghi il disco del sistema operativo a una macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md).
2. Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.
3. Verificare che il disco sia contrassegnato come **online** nella console Gestione disco. Prendere nota della lettera di unità assegnata al disco del sistema operativo collegato.
4. Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.
5. Aprire una sessione del prompt dei comandi con privilegi elevati (**Esegui come amministratore**). Eseguire questi script. In questo script si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la specifica macchina virtuale.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Se la macchina virtuale è aggiunta a un dominio, controllare la chiave del Registro di sistema seguente per verificare se esistono criteri di gruppo che disabilitano RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Se questo valore della chiave è impostato su 1, significa che RDP è stato disabilitato dai criteri. Per abilitare Desktop remoto tramite l'oggetto Criteri di gruppo, modificare i criteri seguenti dal controller di dominio:

   
      **Configurazione computer\Criteri\Modelli amministrativi:**

      Definizioni criteri\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni\Consenti la connessione remota tramite Servizi Desktop remoto
  
1. Scollegare il disco dalla macchina virtuale di ripristino.
1. [Creare una nuova macchina virtuale dal disco](../windows/create-vm-specialized.md).

Se il problema persiste, procedere al passaggio 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Passaggio 2: Abilitare i servizi desktop remoto

Per altre informazioni vedere [Servizi Desktop remoto non si avvia in una macchina virtuale di Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Passaggio 3: Reimpostare il listener RDP

Per altre informazioni vedere [Remote Desktop disconnects frequently in Azure VM](troubleshoot-rdp-intermittent-connectivity.md) (Servizi Desktop remoto non si avvia in una macchina virtuale di Azure).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
