---
title: Abilitare o disabilitare una regola del firewall in un sistema operativo guest in una macchina virtuale di Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: ed3d89bc15f960947a48ac4364bd14f3fdf50cc2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853070"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Abilitare o disabilitare una regola del firewall in un sistema operativo Guest della macchina virtuale di Azure

Questo articolo fornisce informazioni di riferimento per la risoluzione dei problemi in una situazione in cui si sospetta che il firewall del sistema operativo guest filtri il traffico parziale in una macchina virtuale. Questo può essere utile per i motivi seguenti:

*   Se è stata apportata deliberatamente una modifica al firewall che ha causato errori di connessione RDP, l'uso della funzionalità di estensione dello script personalizzata può risolvere il problema.

*   La disabilitazione di tutti i profili firewall è un modo più sicuro di risolvere i problemi rispetto all'impostazione della regola del firewall specifica di RDP.

## <a name="solution"></a>Soluzione

La modalità di configurazione delle regole del firewall dipende dal livello di accesso alla macchina virtuale necessario. Gli esempi seguenti usano regole RDP. Gli stessi metodi possono tuttavia essere applicati a qualsiasi altro tipo di traffico specificando la chiave del Registro di sistema corretta.

### <a name="online-troubleshooting"></a>Risoluzione dei problemi in modalità online 

#### <a name="mitigation-1-custom-script-extension"></a>Mitigazione 1: Estensione di script personalizzati

1.  Creare lo script usando il modello seguente.

    *   Per abilitare una regola:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Per disabilitare una regola:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Caricare lo script nel portale di Azure usando la funzionalità di [estensione dello script personalizzata](../extensions/custom-script-windows.md). 

#### <a name="mitigation-2-remote-powershell"></a>Mitigazione 2: PowerShell remoto

Se la macchina virtuale è online ed è accessibile su un'altra macchina virtuale nella stessa rete virtuale, è possibile apportare le mitigazioni seguenti usando l'altra macchina virtuale.

1.  Nella macchina virtuale per la risoluzione dei problemi aprire una finestra della console di PowerShell.

2.  Eseguire i comandi seguenti, a seconda della situazione.

    *   Per abilitare una regola:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Per disabilitare una regola:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Mitigazione 3: Comandi PSTools

Se la macchina virtuale è online ed è accessibile su un'altra macchina virtuale nella stessa rete virtuale, è possibile apportare le mitigazioni seguenti usando l'altra macchina virtuale.

1.  Nella macchina virtuale per la risoluzione dei problemi scaricare [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Aprire un'istanza di CMD e accedere alla macchina virtuale tramite il relativo indirizzo IP interno. 

    * Per abilitare una regola:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Per disabilitare una regola:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Mitigazione 4: Registro di sistema remoto

Se la macchina virtuale è online ed è accessibile su un'altra macchina virtuale nella stessa rete virtuale, è possibile usare il [Registro di sistema remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) nell'altra macchina virtuale.

1.  Nella macchina virtuale per la risoluzione dei problemi avviare l'editor del Registro di sistema (regedit.exe) e quindi selezionare **File** > **Connetti a Registro di sistema in rete**.

2.  Aprire il ramo  *COMPUTER DI DESTINAZIONE*\SYSTEM e quindi specificare i valori seguenti:

    * Per abilitare una regola, aprire il valore del Registro di sistema seguente:
    
        *COMPUTER DI DESTINAZIONE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Cambiare quindi **Active=FALSE** in **Active=TRUE** nella stringa:

        **v2.22 | Azione = consentire | Attiva = TRUE | Dir = In | Protocollo = 6 | Profilo = Domain | Profilo = privata | Profilo = Public | LPort = 3389 | App=%Systemroot%\System32\Svchost.exe| SVC = termservice | Nome =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**
    
    * Per disabilitare una regola, aprire il valore del Registro di sistema seguente:
    
        *COMPUTER DI DESTINAZIONE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Cambiare quindi **Active=TRUE** in **Active=FALSE**:
        
        **v2.22 | Azione = consentire | Attiva = FALSE | Dir = In | Protocollo = 6 | Profilo = Domain | Profilo = privata | Profilo = Public | LPort = 3389 | App=%Systemroot%\System32\Svchost.exe| SVC = termservice | Nome =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

3.  Riavviare la macchina virtuale per applicare le modifiche.

### <a name="offline-troubleshooting"></a>Risoluzione dei problemi in modalità offline 

Se non è possibile accedere alla macchina virtuale con uno di questi metodi, l'uso dell'estensione dello script personalizzata avrà esito negativo e sarà necessario passare in modalità OFFLINE lavorando direttamente sul disco di sistema.

Prima di seguire questa procedura, creare uno snapshot del disco di sistema della macchina virtuale interessata come backup. Per altre informazioni, vedere  [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

1.  [Collegare il disco di sistema a una macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md).

2.  Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.

3.  Verificare che il disco sia contrassegnato come  **Online**  nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco di sistema collegato.

4.  Prima di apportare qualsiasi modifica, creare una copia della cartella \windows\system32\config nel caso in cui sia necessario un ripristino dello stato precedente.

5.  Nella macchina virtuale per la risoluzione dei problemi avviare l'editor del Registro di sistema (regedit.exe).

6.  Evidenziare la chiave **HKEY_LOCAL_MACHINE**  e quindi scegliere  **File** > **Carica hive**  dal menu.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Individuare e aprire il file \windows\system32\config\SYSTEM. 

    > [!Note]
    > Viene chiesto di immettere un nome. Immettere  **BROKENSYSTEM** e quindi espandere  **HKEY_LOCAL_MACHINE**. Si vedrà una chiave aggiuntiva denominata  **BROKENSYSTEM**. Per la risoluzione di questo problema si monteranno gli hive problematici come  **BROKENSYSTEM**.

8.  Apportare le modifiche seguenti al ramo BROKENSYSTEM:

    1.  Verificare da quale chiave del Registro di sistema **ControlSet** viene avviata la macchina virtuale. Il numero di chiave è disponibile in HKLM\BROKENSYSTEM\Select\Current.

    2.  Per abilitare una regola, aprire il valore del Registro di sistema seguente:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Cambiare quindi **Active=FALSE** in **Active=TRUE**.
        
        **v2.22 | Azione = consentire | Attiva = TRUE | Dir = In | Protocollo = 6 | Profilo = Domain | Profilo = privata | Profilo = Public | LPort = 3389 | App=%Systemroot%\System32\Svchost.exe| SVC = termservice | Nome =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

    3.  Per disabilitare una regola, aprire la chiave del Registro di sistema seguente:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Cambiare quindi **Active=TRUE** in **Active=FALSE**.
        
        **v2.22 | Azione = consentire | Attiva = FALSE | Dir = In | Protocollo = 6 | Profilo = Domain | Profilo = privata | Profilo = Public | LPort = 3389 | App=%Systemroot%\System32\Svchost.exe| SVC = termservice | Nome =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

9.  Evidenziare  **BROKENSYSTEM** e quindi scegliere  **File** > **Scarica hive**  dal menu.

10. [Scollegare il disco di sistema e creare di nuovo la macchina virtuale](troubleshoot-recovery-disks-portal-windows.md).

11. Controllare se il problema è stato risolto.
