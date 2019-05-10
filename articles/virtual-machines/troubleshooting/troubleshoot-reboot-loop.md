---
title: Ciclo di riavvio di Windows in una macchina virtuale di Azure | Microsoft Docs
description: Informazioni su come risolvere un problema di riavvio continuo di Windows | Microsoft Docs
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
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 1c97b1da094b759ccf85f310ceec4c7abfd91b9b
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472301"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Ciclo di riavvio di Windows in una macchina virtuale di Azure
Questo articolo descrive una situazione di ciclo di riavvio che può verificarsi in una macchina virtuale Windows in Microsoft Azure.

## <a name="symptom"></a>Sintomo

Usando [Diagnostica di avvio](./boot-diagnostics.md) per ottenere gli screenshot di una macchina virtuale, si scopre che la macchina virtuale è in fase di avvio ma il processo di avvio viene interrotto e riavviato continuamente.

![Schermata iniziale 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Causa

Il ciclo di riavvio si verifica a seguito di una delle cause seguenti:

### <a name="cause-1"></a>Causa 1

È presente un servizio di terze parti contrassegnato come critico che non può essere avviato. Questo determina il riavvio continuo del sistema operativo.

### <a name="cause-2"></a>Causa 2

Sono state apportate alcune modifiche al sistema operativo. In genere, queste modifiche sono correlate all'installazione di un aggiornamento o di un'applicazione o all'introduzione di nuovi criteri. Per maggiori dettagli, potrebbe essere necessario controllare i log seguenti:

- Log eventi
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>Causa 3

Potrebbe essersi danneggiato il file system. È tuttavia difficile diagnosticare e identificare la modifica che ha causato il danneggiamento del sistema operativo.

## <a name="solution"></a>Soluzione

Per risolvere questo problema, [eseguire il backup del disco del sistema operativo](../windows/snapshot-copy-managed-disk.md), [collegare il disco del sistema operativo a una macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md) e quindi adottare le soluzioni disponibili in base al tipo di problema o una soluzione per volta.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

1. Dopo aver collegato il disco del sistema operativo a una macchina virtuale in esecuzione, assicurarsi che il disco sia contrassegnato come **Online** nella console di Gestione disco e annotare la lettera di unità della partizione in cui è contenuta la cartella **\Windows**.

2. Se il disco è impostato su **Offline**, impostarlo su **Online**.

3. Creare una copia della cartella **\Windows\System32\config** nel caso in cui sia necessario un rollback delle modifiche.

4. Nella macchina virtuale di ripristino aprire l'Editor del Registro di sistema di Windows (regedit).

5. Selezionare la chiave **HKEY_LOCAL_MACHINE** e, nel menu, selezionare **File** > **Carica Hive**.

6. Passare al file SYSTEM nella cartella **\Windows\System32\config**.

7. Selezionare **Apri**, digitare **BROKENSYSTEM** per il nome ed espandere la chiave **HKEY_LOCAL_MACHINE**. Verrà visualizzata una chiave aggiuntiva denominata **BROKENSYSTEM**.

8. Controllare da quale ControlSet si avvia il computer: il numero di chiave corrispondente viene visualizzato nella chiave del Registro di sistema seguente.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Verificare la criticità del servizio agente di macchine virtuali tramite la chiave del Registro di sistema seguente.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Se il valore della chiave del Registro di sistema non è impostato su **2**, passare alla mitigazione successiva.

11. Se il valore della chiave del Registro di sistema è impostato su **2**, modificare il valore da **2** a **1**.

12. Se una delle chiavi seguenti è presente nel Registro di sistema e il valore corrispondente è **2** o **3**, modificare il valore in **1**:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Selezionare la chiave **BROKENSYSTEM** e, nel menu, selezionare **File** > **Carica Hive**.

14. Scollegare il disco del sistema operativo dalla macchina virtuale per la risoluzione dei problemi.

15. Rimuovere il disco dalla macchina virtuale per la risoluzione dei problemi e attendere circa due minuti per consentire ad Azure di rilasciare il disco.

16. [Creare una nuova macchina virtuale dal disco del sistema operativo](../windows/create-vm-specialized.md).

17. Se il problema viene risolto, è possibile che sia necessario reinstallare [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Ripristinare la macchina virtuale all'ultima configurazione valida e seguire i passaggi descritti in [How to start Azure Windows VM with Last Known Good Configuration](https://support.microsoft.com/help/4016731/) (Come avviare una macchina virtuale di Azure in Windows con l'ultima configurazione valida).

### <a name="solution-for-cause-3"></a>Soluzione per la causa 3
>[!NOTE]
>La procedura seguente deve essere usata solo come ultima risorsa. Se da un lato il ripristino da regback può ripristinare l'accesso al computer, dall'altro lato il sistema operativo non è considerato stabile, poiché si è verificata una perdita di dati nel Registro di sistema tra il timestamp dell'hive e la data corrente. È quindi necessario creare una nuova macchina virtuale e definire un piano per la migrazione dei dati.

1. Dopo aver collegato il disco a una macchina virtuale di risoluzione dei problemi, assicurarsi che il disco sia contrassegnato come **Online** nella console di Gestione disco.

2. Creare una copia della cartella **\Windows\System32\config** nel caso in cui sia necessario un rollback delle modifiche.

3. Copia i file nella cartella **\Windows\System32\config\regback** e sostituirli nella cartella **\Windows\System32\config**.

4. Rimuovere il disco dalla macchina virtuale per la risoluzione dei problemi e attendere circa due minuti per consentire ad Azure di rilasciare il disco.

5. [Creare una nuova macchina virtuale dal disco del sistema operativo](../windows/create-vm-specialized.md).


