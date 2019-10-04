---
title: 'Backup di Azure: Ripristinare file e cartelle da un backup di macchine virtuali di Azure'
description: Ripristinare i file da un punto di ripristino della macchina virtuale di Azure
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: ripristino a livello di elemento; ripristino di file da un backup di VM di Azure; ripristinare file da VM di Azure
ms.service: backup
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: dacurwin
ms.openlocfilehash: 5ff4f1ff8a3d6143285b2842c351e1d26bd356ea
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210362"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Ripristinare i file da un backup della macchina virtuale di Azure

Backup di Azure offre la possibilità di ripristinare [dischi e macchine virtuali (VM) di Azure](./backup-azure-arm-restore-vms.md) dai backup di VM di Azure, detti anche punti di recupero. Questo articolo illustra come ripristinare file e cartelle da un backup di VM di Azure. Il ripristino di file e cartelle è disponibile solo per le VM di Azure distribuite con il modello Resource Manager e protette in un insieme di credenziali dei servizi di ripristino.

> [!Note]
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

5. Per scaricare il software usato per copiare i file dal punto di ripristino, fare clic su **Scarica eseguibile**, per una macchina virtuale Windows di Azure, oppure su **Scarica script**, per una macchina virtuale Linux di Azure per cui viene generato uno script di Python.

    ![Password generata](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure scarica il file eseguibile o lo script sul computer locale.

    ![Messaggio per il download del file eseguibile o dello script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Per eseguire il file eseguibile o lo script come amministratore, è consigliabile salvare il download sul computer.

6. Il file eseguibile o lo script è protetto da password, che viene quindi richiesta. Nel menu **Ripristino file** fare clic sul pulsante di copia per caricare la password in memoria.

    ![Password generata](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Nel percorso di download (in genere la cartella Download) fare clic con il pulsante destro del mouse sul file eseguibile o sullo script ed eseguirlo con credenziali di amministratore. Quando richiesto, digitare la password o incollarla dalla memoria e premere INVIO. Dopo l'immissione della password valida, lo script si connette al punto di ripristino.

    ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Se si esegue lo script in un computer con accesso limitato, verificare che sia disponibile l'accesso a:

    - download.microsoft.com
    - URL di servizi di ripristino (il nome geografico si riferisce all'area in cui si trova l'insieme di credenziali di servizi di ripristino)
        - https:\//pod01-rec2.Geo-Name.backup.WindowsAzure.com (per GEOS pubblico di Azure)
        - https:\//pod01-rec2.Geo-Name.backup.WindowsAzure.cn (per Azure Cina 21ViaNet)
        - https:\//pod01-rec2.Geo-Name.backup.WindowsAzure.US (per il governo degli Stati Uniti di Azure)
        - https:\//pod01-rec2.Geo-Name.backup.WindowsAzure.de (per Azure Germania)
    - porta in uscita 3260

> [!Note]
> 
> * Il nome del file script scaricato avrà il **nome geografico** da compilare nell'URL. Per esempio: Il nome dello script scaricato inizia \'con\'VMName\_\'\'geoname\'_GUID\', ad esempio ContosoVM_wcus_12345678....<br><br>
> * L'URL è "https:\//pod01-rec2.wcus.backup.WindowsAzure.com"


   Per Linux, lo script richiede i componenti "open-iscsi" e "lshw" per la connessione al punto di ripristino. Se i componenti non sono presenti nel computer in cui viene eseguito, lo script chiede l'autorizzazione per installarli. Acconsentire all'installazione dei componenti necessari.

   Per accedere a download.microsoft.com è necessario scaricare i componenti usati per creare un canale sicuro tra il computer in cui viene eseguito lo script e i dati nel punto di ripristino.

   È possibile eseguire lo script in qualsiasi computer con lo stesso sistema operativo (o compatibile) della macchina virtuale sottoposta a backup. Vedere la [tabella di sistemi operativi compatibili](backup-azure-restore-files-from-vm.md#system-requirements) per informazioni in proposito. Se la macchina virtuale di Azure protetta usa Spazi di archiviazione Windows (per VM Windows di Azure) o array RAID/LVM (per VM Linux), non è possibile eseguire il file eseguibile o lo script nella stessa macchina virtuale. Eseguire invece il file eseguibile o lo script in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="identifying-volumes"></a>Identificazione dei volumi

#### <a name="for-windows"></a>Per Windows

Quando si esegue il file eseguibile, il sistema operativo monta i nuovi volumi e assegna lettere di unità. È possibile usare Esplora risorse o Esplora file per individuare queste unità. Le lettere di unità assegnate ai volumi potrebbero non essere le stesse lettere della macchina virtuale originale; il nome del volume viene tuttavia mantenuto. Il volume della macchina virtuale originale "Disco dati (E:`\`)", ad esempio, può essere collegato nel computer locale come "Disco dati ('Qualsiasi lettera':`\`)". Esplorare tutti i volumi indicati nell'output dello script fino a individuare il file o la cartella.  

   ![Menu Ripristino file](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Per Linux

In Linux i volumi del punto di ripristino sono montati nella cartella in cui viene eseguito lo script. I dischi collegati, i volumi e i percorsi di montaggio corrispondenti vengono visualizzati di conseguenza. Questi percorsi di montaggio sono visibili agli utenti con accesso a livello radice. Esplorare i volumi indicati nell'output dello script.

  ![Menu Ripristino file per Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Chiusura della connessione

Dopo avere identificato i file e averli copiati in un percorso di archiviazione locale, rimuovere o smontare le unità aggiuntive. Per smontare le unità, nel menu **Ripristino file** del portale di Azure fare clic su **Unmount Disks** (Smonta dischi).

![Smontare i dischi](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Dopo che i dischi sono stati smontati, viene visualizzato un messaggio. L'aggiornamento della connessione in modo che sia possibile rimuovere i dischi potrebbe richiedere alcuni minuti.

In Linux, dopo che la connessione al punto di ripristino viene interrotta, il sistema operativo non rimuove automaticamente i percorsi di montaggio corrispondenti, che rimangono come volumi "orfani" visibili, ma generano un errore in caso di accesso/scrittura di file. Questi percorsi possono essere rimossi manualmente. Quando viene eseguito, lo script identifica tutti i volumi di questo tipo esistenti da eventuali punti di ripristino precedenti e li elimina dopo avere ottenuto il consenso.

## <a name="special-configurations"></a>Configurazioni speciali

### <a name="dynamic-disks"></a>Dischi dinamici

Se la VM di Azure protetta ha volumi con una o entrambe le caratteristiche seguenti, non è possibile eseguire lo script eseguibile nella stessa VM.

- Volumi che includono più dischi (volumi con spanning e con striping)
- Volumi a tolleranza di errore (volume RAID-5 e con mirroring) in dischi dinamici

Eseguire invece lo script eseguibile in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="windows-storage-spaces"></a>Spazi di archiviazione di Windows

Spazi di archiviazione Windows è una tecnologia Windows che consente di virtualizzare le risorse di archiviazione. Con Spazi di archiviazione Windows è possibile raggruppare dischi standard di settore in pool di archiviazione e quindi usare lo spazio disponibile in tali pool per creare dischi virtuali, detti spazi di archiviazione.

Se la VM di Azure protetta usa Spazi di archiviazione Windows, non è possibile eseguire lo script eseguibile nella stessa VM. Eseguire invece lo script eseguibile in qualsiasi altro computer con un sistema operativo compatibile.

### <a name="lvmraid-arrays"></a>LVM/matrici RAID

In Linux vengono usati la gestione dei volumi logici (LVM) e/o le matrici RAID software per gestire i volumi logici su più dischi. Se la VM Linux protetta usa array RAID e/o LVM, non è possibile eseguire lo script nella stessa VM. Eseguire invece lo script in qualsiasi altro computer con sistema operativo compatibile che supporti il file system della VM protetta.

L'output dello script seguente mostra dischi e volumi di array RAID e/o LVM con il tipo di partizione.

   ![Menu dell'output per LVM in Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Per portare online queste partizioni, eseguire i comandi riportati nelle sezioni successive.

#### <a name="for-lvm-partitions"></a>Per le partizioni LVM

Per ottenere un elenco dei nomi del gruppo di volumi in un volume fisico:

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Per ottenere un elenco di tutti i volumi logici, dei nomi e dei relativi percorsi in un gruppo di volumi:

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Per montare i volumi logici nel percorso scelto.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Per le matrici RAID

Il comando seguente visualizza informazioni dettagliate su tutti i dischi RAID.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 Il disco RAID pertinente viene visualizzato come `/dev/mdm/<RAID array name in the protected VM>`

Usare il comando mount se il disco RAID dispone di volumi fisici.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Se nel disco RAID è configurata un'altra LVM, seguire la procedura precedente per le partizioni LVM usando però il nome del volume invece del nome del disco RAID.

## <a name="system-requirements"></a>Requisiti di sistema

### <a name="for-windows-os"></a>Per il sistema operativo Windows

La tabella seguente illustra la compatibilità tra i sistemi operativi del server e del computer. Quando si esegue il ripristino di file, non è possibile ripristinare i file in una versione precedente o successiva del sistema operativo. Ad esempio, non è possibile ripristinare un file da una VM Windows Server 2016 a un computer Windows Server 2012 o Windows 8. È possibile ripristinare i file da una VM allo stesso sistema operativo server o al sistema operativo client compatibile.

|Sistema operativo del server | Sistema operativo compatibile del client  |
| --------------- | ---- |
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

> [!Note]
> Sono stati rilevati alcuni problemi nell'esecuzione dello script di ripristino del file nei computer con sistema operativo SLES 12 SP4. Analisi del team SLES.
> Attualmente, l'esecuzione dello script di ripristino del file funziona sui computer con versioni del sistema operativo SLES 12 SP2 e SP3.
>

Per l'esecuzione e la connessione sicura al punto di ripristino, lo script richiede anche componenti bash e Python.

|Componente | Versione  |
| --------------- | ---- |
| bash | 4 e versioni successive |
| python | 2.6.6 e versioni successive  |
| TLS | 1.2 dovrebbe essere supportata  |

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi durante il ripristino di file dalle macchine virtuali, controllare la tabella seguente per informazioni aggiuntive.

| Messaggio di errore/scenario | Possibile causa | Azione consigliata |
| ------------------------ | -------------- | ------------------ |
| Output del file EXE: *Eccezione di connessione alla destinazione* |Lo script non è in grado di accedere al punto di ripristino    | Controllare se il computer soddisfa i requisiti di accesso indicati in precedenza. |  
| Output del file EXE: *Accesso alla destinazione già eseguito mediante una sessione iSCSI.* | Lo script è stato già eseguito nella stessa macchina virtuale e le unità sono state associate | I volumi del punto di ripristino sono già stati associati. È possibile che NON siano installati con le stesse lettere di unità della VM originale. Esplorare tutti i volumi disponibili in Esplora file per il file |
| Output del file EXE: *Questo script non è valido perché è stato necessario smontare i dischi tramite il portale o è stato superato il limite di 12 ore. Scaricare un nuovo script dal portale.* |    I dischi sono stati smontati dal portale o è stato superato il limite di 12 ore | Non è possibile eseguire questo specifico file con estensione exe perché non è più valido. Se si vuole accedere ai file di questo punto di ripristino, visitare il portale per ottenere un nuovo file con estensione exe|
| Nella macchina virtuale in cui viene eseguito il file con estensione EXE: i nuovi volumi non vengono smontati dopo avere selezionato il pulsante di disinstallazione | L'iniziatore iSCSI nella macchina non sta rispondendo/aggiornando la connessione alla destinazione ed eseguendo la manutenzione della cache. |  Dopo aver fatto clic **Smontare**, attendere qualche minuto. Se i nuovi volumi non sono stati smontati, sfogliare tutti i volumi. In questo modo l'iniziatore deve aggiornare la connessione e il volume viene smontato con un messaggio di errore indicante che il disco non è disponibile.|
| Output del file EXE: lo script viene eseguito correttamente ma l'output indicante nuovi volumi associati non viene visualizzato nell'output dello script |    Si tratta di un errore temporaneo    | I volumi sono stata già associati. Aprire Explorer per visualizzare lo stato. Se si sta usando la stessa macchina virtuale per eseguire gli script ogni volta, è consigliabile riavviare la macchina; l'elenco verrà visualizzato nelle successive esecuzioni del file eseguibile. |
| Specifico per Linux: non è possibile visualizzare i volumi desiderati | Il sistema operativo del computer in cui viene eseguito lo script potrebbe non riconoscere il file system sottostante della VM protetta | Controllare se il punto di ripristino è coerente con l'arresto anomalo del sistema o è coerente a livello di file. Se è coerente a livello di file, eseguire lo script in un altro computer il cui sistema operativo riconosce il file system della VM protetta |
| Specifico per Windows: non è possibile visualizzare i volumi desiderati | I dischi possono essere stati collegati, ma i volumi non sono stati configurati | Dalla schermata Gestione disco, identificare i dischi aggiuntivi correlati al punto di recupero. Se uno di questi dischi è in stato offline, provare a renderli online facendo clic con il pulsante destro del mouse sul disco e scegliendo ' online '|

## <a name="security"></a>Security

Questa sezione illustra le varie misure di sicurezza adottate per l'implementazione del ripristino di file dai backup di macchine virtuali di Azure, in modo che gli utenti siano a conoscenza degli aspetti di sicurezza della funzionalità.

### <a name="feature-flow"></a>Flusso di funzionalità

Questa funzionalità è stata creata per accedere ai dati della macchina virtuale senza dover ripristinare l'intera macchina virtuale o i dischi delle macchine virtuali e in passaggi minimi. L'accesso ai dati della VM viene fornito da uno script (che monta il volume di ripristino quando viene eseguito come illustrato di seguito) e quindi costituisce il fondamento di tutte le implementazioni di sicurezza.

  ![Flusso delle funzionalità di sicurezza](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementazioni della sicurezza

#### <a name="select-recovery-point-who-can-generate-script"></a>Seleziona punto di ripristino (che può generare lo script)

Lo script fornisce l'accesso ai dati della macchina virtuale, è importante definire chi può generarlo in primo luogo. È necessario accedere a portale di Azure e il controllo degli accessi in base al ruolo [autorizzato](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a generare lo script.

Il ripristino del file richiede lo stesso livello di autorizzazione necessario per il ripristino delle macchine virtuali e il ripristino dei dischi. In altre parole, solo gli utenti autorizzati possono visualizzare i dati della macchina virtuale possono generare lo script.

Lo script generato è firmato con un certificato Microsoft ufficiale per il servizio backup di Azure. Eventuali manomissioni dello script indicano che la firma è interruppe e qualsiasi tentativo di eseguire lo script viene evidenziato come un potenziale rischio da parte del sistema operativo.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montaggio del volume di ripristino (utente che può eseguire lo script)

Solo l'amministratore può eseguire lo script ed eseguirlo in modalità con privilegi elevati. Lo script esegue solo un set di passaggi pregenerati e non accetta input da un'origine esterna.

Per eseguire lo script, è necessario specificare una password che viene visualizzata solo per l'utente autorizzato al momento della generazione dello script nella portale di Azure o in PowerShell/CLI. Questo consente di verificare che l'utente autorizzato che Scarica lo script sia anche responsabile dell'esecuzione dello script.

#### <a name="browse-files-and-folders"></a>Sfogliare file e cartelle

Per esplorare file e cartelle, lo script usa l'iniziatore iSCSI nel computer e si connette al punto di ripristino configurato come destinazione iSCSI. Qui è possibile ipotizzare scenari in cui uno sta provando a imitare/falsificare o tutti i componenti.

Viene usato il meccanismo di autenticazione CHAP reciproca, in modo che ogni componente esegua l'autenticazione dell'altro. Ciò significa che è molto difficile per un iniziatore fasullo connettersi alla destinazione iSCSI e una destinazione falsa da connettere al computer in cui viene eseguito lo script.

Il flusso di dati tra il servizio di ripristino e il computer è protetto mediante la creazione di un tunnel SSL sicuro su TCP (è[necessario supportare TLS 1,2](#system-requirements) nel computer in cui viene eseguito lo script)

Anche tutti gli elenchi di controllo di accesso (ACL) dei file presenti nella macchina virtuale padre/di cui è stato eseguito il backup vengono conservati nel file system montato.

Lo script concede l'accesso in sola lettura a un punto di ripristino ed è valido solo per 12 ore. Se l'utente vuole rimuovere l'accesso in precedenza, accedere al portale di Azure/PowerShell/CLI ed eseguire il **smontaggio dei dischi** per quel particolare punto di ripristino. Lo script verrà invalidato immediatamente.
