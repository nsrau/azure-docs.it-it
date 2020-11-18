---
title: Ripristinare file e cartelle da un backup di macchine virtuali di Azure
description: Questo articolo illustra come ripristinare file e cartelle da un punto di ripristino di macchine virtuali di Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: b9d5c90634dac3229e756ad93c10db91b268080c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841163"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Ripristinare i file da un backup della macchina virtuale di Azure

Backup di Azure offre la possibilità di ripristinare [dischi e macchine virtuali (VM) di Azure](./backup-azure-arm-restore-vms.md) dai backup di VM di Azure, detti anche punti di recupero. Questo articolo illustra come ripristinare file e cartelle da un backup di VM di Azure. Il ripristino di file e cartelle è disponibile solo per le VM di Azure distribuite usando il modello di Gestione risorse e protette in un insieme di credenziali di servizi di ripristino.


> [!NOTE]
> Questa funzionalità è disponibile per le VM di Azure distribuite usando il modello Resource Manager e protette in un insieme di credenziali di Servizi di ripristino.
> Il ripristino di file da un backup di VM crittografato non è supportato.
>

![Flusso di lavoro ripristino cartella file](./media/backup-azure-restore-files-from-vm/file-recovery-1.png)

## <a name="step-1-generate-and-download-script-to-browse-and-recover-files"></a>Passaggio 1: generare e scaricare lo script per cercare e ripristinare i file

Per ripristinare i file o le cartelle dal punto di ripristino, passare alla macchina virtuale ed eseguire la procedura seguente:

1. Accedere al [portale di Azure](https://portal.Azure.com) e nel riquadro sinistro selezionare **macchine virtuali**. Nell'elenco delle macchine virtuali selezionare la macchina virtuale per aprirne il dashboard.

2. Nel menu della macchina virtuale selezionare **backup** per aprire il dashboard di backup.

    ![Aprire la voce di backup dell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Scegliere **ripristino file** dal menu Dashboard di backup.

    ![Seleziona ripristino file](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Viene aperto il menu **Ripristino file**.

    ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Nel menu a discesa **Selezionare il punto di ripristino**, selezionare il punto di ripristino contenente i file desiderati. Per impostazione predefinita, il punto di ripristino più recente è già selezionato.

5. Selezionare **Scarica eseguibile** (per le macchine virtuali di Windows Azure) o **Scarica script** (per le macchine virtuali Linux di Azure, viene generato uno script Python) per scaricare il software usato per copiare i file dal punto di ripristino.

    ![Scarica eseguibile](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure scarica il file eseguibile o lo script sul computer locale.

    ![Messaggio per il download del file eseguibile o dello script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Per eseguire il file eseguibile o lo script come amministratore, è consigliabile salvare il file scaricato sul computer.

6. Il file eseguibile o lo script è protetto da password, che viene quindi richiesta. Nel menu **ripristino file** selezionare il pulsante copia per caricare la password in memoria.

    ![Password generata](./media/backup-azure-restore-files-from-vm/generated-pswd.png)


## <a name="step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script"></a>Passaggio 2: verificare che il computer soddisfi i requisiti prima di eseguire lo script

Al termine del download dello script, assicurarsi di disporre del computer corretto per l'esecuzione dello script. La macchina virtuale in cui si intende eseguire lo script non deve avere una delle seguenti configurazioni non supportate. In caso contrario, scegliere una macchina alternativa preferibilmente dalla stessa area che soddisfa i requisiti.  

### <a name="dynamic-disks"></a>Dischi dinamici

Non è possibile eseguire lo script eseguibile nella macchina virtuale con le caratteristiche seguenti:

- Volumi che si estendono su più dischi (volumi con spanning e con striping).
- Volumi a tolleranza di errore (volumi con mirroring e RAID-5) sui dischi dinamici.

### <a name="windows-storage-spaces"></a>Spazi di archiviazione di Windows

Non è possibile eseguire il file eseguibile scaricato nella macchina virtuale configurata per spazi di archiviazione Windows.

### <a name="virtual-machine-backups-having-large-disks"></a>Backup di macchine virtuali con dischi di grandi dimensioni

Se il computer di cui è stato eseguito il backup ha un numero elevato di dischi (>16) o dischi di grandi dimensioni (> 4 TB ciascuno), non è consigliabile eseguire lo script nello stesso computer per il ripristino, poiché avrà un impatto significativo sulla macchina virtuale. È invece consigliabile avere una macchina virtuale separata solo per il ripristino dei file (VM D2v3 VM di Azure) e quindi arrestarla quando non è necessario. 

## <a name="step-3-os-requirements-to-successfully-run-the-script"></a>Passaggio 3: requisiti del sistema operativo per eseguire correttamente lo script

La macchina virtuale in cui si vuole eseguire lo script scaricato deve soddisfare i requisiti seguenti.

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
> Sono stati rilevati alcuni problemi nell'esecuzione dello script di ripristino del file nei computer con sistema operativo SLES 12 SP4 ed è in corso l'analisi del team SLES.
> Attualmente, l'esecuzione dello script di ripristino dei file funziona sui computer con i sistemi operativi SLES 12 SP2 e SP3.
>

Per l'esecuzione e la connessione sicura al punto di ripristino, lo script richiede anche componenti bash e Python.

|Componente | Versione  |
| --------------- | ---- |
| bash | 4 e versioni successive |
| python | 2.6.6 e versioni successive  |
| TLS | 1.2 dovrebbe essere supportata  |

## <a name="step-4-access-requirements-to-successfully-run-the-script"></a>Passaggio 4: accedere ai requisiti per eseguire correttamente lo script

Se si esegue lo script in un computer con accesso limitato, verificare che sia disponibile l'accesso a:

- `download.microsoft.com`
- URL del servizio di ripristino (nome geografico si riferisce all'area in cui risiede l'insieme di credenziali dei servizi di ripristino)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.com` (Per le aree pubbliche di Azure)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.cn` (per Azure China (21Vianet))
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.us` (Per Azure US Gov)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.de` (Per Azure Germania)
- Porte in uscita 53 (DNS), 443, 3260

> [!NOTE]
>
> Il file di script scaricato nel passaggio 1 [precedente](#step-1-generate-and-download-script-to-browse-and-recover-files) avrà il **nome geografico** nel nome del file. Usare tale **nome geografico** per inserire l'URL. Il nome dello script scaricato inizierà con: \' VMName \' \_ \' geoname \' _ \' GUID \' .<br><br>
> Se, ad esempio, il nome del file di script è *ContosoVM_wcus_12345678*, il **nome geografico** è *wcus* e l'URL sarà:<br> <https://pod01-rec2.wcus.backup.windowsazure.com>
>

Per Linux, lo script richiede i componenti "open-iscsi" e "lshw" per la connessione al punto di ripristino. Se i componenti non sono presenti nel computer in cui viene eseguito, lo script chiede l'autorizzazione per installarli. Acconsentire all'installazione dei componenti necessari.

È necessario accedere a `download.microsoft.com` per scaricare i componenti usati per creare un canale sicuro tra il computer in cui viene eseguito lo script e i dati nel punto di ripristino.


## <a name="step-5-running-the-script-and-identifying-volumes"></a>Passaggio 5: esecuzione dello script e identificazione dei volumi

### <a name="for-windows"></a>Per Windows

Una volta soddisfatti tutti i requisiti elencati nel passaggio 2, passaggio 3 e passaggio 4, copiare lo script dal percorso scaricato, in genere la cartella Downloads, fare clic con il pulsante destro del mouse sul file eseguibile o sullo script ed eseguirlo con le credenziali di amministratore. Quando richiesto, digitare la password o incollarla dalla memoria e premere INVIO. Dopo l'immissione della password valida, lo script si connette al punto di ripristino.

  ![Output eseguibile](./media/backup-azure-restore-files-from-vm/executable-output.png)


Quando si esegue il file eseguibile, il sistema operativo monta i nuovi volumi e assegna lettere di unità. È possibile usare Esplora risorse o Esplora file per individuare queste unità. Le lettere di unità assegnate ai volumi potrebbero non essere le stesse lettere della macchina virtuale originale. Il nome del volume viene tuttavia mantenuto. Il volume della macchina virtuale originale "Disco dati (E:`\`)", ad esempio, può essere collegato nel computer locale come "Disco dati ('Qualsiasi lettera':`\`)". Esplorare tutti i volumi indicati nell'output dello script fino a individuare i file o la cartella.  

   ![Volumi di ripristino collegati](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

**Per le macchine virtuali di cui è stato eseguito il backup con dischi di grandi dimensioni (Windows)**

Se il processo di ripristino dei file si blocca dopo l'esecuzione dello script di ripristino del file, ad esempio se i dischi non sono mai montati o se sono montati ma i volumi non vengono visualizzati, seguire questa procedura:
  
1. Verificare che il sistema operativo sia WS 2012 o versione successiva.
2. Verificare che le chiavi del Registro di sistema siano impostate come indicato di seguito nel server di ripristino e assicurarsi di riavviare il server. Il numero accanto al GUID può essere compreso tra 0001 e 0005. Nell'esempio seguente è 0004. Passare al percorso della chiave del Registro di sistema fino alla sezione Parameters.

    ![Modifiche della chiave del registro di sistema](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

### <a name="for-linux"></a>Per Linux

Per i computer Linux, viene generato uno script Python. Scaricare lo script e copiarlo nel server Linux pertinente/compatibile. Potrebbe essere necessario modificare le autorizzazioni per eseguirlo con ```chmod +x <python file name>```. Eseguire quindi il file Python con ```./<python file name>```.


In Linux i volumi del punto di ripristino sono montati nella cartella in cui viene eseguito lo script. I dischi collegati, i volumi e i percorsi di montaggio corrispondenti vengono visualizzati di conseguenza. Questi percorsi di montaggio sono visibili agli utenti con accesso a livello radice. Esplorare i volumi indicati nell'output dello script.

  ![Menu Ripristino file per Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)


**Per le macchine virtuali di cui è stato eseguito il backup con dischi di grandi dimensioni (Linux)**

Se il processo di ripristino dei file si blocca dopo l'esecuzione dello script di ripristino del file, ad esempio se i dischi non sono mai montati o se sono montati ma i volumi non vengono visualizzati, seguire questa procedura:

1. Nel file /etc/iscsi/iscsid.conf modificare l'impostazione da:
    - `node.conn[0].timeo.noop_out_timeout = 5`  A `node.conn[0].timeo.noop_out_timeout = 30`
2. Dopo aver apportato le modifiche precedenti, eseguire di nuovo lo script. Se sono presenti errori temporanei, verificare che siano presenti da 20 a 30 minuti tra i riesecuzioni per evitare picchi successivi di richieste che incidono sulla preparazione della destinazione. Questo intervallo tra le esecuzioni garantisce che la destinazione sia pronta per la connessione dallo script.
3. Dopo il ripristino del file, assicurarsi di tornare al portale e selezionare **smontare i dischi** per i punti di ripristino in cui non è stato possibile montare i volumi. In pratica, questo passaggio elimina eventuali processi/sessioni esistenti e aumenta le probabilità di ripristino.


#### <a name="lvmraid-arrays-for-linux-vms"></a>Matrici LVM/RAID (per VM Linux)

In Linux, Logical Volume Manager (LVM) e/o array RAID software vengono usati per gestire i volumi logici su più dischi. Se la VM Linux protetta usa array RAID e/o LVM, non è possibile eseguire lo script nella stessa VM.<br>
Eseguire invece lo script in qualsiasi altro computer con sistema operativo compatibile che supporti il file system della VM protetta.<br>
L'output dello script seguente mostra dischi e volumi di array RAID e/o LVM con il tipo di partizione.

   ![Menu dell'output per LVM in Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Per portare online queste partizioni, eseguire i comandi riportati nelle sezioni successive.

#### <a name="for-lvm-partitions"></a>Per le partizioni LVM

Una volta eseguito lo script, le partizioni LVM vengono montate nei volumi fisici/disk specificati nell'output dello script. Il processo è

1. Ottenere l'elenco univoco dei nomi dei gruppi di volumi dai volumi fisici o dai dischi
2. Elencare quindi i volumi logici nei gruppi di volumi
3. Montare quindi i volumi logici in un percorso desiderato.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Elenco di nomi di gruppi di volumi da volumi fisici

Per elencare i nomi dei gruppi di volumi:

```bash
pvs -o +vguuid
```

Questo comando elenca tutti i volumi fisici (inclusi quelli presenti prima di eseguire lo script), i nomi dei gruppi di volumi corrispondenti e gli ID utente univoci (UUID) del gruppo di volumi. Di seguito è riportato un output di esempio del comando.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

La prima colonna (PV) Mostra il volume fisico, le colonne successive mostrano il nome del gruppo di volumi, il formato, gli attributi, le dimensioni, lo spazio disponibile e l'ID univoco del gruppo di volumi pertinenti. L'output del comando Mostra tutti i volumi fisici. Vedere l'output dello script e identificare i volumi correlati al backup. Nell'esempio precedente, l'output dello script avrebbe mostrato/dev/sdf e/dev/sdd. Quindi, il gruppo di volumi *datavg_db* appartiene allo script e il gruppo del volume *Appvg_new* appartiene al computer. L'idea finale consiste nel verificare che il nome di un gruppo di volumi univoco includa un ID univoco.

###### <a name="duplicate-volume-groups"></a>Gruppi di volumi duplicati

Esistono scenari in cui i nomi dei gruppi di volumi possono avere 2 UUID dopo l'esecuzione dello script. Ciò significa che i nomi dei gruppi di volumi nel computer in cui viene eseguito lo script e nella macchina virtuale di cui è stato eseguito il backup sono gli stessi. Quindi, è necessario rinominare i gruppi di volumi di macchine virtuali di cui è stato eseguito il backup. Vedere l'esempio seguente.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

L'output dello script avrebbe visualizzato/dev/sdg,/dev/SDH,/dev/sdm2 come allegato. Quindi, i nomi VG corrispondenti sono Appvg_new e rootvg. Gli stessi nomi, tuttavia, sono presenti anche nell'elenco VG del computer. È possibile verificare che un nome VG abbia due UUID.

A questo punto è necessario rinominare i nomi VG per i volumi basati su script, ad esempio:/dev/sdg,/dev/SDH,/dev/sdm2. Per rinominare il gruppo di volumi, utilizzare il comando seguente

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Sono ora disponibili tutti i nomi VG con ID univoci.

###### <a name="active-volume-groups"></a>Gruppi di volumi attivi

Verificare che i gruppi di volumi corrispondenti ai volumi dello script siano attivi. Il comando seguente viene usato per visualizzare i gruppi di volumi attivi. Controllare se i gruppi di volumi correlati allo script sono presenti nell'elenco.

```bash
vgdisplay -a
```  

In caso contrario, attivare il gruppo di volumi usando il comando seguente.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>Elenco di volumi logici nei gruppi di volumi

Una volta ottenuto l'elenco univoco e attivo dei VGs correlati allo script, i volumi logici presenti in tali gruppi di volumi possono essere elencati usando il comando seguente.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

Questo comando Visualizza il percorso di ogni volume logico come ' LV Path '.

##### <a name="mounting-logical-volumes"></a>Montaggio di volumi logici

Per montare i volumi logici nel percorso scelto:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> Non usare "Mount-a". Questo comando monta tutti i dispositivi descritti in '/etc/fstab '. Questo potrebbe significare che i dispositivi duplicati possono essere montati. I dati possono essere reindirizzati ai dispositivi creati da uno script, che non vengono salvati in modo permanente e potrebbero quindi causare la perdita di dati.

#### <a name="for-raid-arrays"></a>Per le matrici RAID

Il comando seguente visualizza informazioni dettagliate su tutti i dischi RAID:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Il disco RAID pertinente viene visualizzato come `/dev/mdm/<RAID array name in the protected VM>`

Usare il comando mount se il disco RAID dispone di volumi fisici:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Se nel disco RAID è configurata un'altra LVM, seguire la procedura precedente per le partizioni LVM usando il nome del volume invece del nome del disco RAID.

## <a name="step-6-closing-the-connection"></a>Passaggio 6: chiusura della connessione

Dopo avere identificato i file e averli copiati in un percorso di archiviazione locale, rimuovere o smontare le unità aggiuntive. Per smontare le unità, nel menu **ripristino file** della portale di Azure selezionare **smontare i dischi**.

![Smontare i dischi](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Una volta che i dischi sono stati smontati, si riceverà un messaggio. L'aggiornamento della connessione in modo che sia possibile rimuovere i dischi potrebbe richiedere alcuni minuti.

In Linux, dopo che la connessione al punto di ripristino viene interrotta, il sistema operativo non rimuove automaticamente i percorsi di montaggio corrispondenti, che rimangono come volumi "orfani" visibili, ma generano un errore in caso di accesso/scrittura di file. Questi percorsi possono essere rimossi manualmente. Quando viene eseguito, lo script identifica tutti i volumi di questo tipo esistenti da eventuali punti di ripristino precedenti e li elimina dopo avere ottenuto il consenso.

> [!NOTE]
> Assicurarsi che la connessione venga chiusa dopo il ripristino dei file necessari. Si tratta di un aspetto importante, soprattutto nello scenario in cui il computer in cui viene eseguito lo script è configurato anche per il backup. Se la connessione è ancora aperta, il backup successivo potrebbe non riuscire con l'errore "UserErrorUnableToOpenMount". Questo problema si verifica perché si presuppone che le unità o i volumi montati siano disponibili e quando vi si accede potrebbero avere esito negativo perché l'archiviazione sottostante, ovvero il server di destinazione iSCSI potrebbe non essere disponibile. Se si pulisce la connessione, queste unità/volumi verranno rimossi e pertanto non saranno disponibili durante il backup.

## <a name="security"></a>Sicurezza

Questa sezione illustra le varie misure di sicurezza adottate per l'implementazione del ripristino dei file dai backup di macchine virtuali di Azure.

### <a name="feature-flow"></a>Flusso di funzionalità

Questa funzionalità è stata creata per accedere ai dati della macchina virtuale senza la necessità di ripristinare l'intera VM o i relativi dischi e con il numero minimo di passaggi. L'accesso ai dati della VM viene fornito da uno script (che monta il volume di ripristino quando viene eseguito come illustrato di seguito) e costituisce la base di tutte le implementazioni di sicurezza:

  ![Flusso di funzionalità di sicurezza](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementazioni di sicurezza

#### <a name="select-recovery-point-who-can-generate-script"></a>Selezionare il punto di ripristino (che può generare lo script)

Lo script consente di accedere ai dati della macchina virtuale, quindi è importante definire in primo luogo chi possa generarlo. È necessario accedere al portale di Azure ed essere un controllo degli accessi in base al ruolo di [Azure autorizzato](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a generare lo script.

Il ripristino dei file richiede lo stesso livello di autorizzazione necessario per il ripristino di macchine virtuali e dischi. In altre parole, solo gli utenti autorizzati possono visualizzare i dati della macchina virtuale e generare lo script.

Lo script generato è firmato con il certificato Microsoft ufficiale per il servizio Backup di Azure. Eventuali manomissioni dello script indicano che la firma è danneggiata e qualsiasi tentativo di eseguire lo script viene evidenziato come un potenziale rischio da parte del sistema operativo.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montare il volume di ripristino (che può eseguire lo script)

Solo un amministratore può eseguire lo script e l'operazione deve essere eseguita in modalità con privilegi elevati. Lo script esegue solo un set di passaggi pregenerati e non accetta input da un'origine esterna.

Per eseguire lo script, è necessaria una password che viene visualizzata solo all'utente autorizzato al momento della generazione dello script nella portale di Azure o in PowerShell/CLI. In questo modo, l'utente autorizzato che Scarica lo script è anche responsabile dell'esecuzione dello script.

#### <a name="browse-files-and-folders"></a>Esplorare file e cartelle

Per esplorare file e cartelle, lo script usa l'iniziatore iSCSI nel computer e si connette al punto di ripristino configurato come destinazione iSCSI. Qui è possibile immaginare scenari in cui si prova a imitare o eseguire lo spoofing di uno o tutti i componenti.

Viene usato un meccanismo di autenticazione CHAP reciproca, in modo che ogni componente esegua l'autenticazione dell'altro. Ciò significa che è molto difficile per un iniziatore fittizio connettersi alla destinazione iSCSI e per una destinazione fittizia connettersi al computer in cui viene eseguito lo script.

Il flusso di dati tra il servizio di ripristino e il computer è protetto dalla compilazione di un tunnel TLS sicuro su TCP ([TLS 1.2 dovrebbe essere supportato](#step-3-os-requirements-to-successfully-run-the-script) nel computer in cui viene eseguito lo script).

Anche tutti gli elenchi di controllo di accesso (ACL) dei file presenti nella macchina virtuale padre/di cui è stato eseguito il backup vengono conservati nel file system montato.

Lo script concede l'accesso in sola lettura a un punto di ripristino ed è valido solo per 12 ore. Se si vuole rimuovere l'accesso in precedenza, accedere portale di Azure/PowerShell/CLI ed eseguire lo **smontaggio dei dischi** per quel particolare punto di ripristino. Lo script verrà immediatamente invalidato.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [ripristinare i file tramite PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- Informazioni su come [ripristinare i file tramite l'interfaccia della riga di comando di Azure](./tutorial-restore-files.md)
- Informazioni su come [gestire i backup](./backup-azure-manage-vms.md) dopo il ripristino di una macchina virtuale
