---
title: Risolvere un problema correlato a netvsc.sys quando ci si connette in remoto a una macchina virtuale Windows 10 o Windows Server 2016 in Azure | Microsoft Docs
description: Informazioni su come risolvere un problema di connessione RDP correlato a netsvc.sys quando ci si connette a una macchina virtuale Windows 10 o Windows Server 2016 in Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: e6685a5e77d92bb9e05ab9578e48c99e80a64b74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362255"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Non è possibile stabilire una connessione remota a una macchina virtuale Windows 10 o Windows Server 2016 in Azure a causa di netvsc.sys

Questo articolo illustra come risolvere un problema dovuto all'assenza di connessione di rete quando si prova a connettersi a una macchina virtuale Windows 10 o Windows Server 2016 Datacenter su un host Hyper-V Server 2016.

## <a name="symptoms"></a>Sintomi

È possibile connettersi a un Azure Windows 10 o una macchina virtuale di Windows Server 2016 tramite Remote Desktop Protocol (RDP). In [Diagnostica di avvio](boot-diagnostics.md) viene visualizzata una croce rossa sulla scheda di interfaccia di rete (NIC). Ciò indica che la macchina virtuale non dispone di connettività dopo che il sistema operativo è completamente caricato.

Questo problema si verifica nelle build [14393](https://support.microsoft.com/help/4093120/) e [15063](https://support.microsoft.com/help/4015583/) di Windows. Se si usa una versione successiva del sistema operativo, questo articolo non si applica allo specifico scenario. Per controllare la versione del sistema, aprire una sessione CMD nella [console seriale di accesso](serial-console-windows.md) e quindi eseguire **Ver**.

## <a name="cause"></a>Causa

Questo problema può verificarsi se la versione del file system netvsc.sys installato è **10.0.14393.594** o **10.0.15063.0**. Queste versioni di netvsc.sys possono impedire l'interazione del sistema con la piattaforma Azure.


## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, [creare uno snapshot del disco di sistema](../windows/snapshot-copy-managed-disk.md) della macchina virtuale interessata come backup. Per risolvere questo problema, usare la console seriale o [riparare la macchina virtuale in modalità offline](#repair-the-vm-offline) collegando il disco di sistema della macchina virtuale a una macchina virtuale di ripristino.


### <a name="use-the-serial-console"></a>Usare la console seriale

Connettersi alla [console seriale, aprire un'istanza di PowerShell](serial-console-windows.md) e quindi seguire questa procedura.

> [!NOTE]
> Se la console seriale non è abilitata nella macchina virtuale, andare alla sezione [Riparare la macchina virtuale in modalità offline](#repair-the-vm-offline).

1. Eseguire il comando seguente in un'istanza di PowerShell per ottenere la versione del file (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Scaricare dalla stessa area l'aggiornamento appropriato in un disco dati nuovo o già esistente collegato a una macchina virtuale in esecuzione:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) o un aggiornamento successivo
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) o un aggiornamento successivo

3. Scollegare il disco di utilità dalla macchina virtuale in esecuzione e collegarlo alla macchina virtuale non funzionante.

4. Eseguire il comando seguente per installare l'aggiornamento nella macchina virtuale:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Riavviare la VM.

### <a name="repair-the-vm-offline"></a>Riparare la macchina virtuale in modalità offline

1. [Collegare il disco di sistema a una macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md).

2. Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.

3. Verificare che il disco sia contrassegnato come **Online** nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco di sistema collegato.

4. Creare una copia della cartella **\Windows\System32\config** nel caso in cui sia necessario un ripristino dello stato precedente.

5. Nella macchina virtuale di ripristino avviare l'editor del Registro di sistema (regedit.exe).

6. Selezionare la chiave **HKEY_LOCAL_MACHINE** e quindi scegliere **File** > **Carica hive** dal menu.

7. Individuare il file SYSTEM nella cartella **\Windows\System32\config**.

8. Selezionare **Apri**, digitare **BROKENSYSTEM** per il nome, espandere la chiave **HKEY_LOCAL_MACHINE** e quindi individuare la chiave aggiuntiva denominata **BROKENSYSTEM**.

9. Spostarsi nel percorso seguente:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. In ogni sottochiave, ad esempio 0000, esaminare il valore di **DriverDesc** visualizzato come **Scheda di rete Hyper-V Microsoft**.

11. Nella sottochiave esaminare il valore di **DriverVersion**, ovvero la versione del driver della scheda di rete della macchina virtuale.

12. Scaricare l'aggiornamento appropriato:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) o un aggiornamento successivo
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) o un aggiornamento successivo

13. Collegare il disco di sistema come disco dati in una macchina virtuale di ripristino in cui è possibile scaricare l'aggiornamento.

14. Eseguire il comando seguente per installare l'aggiornamento nella macchina virtuale:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Eseguire il comando seguente per smontare gli hive:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Scollegare il disco di sistema e creare di nuovo la macchina virtuale](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
