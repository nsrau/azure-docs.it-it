---
title: Il firewall del sistema operativo guest della macchina virtuale di Azure blocca il traffico in ingresso | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 6e90b164fac4ea1123f5f9a43eea1169d93d9a04
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154034"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Il firewall del sistema operativo guest della macchina virtuale di Azure blocca il traffico in ingresso

Questo articolo illustra come risolvere il problema di connessione RDP (Remote Desktop Protocol) che si verifica se il firewall del sistema operativo guest blocca il traffico in ingresso.

## <a name="symptoms"></a>Sintomi

Non è possibile stabilire una connessione RDP a una macchina virtuale di Azure. Dalla schermata iniziale visualizzata in Diagnostica di avvio -> Screenshot risulta che il sistema operativo è completamente caricato (CTRL+ALT+CANC).

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

La regola RDP non è configurata per consentire il traffico RDP.

### <a name="cause-2"></a>Causa 2

I profili firewall del sistema guest sono configurati in modo da bloccare tutte le connessioni in ingresso, incluso il traffico RDP.

![Impostazioni del firewall](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, creare uno snapshot del disco di sistema della macchina virtuale interessata come backup. Per altre informazioni, vedere  [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

Per risolvere il problema, usare uno dei metodi descritti in [Usare gli strumenti remoti per risolvere i problemi delle macchine virtuali di Azure](remote-tools-troubleshoot-azure-vm-issues.md) per connettersi in remoto alla macchina virtuale e quindi modificare le regole del firewall del sistema operativo guest per **consentire** il traffico RDP.

### <a name="online-troubleshooting"></a>Risoluzione dei problemi in modalità online

Connettersi alla [console seriale e quindi aprire un'istanza di PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se la console seriale non è abilitata sulla macchina virtuale, passare a [Riparare la macchina virtuale in modalità offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Mitigazione 1

1.  Se l'agente di Azure è installato e funziona correttamente nella macchina virtuale, è possibile usare l'opzione "Reimposta solo configurazione" in **Supporto e risoluzione dei problemi** > **Reimposta password** nel menu della macchina virtuale.

2.  Questa opzione di ripristino esegue le operazioni seguenti:

    *   Abilita un componente RDP se è disabilitato.

    *   Abilita tutti i profili di Windows Firewall.

    *   Verificare che in Windows Firewall sia attivata la regola RDP.

    *   Se i passaggi precedenti non hanno alcun effetto, è necessario reimpostare manualmente la regola del firewall. A tale scopo, eseguire una query per tutte le regole che contengono il nome "Remote Desktop" con il comando seguente:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Se come porta RDP è stata impostata una porta diversa dalla 3389, è necessario trovare un'eventuale regola personalizzata creata e impostata su tale porta. Per eseguire una query per tutte le regole in ingresso che contengono una porta personalizzata, usare il comando seguente:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Se si nota che la regola è disabilitata, abilitarla. Per aprire un intero gruppo, ad esempio il gruppo Remote Desktop predefinito, eseguire il comando seguente:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    In alternativa, per aprire la specifica regola Desktop remoto (TCP-In), eseguire il comando seguente:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Per risolvere il problema, è possibile impostare i profili firewall su OFF:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Dopo aver completato la procedura di risoluzione dei problemi e impostato il firewall in modo corretto, riabilitare il firewall.

    > [!Note]
    > Non è necessario riavviare la macchina virtuale per applicare queste modifiche.

5.  Provare a stabilire una connessione RDP per accedere alla macchina virtuale.

#### <a name="mitigation-2"></a>Mitigazione 2

1.  Eseguire una query sui profili firewall per determinare se il criterio firewall in ingresso è impostato su  *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![AllProfiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Le linee guida seguenti si applicano al criterio firewall, a seconda della relativa configurazione:
    >    * *BlockInbound*: tutto il traffico in ingresso verrà bloccato, a meno che non sia attiva una regola per consentire il traffico.
    >    * *BlockInboundAlways*: tutte le regole del firewall verranno ignorate e tutto il traffico verrà bloccato.

2.  Modificare  *DefaultInboundAction* per impostare questi profili in modo da **consentire** il traffico. A tale scopo, usare il comando seguente:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Eseguire di nuovo una query sui profili per verificare che la modifica sia stata applicata. A tale scopo, usare il comando seguente:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Non è necessario riavviare la macchina virtuale per applicare le modifiche.

4.  Provare di nuovo ad accedere alla macchina virtuale tramite RDP.

### <a name="offline-mitigations"></a>Procedure di mitigazione offline

1.  [Collegare il disco di sistema a una macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md).

2.  Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.

3.  Verificare che il disco sia contrassegnato come  **Online**  nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco di sistema collegato.

#### <a name="mitigation-1"></a>Mitigazione 1

Consultare  [Abilitare o disabilitare una regola del firewall in un sistema operativo guest](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Mitigazione 2

1.  [Collegare il disco di sistema a una macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md).

2.  Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.

3.  Dopo aver collegato il disco di sistema alla macchina virtuale di ripristino, verificare che il disco sia contrassegnato come  **Online** nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco del sistema operativo collegato.

4.  Aprire un'istanza di CMD con privilegi elevati e quindi eseguire lo script seguente:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Scollegare il disco di sistema e creare di nuovo la macchina virtuale](troubleshoot-recovery-disks-portal-windows.md).

6.  Controllare se il problema è stato risolto.
