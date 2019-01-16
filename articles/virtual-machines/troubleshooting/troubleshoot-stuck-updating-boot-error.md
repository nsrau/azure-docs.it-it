---
title: L'avvio di una macchina virtuale di Azure si blocca in Windows Update | Microsoft Docs
description: Informazioni su come risolvere il problema per cui l'avvio di una macchina virtuale di Azure si blocca in Windows Update.
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: d56e96ca1fbc96261f6f526c792b0a53c74718ef
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063661"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>L'avvio della macchina virtuale di Azure si blocca in Windows Update

Questo articolo consente di risolvere il problema per cui una macchina virtuale si blocca in fase di avvio in Windows Update. 

> [!NOTE] 
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l’utilizzo del modello di distribuzione Gestione risorse. Invece del modello di distribuzione classica, per le nuove distribuzioni è consigliabile usare questo modello.

 ## <a name="symptom"></a>Sintomo

 La macchina virtuale Windows non si avvia. Quando si controllano gli screenshot nella finestra [Diagnostica di avvio](../troubleshooting/boot-diagnostics.md), si nota che l'avvio si è bloccato durante il processo di aggiornamento. Di seguito sono riportati esempi di messaggi che è possibile ricevere:

- Installazione di Windows in corso ##% Non spegnere il PC. Questa operazione richiede alcuni minuti. Il PC si riavvierà più volte
- Mantenere acceso il PC fino al completamento dell'operazione. Installazione dell'aggiornamento # di #... 
- Impossibile completare gli aggiornamenti. Annullamento delle modifiche. Non spegnere il computer
- Impossibile configurare gli aggiornamenti di Windows. Annullamento delle modifiche. Non spegnere il computer
- Errore < codice errore > durante l'applicazione delle operazioni di aggiornamento ##### di ##### (\Regist...)
- Errore grave < codice errore > durante l'applicazione delle operazioni di aggiornamento ##### of ##### ($$...)


## <a name="solution"></a>Soluzione

A seconda del numero di aggiornamenti di cui si sta eseguendo l'installazione o il rollback, il processo di aggiornamento può richiedere tempo. Lasciare la macchina virtuale in questo stato per otto ore. Se al termine delle otto ore la macchina virtuale è ancora in questo stato, riavviarla dal portale di Azure per vedere se si riavvia normalmente. Se questa operazione non risolve il problema, provare la soluzione seguente.

### <a name="remove-the-update-that-causes-the-problem"></a>Rimuovere l'aggiornamento che causa il problema

1. Creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md). 
2. [Collegare il disco del sistema operativo alla macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md).
3. Una volta collegato il disco del sistema operativo alla macchina virtuale di ripristino, eseguire **diskmgmt.msc** per aprire Gestione disco e verificare che il disco collegato sia **ONLINE**. Prendere nota della lettera di unità assegnata al disco del sistema operativo collegato in cui è contenuta la cartella \windows. Se il disco è crittografato, decrittografarlo prima di procedere con gli altri passaggi descritti in questo documento.

4. Aprire un'istanza del prompt dei comandi con privilegi elevati (Esegui come amministratore). Eseguire il comando seguente per ottenere l'elenco dei pacchetti di aggiornamento in esecuzione sul disco del sistema operativo collegato:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Se ad esempio il disco del sistema operativo collegato corrisponde all'unità F, eseguire il comando seguente:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Aprire il file C:\temp\Patch_level.txt e leggerlo dal basso verso l'alto. Individuare l'aggiornamento che si trova in stato **Installazione in sospeso** o **Disinstallazione in sospeso**.  Di seguito è riportato un esempio dello stato di aggiornamento:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Rimuovere l'aggiornamento che ha causato il problema:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Esempio: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > A seconda delle dimensioni del pacchetto, è possibile che, per elaborare la disinstallazione, lo strumento Gestione e manutenzione immagini distribuzione richieda un po' di tempo. In genere, il processo viene completato in 16 minuti.

7. [Scollegare il disco del sistema operativo e ricreare la macchina virtuale](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Controllare quindi se il problema è stato risolto.