---
title: Ripristinare file e cartelle dal backup della macchina virtuale di AzureRecover files and folders from Azure VM backup
description: In questo articolo viene illustrato come ripristinare file e cartelle da un punto di ripristino della macchina virtuale di Azure.In this article, learn how to recover files and folders from an Azure virtual machine recovery point.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273306"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Ripristinare i file da un backup della macchina virtuale di Azure

Backup di Azure offre la possibilità di ripristinare [dischi e macchine virtuali (VM) di Azure](./backup-azure-arm-restore-vms.md) dai backup di VM di Azure, detti anche punti di recupero. Questo articolo illustra come ripristinare file e cartelle da un backup di VM di Azure. Il ripristino di file e cartelle è disponibile solo per le VM di Azure distribuite con il modello Resource Manager e protette in un insieme di credenziali dei servizi di ripristino.

> [!NOTE]
> Questa funzionalità è disponibile per le VM di Azure distribuite usando il modello Resource Manager e protette in un insieme di credenziali di Servizi di ripristino.
> Il ripristino di file da un backup della VM crittografato non è supportato.
>

## <a name="mount-the-volume-and-copy-files"></a>Montare il volume e copiare i file

Per ripristinare file o cartelle dal punto di recupero, passare alla macchina virtuale e scegliere il punto di recupero desiderato.

1. Accedere al [portale di Azure](https://portal.Azure.com) e, nel riquadro a sinistra, fare clic su **Macchine virtuali**. Nell'elenco delle macchine virtuali selezionare la macchina virtuale per aprirne il dashboard.

2. Nel menu della macchina virtuale fare clic su **Backup** per aprire il dashboard Backup.

    ![Aprire la voce di backup dell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Nel menu del dashboard Backup fare clic su **Ripristino file**.

    ![Pulsante Ripristino file](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Viene aperto il menu **Ripristino file**.

    ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Nel menu a discesa **Selezionare il punto di ripristino**, selezionare il punto di ripristino contenente i file desiderati. Per impostazione predefinita, il punto di ripristino più recente è già selezionato.

5. Per scaricare il software usato per copiare i file dal punto di ripristino, fare clic su **Scarica eseguibile** (per le macchine virtuali di Windows Azure) o **Scarica script** (per macchine virtuali di Azure Linux Linux, viene generato uno script python).

    ![Password generata](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure scarica il file eseguibile o lo script sul computer locale.

    ![Messaggio per il download del file eseguibile o dello script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Per eseguire l'eseguibile o lo script come amministratore, si consiglia di salvare il file scaricato nel computer.

6. Il file eseguibile o lo script è protetto da password, che viene quindi richiesta. Nel menu **Ripristino file** fare clic sul pulsante di copia per caricare la password in memoria.

    ![Password generata](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Nel percorso di download (in genere la cartella Download) fare clic con il pulsante destro del mouse sul file eseguibile o sullo script ed eseguirlo con credenziali di amministratore. Quando richiesto, digitare la password o incollarla dalla memoria e premere **INVIO**. Dopo l'immissione della password valida, lo script si connette al punto di ripristino.

    ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Per le macchine Linux, viene generato uno script python. È necessario scaricare lo script e copiarlo sul server Linux pertinente/compatibile. Potrebbe essere necessario modificare le autorizzazioni ```chmod +x <python file name>```per eseguirlo con . Quindi eseguire il ```./<python file name>```file python con .

Fare riferimento alla sezione Requisiti di [accesso](#access-requirements) per assicurarsi che lo script sia stato eseguito correttamente.

### <a name="identifying-volumes"></a>Identificazione dei volumi

#### <a name="for-windows"></a>Per Windows

Quando si esegue il file eseguibile, il sistema operativo monta i nuovi volumi e assegna lettere di unità. È possibile usare Esplora risorse o Esplora file per individuare queste unità. Le lettere di unità assegnate ai volumi potrebbero non essere le stesse lettere della macchina virtuale originale. Tuttavia, il nome del volume viene mantenuto. Ad esempio, se il volume nella macchina virtuale originale`\`era "Disco dati (E: )", tale volume può essere`\`collegato nel computer locale come "Disco dati ('Qualsiasi lettera': ). Sfogliare tutti i volumi indicati nell'output dello script fino a trovare i file o la cartella.  

   ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Per Linux

In Linux i volumi del punto di ripristino sono montati nella cartella in cui viene eseguito lo script. I dischi collegati, i volumi e i percorsi di montaggio corrispondenti vengono visualizzati di conseguenza. Questi percorsi di montaggio sono visibili agli utenti con accesso a livello radice. Esplorare i volumi indicati nell'output dello script.

  ![Menu Ripristino file per Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Chiusura della connessione

Dopo avere identificato i file e averli copiati in un percorso di archiviazione locale, rimuovere o smontare le unità aggiuntive. Per smontare le unità, nel menu **Ripristino file** del portale di Azure fare clic su **Unmount Disks** (Smonta dischi).

![Smontare i dischi](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Dopo che i dischi sono stati smontati, viene visualizzato un messaggio. L'aggiornamento della connessione in modo che sia possibile rimuovere i dischi potrebbe richiedere alcuni minuti.

In Linux, dopo che la connessione al punto di ripristino viene interrotta, il sistema operativo non rimuove automaticamente i percorsi di montaggio corrispondenti, I percorsi di montaggio esistono come volumi "orfani" e sono visibili, ma generano un errore quando si accede/si scrivono i file. Questi percorsi possono essere rimossi manualmente. Quando viene eseguito, lo script identifica tutti i volumi di questo tipo esistenti da eventuali punti di ripristino precedenti e li elimina dopo avere ottenuto il consenso.

## <a name="special-configurations"></a>Configurazioni speciali

### <a name="dynamic-disks"></a>Dischi dinamici

Se la VM di Azure protetta ha volumi con una o entrambe le caratteristiche seguenti, non è possibile eseguire lo script eseguibile nella stessa VM.

- Volumi che includono più dischi (volumi con spanning e con striping)
- Volumi a tolleranza di errore (volume RAID-5 e con mirroring) in dischi dinamici

Eseguire invece lo script eseguibile in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="windows-storage-spaces"></a>Spazi di archiviazione di Windows

Spazi di archiviazione Windows è una tecnologia Windows che consente di virtualizzare le risorse di archiviazione. Con Spazi di archiviazione Windows è possibile raggruppare dischi standard di settore in pool di archiviazione e quindi usare lo spazio disponibile in tali pool per creare dischi virtuali, detti spazi di archiviazione.

Se la VM di Azure protetta usa Spazi di archiviazione Windows, non è possibile eseguire lo script eseguibile nella stessa VM. Eseguire invece lo script eseguibile in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="lvmraid-arrays"></a>Array LVM/RAID

In Linux vengono usati la gestione dei volumi logici (LVM) e/o le matrici RAID software per gestire i volumi logici su più dischi. Se la VM Linux protetta usa array RAID e/o LVM, non è possibile eseguire lo script nella stessa VM. Eseguire invece lo script in qualsiasi altro computer con sistema operativo compatibile che supporti il file system della VM protetta.

L'output dello script seguente mostra dischi e volumi di array RAID e/o LVM con il tipo di partizione.

   ![Menu dell'output per LVM in Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Per portare online queste partizioni, eseguire i comandi riportati nelle sezioni successive.

#### <a name="for-lvm-partitions"></a>Per le partizioni LVM

Per elencare i nomi dei gruppi di volumi in un volume fisico:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Per elencare tutti i volumi logici, i nomi e i relativi percorsi in un gruppo di volumi:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

Per montare i volumi logici nel percorso desiderato:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Per array RAID

Il comando seguente visualizza i dettagli su tutti i dischi raid:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Il disco RAID pertinente viene visualizzato come `/dev/mdm/<RAID array name in the protected VM>`

Utilizzare il comando mount se il disco RAID dispone di volumi fisici:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Se nel disco RAID è configurata un'altra LVM, utilizzare la procedura precedente per le partizioni LVM ma utilizzare il nome del volume al posto del nome del disco RAID.

## <a name="system-requirements"></a>Requisiti di sistema

### <a name="for-windows-os"></a>Per il sistema operativo Windows

La tabella seguente illustra la compatibilità tra i sistemi operativi del server e del computer. Quando si esegue il ripristino di file, non è possibile ripristinare i file in una versione precedente o successiva del sistema operativo. Ad esempio, non è possibile ripristinare un file da una VM Windows Server 2016 a un computer Windows Server 2012 o Windows 8. È possibile ripristinare i file da una VM allo stesso sistema operativo server o al sistema operativo client compatibile.

|Sistema operativo del server | Sistema operativo compatibile del client  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Per il sistema operativo Linux

In Linux, il sistema operativo del computer usato per ripristinare i file deve supportare il file system della macchina virtuale protetta. Quando si seleziona un computer per l'esecuzione dello script, assicurarsi che abbia un sistema operativo compatibile e che usi una delle versioni indicate nella tabella seguente:

|Sistema operativo Linux | Versioni  |
| --------------- | ---- |
| Ubuntu | 12.04 e versioni successive |
| CentOS | 6.5 e versioni successive  |
| RHEL | 6.7 e versioni successive |
| Debian | 7 e versioni successive |
| Oracle Linux | 6.4 e versioni successive |
| SLES | 12 e versioni successive |
| openSUSE | 42.2 e versioni successive |

> [!NOTE]
> Abbiamo trovato alcuni problemi nell'esecuzione dello script di recupero dei file su macchine con sistema operativo SLES 12 SP4 e stiamo indagando con il team SLES.
> Attualmente, l'esecuzione dello script di recupero file sta lavorando su computer con versioni del sistema operativo SLES 12 SP2 e SP3.
>

Per l'esecuzione e la connessione sicura al punto di ripristino, lo script richiede anche componenti bash e Python.

|Componente | Versione  |
| --------------- | ---- |
| bash | 4 e versioni successive |
| python | 2.6.6 e versioni successive  |
| TLS | 1.2 dovrebbe essere supportata  |

## <a name="access-requirements"></a>Requisiti per l'accesso

Se si esegue lo script in un computer con accesso limitato, verificare che sia disponibile l'accesso a:

- `download.microsoft.com`
- URL di servizi di ripristino (il nome geografico si riferisce all'area in cui si trova l'insieme di credenziali di servizi di ripristino)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` (Per aree geografiche pubbliche di Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(Per Azure China 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us` (Per Azure US Gov)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de` (Per Azure Germania)
- Porte in uscita 53 (DNS), 443, 3260

> [!NOTE]
>
> - Il nome del file di script scaricato avrà il **nome geografico** da compilare nell'URL. Per exampple: il nome \'dello script\'\_\'scaricato\'\'inizia\'con VMname geoname _ GUID , ad *esempio ContosoVM_wcus_12345678*
> - L'URL <https://pod01-rec2.wcus.backup.windowsazure.com>sarebbe "
>

Per Linux, lo script richiede i componenti "open-iscsi" e "lshw" per la connessione al punto di ripristino. Se i componenti non esistono nel computer in cui viene eseguito lo script, lo script richiede l'autorizzazione per installare i componenti. Acconsentire all'installazione dei componenti necessari.

L'accesso `download.microsoft.com` a è necessario per scaricare i componenti utilizzati per creare un canale sicuro tra il computer in cui viene eseguito lo script e i dati nel punto di ripristino.

È possibile eseguire lo script in qualsiasi computer con lo stesso sistema operativo (o compatibile) della macchina virtuale sottoposta a backup. Vedere la [tabella di sistemi operativi compatibili](backup-azure-restore-files-from-vm.md#system-requirements) per informazioni in proposito. Se la macchina virtuale di Azure protetta usa Spazi di archiviazione Windows (per VM Windows di Azure) o array RAID/LVM (per VM Linux), non è possibile eseguire il file eseguibile o lo script nella stessa macchina virtuale. Eseguire invece il file eseguibile o lo script in qualsiasi altro computer con un sistema operativo compatibile.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Ripristino dei file dai backup delle macchine virtuali con dischi di grandi dimensioni

In questa sezione viene illustrato come eseguire il ripristino dei file dai backup di macchine virtuali di Azure con più di 16 dischi e le dimensioni di ogni disco sono superiori a 32 TB.

Poiché il processo di ripristino dei file collega tutti i dischi del backup, quando viene utilizzato un numero elevato di dischi (>16) o dischi di grandi dimensioni (> 32 TB ciascuno), sono consigliati i seguenti punti di azione:

- Mantenere un server di ripristino separato (macchine virtuali D2v3 VM di Azure) per il ripristino dei file. È possibile utilizzarlo solo per il ripristino dei file e quindi arrestarlo quando non è necessario. Il ripristino nel computer originale non è consigliato poiché avrà un impatto significativo sulla macchina virtuale stessa.
- Quindi eseguire lo script una volta per verificare se l'operazione di recupero del file ha esito positivo.
- Se il processo di ripristino dei file si blocca (i dischi non vengono mai montati o sono montati ma i volumi non vengono visualizzati), eseguire la procedura seguente.
  - Se il server di ripristino è una macchina virtuale Windows:If the restore server is a Windows VM:
    - Assicurarsi che il sistema operativo sia WS 2012 o versione successiva.
    - Assicurarsi che le chiavi del Registro di sistema siano impostate come indicato di seguito nel server di ripristino e assicurarsi di riavviare il server. Il numero accanto al GUID può essere compreso tra 0001-0005. Nell'esempio seguente, è 0004. Spostarsi nel percorso della chiave del Registro di sistema fino alla sezione dei parametri.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Se il server di ripristino è una macchina virtuale Linux:If the restore server is a Linux VM:
  - Nel file /etc/iscsi/iscsid.conf, modificare l'impostazione da:
    - node.conn[0].timeo.noop_out_timeout - 5 a node.conn[0].timeo.noop_out_timeout : 30
- Dopo aver apportato la modifica precedente, eseguire nuovamente lo script. Con queste modifiche, è altamente probabile che il recupero del file avrà esito positivo.
- Ogni volta che l'utente scarica uno script, Backup di Azure avvia il processo di preparazione del punto di ripristino per il download. Con dischi di grandi dimensioni, questo processo richiederà molto tempo. Se ci sono picchi successivi di richieste, la preparazione di destinazione andrà in una spirale di download. Pertanto, è consigliabile scaricare uno script da Portal/Powershell/CLI, attendere 20-30 minuti (un'euristica) e quindi eseguirlo. A questo punto, la destinazione dovrebbe essere pronta per la connessione da script.
- Dopo il ripristino dei file, assicurarsi di tornare al portale e fare clic su **Smonta dischi** per i punti di ripristino in cui non è stato possibile montare i volumi. Essenzialmente, questo passaggio pulirà tutti i processi/sessioni esistenti e aumenterà le possibilità di recupero.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi durante il ripristino di file dalle macchine virtuali, controllare la tabella seguente per informazioni aggiuntive.

| Messaggio di errore/scenario | Possibile causa | Azione consigliata |
| ------------------------ | -------------- | ------------------ |
| Output Exe: *eccezione rilevata durante la connessione alla destinazione* | Lo script non è in grado di accedere al punto di ripristino    | Verificare se la macchina soddisfa i requisiti di [accesso precedenti.](#access-requirements) |  
| Output del file exe: *Accesso alla destinazione già eseguito mediante una sessione iSCSI.* | Lo script è stato già eseguito nella stessa macchina virtuale e le unità sono state associate | I volumi del punto di ripristino sono già stati associati. È possibile che NON siano installati con le stesse lettere di unità della VM originale. Sfogliare tutti i volumi disponibili in Esplora file per il file. |
| Output Exe: *questo script non è valido perché i dischi sono stati disinstallati tramite portale/superato il limite di 12 ore. Scaricare un nuovo script dal portale.* |    I dischi sono stati smontati dal portale o il limite di 12 ore è stato superato | Questo particolare exe non è più valido e non può essere eseguito. Se si desidera accedere ai file di tale punto di ripristino nel tempo, visitare il portale per un nuovo exe.|
| Sul computer in cui viene eseguito l'exe: i nuovi volumi non vengono smontati dopo aver fatto clic sul pulsante di smontaggio | L'introditore iSCSI nel computer non risponde/aggiorna la connessione alla destinazione e gestisce la cache. |  Dopo aver fatto clic **Smontare**, attendere qualche minuto. Se i nuovi volumi non vengono smontati, sfogliare tutti i volumi. L'esplorazione di tutti i volumi forza l'innatore ad aggiornare la connessione e il volume viene disinstallato con un messaggio di errore che indica che il disco non è disponibile.|
| Output Exe: lo script viene eseguito correttamente ma "Nuovi volumi allegati" non viene visualizzato nell'output dello script |    Si tratta di un errore temporaneo    | I volumi saranno già stati allegati. Aprire Explorer per visualizzare lo stato. Se si utilizza lo stesso computer per l'esecuzione di script ogni volta, è consigliabile riavviare il computer e l'elenco deve essere visualizzato nelle esecuzioni successive del file exe. |
| Specifico per Linux: non è possibile visualizzare i volumi desiderati | Il sistema operativo del computer in cui viene eseguito lo script potrebbe non riconoscere il file system sottostante della VM protetta | Verificare se il punto di ripristino è coerente con l'arresto anomalo del sistema o per i file. Se compatibile con i file, eseguire lo script in un altro computer il cui sistema operativo riconosce il file system della macchina virtuale protetta. |
| Specifico per Windows: non è possibile visualizzare i volumi desiderati | È possibile che i dischi siano stati collegati ma i volumi non sono stati configurati | Dalla schermata Gestione disco, identificare i dischi aggiuntivi correlati al punto di recupero. Se uno di questi dischi si trova in uno stato offline, provare a portarli in linea facendo clic con il pulsante destro del mouse sul disco e scegliendo **Online**.|

## <a name="security"></a>Security

In questa sezione vengono illustrate le varie misure di sicurezza adottate per l'implementazione del ripristino dei file dai backup delle macchine virtuali di Azure.This section discusses the various security measures taken for the implementation of file recovery from Azure VM backups.

### <a name="feature-flow"></a>Flusso di funzionalità

Questa funzionalità è stata creata per accedere ai dati della macchina virtuale senza la necessità di ripristinare l'intera macchina virtuale o i dischi della macchina virtuale e con il numero minimo di passaggi. L'accesso ai dati della macchina virtuale viene fornito da uno script (che monta il volume di ripristino quando viene eseguito come illustrato di seguito) e costituisce la pietra angolare di tutte le implementazioni di sicurezza:Access to VM data is provided by a script (which mounts the recovery volume when run as shown at shown below) and it forms the cornerstone of all security implementations:

  ![Flusso delle funzionalità di sicurezza](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementazioni di sicurezza

#### <a name="select-recovery-point-who-can-generate-script"></a>Selezionare Punto di ripristino (che può generare script)

Lo script fornisce l'accesso ai dati della macchina virtuale, pertanto è importante regolare chi può generarli in primo luogo. È necessario accedere al portale di Azure ed essere [RBAC autorizzati](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a generare lo script.

Il ripristino dei file richiede lo stesso livello di autorizzazione richiesto per il ripristino della macchina virtuale e il ripristino dei dischi. In altre parole, solo gli utenti autorizzati possono visualizzare i dati della macchina virtuale possono generare lo script.

Lo script generato è firmato con il certificato Microsoft ufficiale per il servizio Backup di Azure.The generated script is signed with the official Microsoft certificate for the Azure Backup service. Qualsiasi manomissione dello script significa che la firma è interrotta e qualsiasi tentativo di eseguire lo script viene evidenziato come un potenziale rischio dal sistema operativo.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Mount Recovery (che può eseguire script)

Solo un amministratore può eseguire lo script e deve essere eseguito in modalità elevata. Lo script esegue solo un set pre-generato di passaggi e non accetta input da qualsiasi origine esterna.

Per eseguire lo script, è necessaria una password che venga visualizzata solo all'utente autorizzato al momento della generazione dello script nel portale di Azure o in PowerShell/CLI. Questo per garantire che l'utente autorizzato che scarica lo script sia anche responsabile dell'esecuzione dello script.

#### <a name="browse-files-and-folders"></a>Sfogliare file e cartelle

Per sfogliare file e cartelle, lo script utilizza l'inititore iSCSI nel computer e si connette al punto di ripristino configurato come destinazione iSCSI. Qui si possono immaginare scenari in cui si sta cercando di imitare / spoof uno o/tutti i componenti.

Viene utilizzato un meccanismo di autenticazione CHAP reciproco in modo che ogni componente autenticherà l'altro. Ciò significa che è estremamente difficile per un falso invitante connettersi alla destinazione iSCSI e per una destinazione fittizia da collegare alla macchina in cui viene eseguito lo script.

Il flusso di dati tra il servizio di ripristino e il computer è protetto dalla creazione di un tunnel TLS sicuro su TCP[(TLS 1.2 deve essere supportato](#system-requirements) nel computer in cui viene eseguito lo script).

Qualsiasi elenco di controllo di accesso (ACL) file presente nella macchina virtuale padre/backup viene mantenuto anche nel file system montato.

Lo script consente l'accesso in sola lettura a un punto di ripristino ed è valido solo per 12 ore. Se si vuole rimuovere l'accesso in precedenza, accedere a Portale di Azure/PowerShell/CLI ed eseguire **lo smontaggio** dei dischi per quel particolare punto di ripristino. Lo script verrà invalidato immediatamente.

## <a name="next-steps"></a>Passaggi successivi

- Per eventuali problemi durante il ripristino dei file, fare riferimento alla sezione [Risoluzione dei problemi](#troubleshooting)
- Scopri come [ripristinare i file tramite Powershell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Informazioni su come [ripristinare i file tramite l'interfaccia della riga di comando](https://docs.microsoft.com/azure/backup/tutorial-restore-files) di AzureLearn how to restore files via Azure CLI
- Dopo il ripristino della macchina virtuale, scopri come [gestire i backup](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
