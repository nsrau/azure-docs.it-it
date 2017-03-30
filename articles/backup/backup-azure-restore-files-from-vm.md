---
title: 'Backup di Azure: ripristinare file e cartelle da un backup di VM di Azure | Documentazione Microsoft'
description: Ripristinare i file da un punto di ripristino della macchina virtuale di Azure
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: ripristino a livello di elemento; ripristino di file da un backup di VM di Azure; ripristinare file da VM di Azure
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/6/2017
ms.author: pullabhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 873c64dfbd4ad6ced9e5a9eeb80d7ad6dbc558a6
ms.lasthandoff: 03/17/2017


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>Ripristinare i file da un backup della macchina virtuale di Azure (anteprima)

Backup di Azure consente di ripristinare [i dischi e le VM di Azure](./backup-azure-arm-restore-vms.md) dai backup delle VM di Azure. Questo articolo illustra come è possibile ripristinare elementi, come file e cartelle, da un backup di VM di Azure.

> [!Note]
> Questa funzionalità è disponibile per le VM di Azure distribuite usando il modello di Resource Manager e protette in un insieme di credenziali di Servizi di ripristino.
> Il ripristino di file da un backup della VM crittografato non è supportato.
>

## <a name="mount-the-volume-and-copy-files"></a>Montare il volume e copiare i file

1. Accedere al [portale di Azure](http://portal.Azure.com). Cercare l'insieme di credenziali di Servizi di ripristino pertinente e la voce di backup necessaria.

2. Nel pannello Elemento di backup fare clic su **Ripristino file (anteprima)**

    ![Aprire la voce di backup dell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    Verrà aperto il pannello **Ripristino file**.

    ![Pannello Ripristino file](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. Nel menu a discesa **Selezionare il punto di ripristino**, selezionare il punto di ripristino contenente i file desiderati. Per impostazione predefinita, il punto di ripristino più recente è già selezionato.

4. Fare clic su **Download Executable** (Scarica eseguibile) (per la VM di Windows Azure) o su **Scarica script** (per la VM di Azure per Linux) per scaricare il software che verrà usato per copiare i file dal punto di ripristino.

  Il file eseguibile/script crea una connessione tra il computer locale e il punto di ripristino specificato.

5. Eseguire il file eseguibile/script nel computer in cui si vogliono ripristinare i file. È necessario eseguire il file eseguibile/script con le credenziali di amministratore. Se si esegue lo script in un computer con accesso limitato, verificare che sia disponibile l'accesso a:

    - go.microsoft.com
    - Endpoint di Azure usati per i backup di VM di Azure
    - porta in uscita 3260

   Per Linux, lo script richiede i componenti "open-iscsi" e "lshw" per la connessione al punto di ripristino. Se tali componenti non esistono nel computer in cui lo script è in esecuzione, verrà richiesta l'autorizzazione per installare i componenti rilevanti, che saranno installati dopo avere ottenuto il consenso.
      
    ![Pannello Ripristino file](./media/backup-azure-restore-files-from-vm/executable-output.png)
    
   
   È possibile eseguire lo script in qualsiasi computer con lo stesso sistema operativo (o compatibile) della macchina virtuale sottoposta a backup. Vedere la [tabella di sistemi operativi compatibili](backup-azure-restore-files-from-vm.md#compatible-os) per informazioni in proposito. Se la macchina virtuale di Azure protetta usa Spazi di archiviazione Windows (per le VM di Windows Azure) o LVM/matrici RAID (per le VM di Linux), non è possibile eseguire il file eseguibile/script nella stessa macchina virtuale. Eseguirlo invece in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="compatible-os"></a>Sistema operativo compatibile

#### <a name="for-windows"></a>Per Windows

La tabella seguente illustra la compatibilità tra i sistemi operativi del server e del computer. Quando si esegue il ripristino dei file, non è possibile ripristinare i file tra sistemi operativi non compatibili.

|Sistema operativo del server | Sistema operativo compatibile del client  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>Per Linux

In Linux il requisito fondamentale è che il sistema operativo del computer in cui viene eseguito lo script supporti il file system dei file presenti nella VM Linux sottoposta a backup. Quando si seleziona un computer per eseguire lo script, verificare che disponga delle versioni e del sistema operativo compatibile indicati nella tabella seguente.

|Sistema operativo Linux | Versioni  |
| --------------- | ---- |
| Ubuntu | 12.04 e versioni successive |
| CentOS | 6.5 e versioni successive  |
| RHEL | 6.7 e versioni successive |
| Debian | 7 e versioni successive |
| Oracle Linux | 6.4 e versioni successive |

Lo script richiede inoltre i componenti python e bash per l'esecuzione e la connessione protetta al punto di ripristino.

|Componente | Versione  |
| --------------- | ---- |
| bash | 4 e versioni successive |
| python | 2.6.6 e versioni successive  |


### <a name="identifying-volumes"></a>Identificazione dei volumi

#### <a name="for-windows"></a>Per Windows

Quando si esegue il file eseguibile, il sistema operativo monta i nuovi volumi e assegna le lettere di unità. È possibile usare Esplora risorse o Esplora file per individuare queste unità. Le lettere di unità assegnate ai volumi potrebbero non essere le stesse lettere della macchina virtuale originale; il nome del volume viene tuttavia mantenuto. Ad esempio, se il volume nella macchina virtuale originale era "Disco dati (e:\)", questo volume può essere associato come "Disco dati ('Lettera di unità disponibile':\)" nel computer locale. Esplorare tutti i volumi indicati nell'output dello script fino a individuare il file o la cartella.  
       
   ![Pannello Ripristino file](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Per Linux

In Linux i volumi del punto di ripristino sono montati nella cartella in cui viene eseguito lo script. I dischi collegati, i volumi e i percorsi di montaggio corrispondenti vengono visualizzati di conseguenza. Questi percorsi di montaggio sono visibili agli utenti con accesso a livello radice. Esplorare i volumi indicati nell'output dello script.

  ![Pannello Ripristino file per Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Chiusura della connessione

Dopo avere identificato i file e averli copiati in un percorso di archiviazione locale, rimuovere o smontare le unità aggiuntive. Smontare le unità, nel pannello **Ripristino file** del portale di Azure fare clic su **Unmount Disks** (Smonta dischi).

![Smontare i dischi](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Dopo che i dischi sono stati smontati, viene visualizzato un messaggio indicante che l'operazione è stata eseguita correttamente. L'aggiornamento della connessione in modo che sia possibile rimuovere i dischi potrebbe richiedere alcuni minuti.

In Linux, dopo che la connessione al punto di ripristino viene interrotta, il sistema operativo non rimuove automaticamente i percorsi di montaggio corrispondenti, che rimangono come volumi "orfani" e sono visibili, ma generano un errore quando si accede o si scrive sui file. Questi percorsi possono essere rimossi manualmente. Quando viene eseguito, lo script identifica tutti i volumi di questo tipo esistenti da eventuali punti di ripristino precedenti e li elimina dopo avere ottenuto il consenso.

## <a name="special-configurations"></a>Configurazioni speciali

### <a name="windows-storage-spaces"></a>Spazi di archiviazione di Windows

Spazi di archiviazione Windows è una tecnologia di archiviazione Windows che consente di virtualizzare le risorse di archiviazione. Con Spazi di archiviazione Windows è possibile raggruppare dischi standard del settore in pool di archiviazione e creare dischi virtuali, detti spazi di archiviazione, dallo spazio disponibile nei pool di archiviazione.

Se la VM di Azure sottoposta a backup usa Spazi di archiviazione Windows, non è possibile eseguire lo script eseguibile nella stessa VM. Eseguire invece lo script eseguibile in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="lvmraid-arrays"></a>LVM/matrici RAID

In Linux vengono usati la gestione dei volumi logici (LVM) e/o le matrici RAID software per gestire i volumi logici su più dischi. Se la VM Linux sottoposta a backup usa la gestione dei volumi logici (LVM) e/o matrici RAID, non è possibile eseguire lo script nella stessa macchina virtuale. Eseguire invece lo script in qualsiasi altro computer con sistema operativo compatibile e che supporti il file system della macchina virtuale sottoposta a backup.

L'output dello script visualizza i dischi LVM e/o delle matrici RAID e i volumi con il tipo di partizione come mostrato di seguito:

   ![Pannello dell'output LVM per Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
I comandi seguenti devono essere eseguiti dall'utente per portare queste partizioni online. 

**Per le partizioni LVM**

```
$ pvs <volume name as shown above in the script output> 
```
Elenca i nomi del gruppo di volumi in un volume fisico.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```
Elenca tutti i volumi logici, i nomi e i relativi percorsi in un gruppo di volumi.

```
$ mount <LV path> </mountpath>
```
Per montare i volumi logici nel percorso scelto.


**Per le matrici RAID**

```
$ mdadm –detail –scan
```
Consente di visualizzare informazioni dettagliate su tutti i dischi RAID. Il disco RAID rilevante verrà visualizzato come `/dev/mdm/<RAID array name in the backed up VM>`

Usare il comando mount se il disco RAID dispone di volumi fisici.
```
$ mount [RAID Disk Path] [/mounthpath]
```

Se questo disco RAID ha un'altra LVM configurata, seguire la stessa procedura descritta in precedenza per le partizioni LVM con il nome del volume corrispondente al nome del disco RAID.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi durante il ripristino di file dalle macchine virtuali, controllare la tabella seguente per informazioni aggiuntive.

| Messaggio di errore/scenario | Possibile causa | Azione consigliata |
| ------------------------ | -------------- | ------------------ |
| Output del file exe: *Eccezione di connessione alla destinazione.* |Lo script non è in grado di accedere al punto di ripristino    | Controllare se la macchina virtuale soddisfa i requisiti di accesso indicati in precedenza|  
|    Output del file exe: *Accesso alla destinazione già eseguito mediante una sessione iSCSI.* |    Lo script è stato già eseguito nella stessa macchina virtuale e le unità sono state associate |    I volumi del punto di ripristino sono già stati associati. È possibile che NON siano installati con le stesse lettere di unità della VM originale. Esplorare tutti i volumi disponibili in Esplora file per il file |
| Output di file exe: *Questo script non è valido perché è necessario smontare i dischi tramite il portale/è stato superato il limite di 12 ore. Scaricare un nuovo script dal portale.* |    I dischi sono stati smontati dal portale o è stato superato il limite di 12 ore |    Non è possibile eseguire questo specifico file con estensione exe perché non è più valido. Se si vuole accedere ai file di questo punto di ripristino, visitare il portale per ottenere un nuovo file con estensione exe|
| Nella macchina virtuale in cui viene eseguito il file con estensione exe: i nuovi volumi non vengono smontati dopo avere selezionato il pulsante di disinstallazione |    L'iniziatore ISCSI nella macchina virtuale non sta rispondendo/aggiornando la connessione alla destinazione ed eseguendo la manutenzione della cache |    Attendere alcuni minuti dopo la pressione del pulsante per smontare. Se i nuovi volumi non sono ancora stati smontati, sfogliare tutti i volumi. In questo modo l'iniziatore deve aggiornare la connessione e il volume viene smontato con un messaggio di errore indicante che il disco non è disponibile|
| Output del file exe: lo script viene eseguito correttamente ma l'output indicante nuovi volumi associati non viene visualizzato nell'output dello script |    Si tratta di un errore temporaneo    | I volumi sono stata già associati. Aprire Explorer per visualizzare lo stato. Se si sta usando la stessa macchina virtuale per eseguire gli script ogni volta, è consigliabile riavviare la macchina; l'elenco verrà visualizzato nelle successive esecuzioni del file eseguibile. |
| Specifico per Linux: non è possibile visualizzare i volumi desiderati | Il sistema operativo del computer in cui viene eseguito lo script potrebbe non riconoscere il file system sottostante della macchina virtuale sottoposta a backup | Controllare se il punto di ripristino è coerente con l'arresto anomalo del sistema o è coerente a livello di file. Se è coerente a livello di file, eseguire lo script in un altro computer il cui sistema operativo riconosca il file system della macchina virtuale sottoposta a backup. |

