---
title: Backup di SAP HANA in Azure basato su snapshot di archiviazione | Documentazione Microsoft
description: "Esistono due possibilità principali per il backup di SAP HANA su macchine virtuali di Azure e questo articolo descrive il backup di SAP HANA basato su snapshot di archiviazione"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5d395da0779d84e414341d6d73151fd7e77e3e8e
ms.lasthandoff: 04/03/2017


---

# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Backup di SAP HANA basato su snapshot di archiviazione

## <a name="introduction"></a>Introduzione

Questo articolo fa parte di una serie di tre articoli correlati dedicati al backup di SAP HANA. La [Guida del backup di SAP HANA in macchine virtuali di Azure](sap-hana-backup-guide.md) offre una panoramica e informazioni introduttive, mentre [SAP HANA Azure Backup on file level](sap-hana-backup-file-level.md) (Backup di SAP HANA in Azure a livello di file) tratta l'opzione relativa al backup basato su file.

Quando si usa una funzionalità di backup di VM per un sistema dimostrativo all-in-one a istanza singola, è preferibile eseguire il backup della VM anziché gestire i backup HANA a livello di sistema operativo. Un'alternativa consiste nell'acquisire snapshot BLOB di Azure per creare copie dei singoli dischi virtuali collegati a una macchina virtuale e mantenere i file di dati HANA. La coerenza delle app è molto importante durante la creazione di uno snapshot di backup o del disco di una VM mentre il sistema è in esecuzione. Vedere _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ nell'articolo correlato [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md). SAP HANA dispone di una funzionalità che supporta questi tipi di snapshot di archiviazione.

## <a name="sap-hana-snapshots"></a>Snapshot di SAP HANA

SAP HANA dispone di una funzionalità che supporta la creazione di uno snapshot di archiviazione. Tuttavia, a partire da dicembre 2016, esiste una limitazione per i sistemi a contenitore singolo. Le configurazioni con contenitore multi-tenant non supportano questo tipo di snapshot del database (vedere [Create a Storage Snapshot (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) (Creare uno snapshot di archiviazione (SAP HANA Studio))).

Il funzionamento è il seguente:

- Preparare la creazione dello snapshot di archiviazione avviando lo snapshot di SAP HANA
- Eseguire lo snapshot di archiviazione (ad esempio uno snapshot BLOB di Azure)
- Confermare lo snapshot SAP HANA

![La schermata mostra che è possibile creare uno snapshot dei dati di SAP HANA tramite un'istruzione SQL](media/sap-hana-backup-storage-snapshots/image011.png)

La schermata mostra che è possibile creare uno snapshot dei dati di SAP HANA tramite un'istruzione SQL.

![Lo snapshot appare anche nel catalogo di backup in SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Lo snapshot appare anche nel catalogo di backup in SAP HANA Studio.

![Sul disco lo snapshot appare nella directory dei dati di SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

Sul disco lo snapshot appare nella directory dei dati di SAP HANA.

Prima di eseguire lo snapshot di archiviazione, mentre SAP HANA si trova nella modalità di preparazione dello snapshot, è necessario assicurarsi che sia garantita anche la coerenza del file system. Vedere _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ nell'articolo correlato [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md).

Dopo avere completato lo snapshot di archiviazione, è fondamentale confermare lo snapshot di SAP HANA. C'è un'istruzione SQL corrispondente da eseguire, ovvero BACKUP DATA CLOSE SNAPSHOT: vedere [BACKUP DATA CLOSE SNAPSHOT Statement - Backup and Recovery](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm) (Istruzione BACKUP DATA CLOSE SNAPSHOT - Backup e ripristino).

> [!IMPORTANT]
> Confermare lo snapshot HANA. A causa di &quot;Copy-on-Write&quot; SAP HANA potrebbe richiedere ulteriore spazio su disco nella modalità di preparazione dello snapshot e non è possibile avviare nuovi backup fino a quando lo snapshot di SAP HANA viene confermato.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Backup di VM HANA tramite il servizio Backup di Azure

A partire da dicembre 2016 l'agente di backup del servizio Backup di Azure non è disponibile per le VM Linux. Per usare il backup di Azure a livello di file o directory, è necessario copiare i file di backup di SAP HANA in una VM Windows e quindi usare l'agente di backup. Altrimenti è possibile soltanto eseguire un backup completo di una VM Linux tramite il servizio Backup di Azure. Per altre informazioni, vedere [Panoramica delle funzionalità di Backup di Azure](../../../backup/backup-introduction-to-azure-backup.md).

Il servizio Backup di Azure offre un'opzione per eseguire il backup e il ripristino di una VM. Altre informazioni su questo servizio e il suo funzionamento sono riportate nell'articolo [Pianificare l'infrastruttura di backup delle VM in Azure](../../../backup/backup-azure-vms-introduction.md).

L'articolo afferma che ci sono due fattori importanti da considerare:

_&quot;Per le macchine virtuali Linux sono possibili solo backup coerenti con i file perché Linux non ha una piattaforma equivalente al Servizio Copia Shadow del volume.&quot;_

_&quot;Le applicazioni devono implementare il proprio meccanismo di &quot;correzione&quot; sui dati ripristinati.&quot;_

Pertanto è necessario assicurarsi che SAP HANA sia in uno stato coerente sul disco all'avvio del backup. Vedere _gli snapshot di SAP HANA_ descritti in precedenza nel documento. Ma esiste un problema potenziale quando SAP HANA si trova in questa modalità di preparazione dello snapshot. Per altre informazioni, vedere [Create a Storage Snapshot - SAP HANA Studio](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) (Creare uno snapshot di archiviazione - SAP HANA Studio).

L'articolo afferma quanto segue:

_&quot;Si consiglia di confermare o abbandonare uno snapshot di archiviazione appena possibile dopo che è stato creato. Mentre lo snapshot di archiviazione viene preparato o creato, i dati interessati dallo snapshot vengono bloccati. Mentre i dati interessati dallo snapshot rimangano bloccati è comunque possibile apportare modifiche nel database. Tali modifiche non comporteranno variazioni nei dati bloccati interessati dallo snapshot. Le modifiche vengono scritte in posizioni dell'area dati separate dallo snapshot di archiviazione. Le modifiche vengono scritte anche nel log. Più i dati interessati dallo snapshot sono mantenuti bloccati, più il volume dei dati può aumentare.&quot;_

Il servizio Backup di Azure si occupa della coerenza del file system mediante le estensioni di VM di Azure. Queste estensioni non sono disponibili in modalità autonoma e funzionano solo in combinazione con il servizio Backup di Azure. È comunque necessario gestire uno snapshot di SAP HANA per garantire la coerenza delle app.

Backup di Azure presenta due fasi principali:

- Creare lo snapshot
- Trasferire i dati all'insieme di credenziali

Perciò è possibile confermare lo snapshot di SAP HANA dopo che la fase di creazione dello snapshot del servizio Backup di Azure è stata completata. Potrebbero essere necessari alcuni minuti per poter osservare questo nel portale di Azure.

![La figura mostra una parte dell'elenco del processo di backup di un servizio Backup di Azure](media/sap-hana-backup-storage-snapshots/image014.png)

La figura mostra una parte dell'elenco del processo di backup di un servizio Backup di Azure che è stato utilizzato per eseguire il backup della VM HANA di test.

![Per vedere i dettagli del processo fare clic sul processo di backup nel portale di Azure](media/sap-hana-backup-storage-snapshots/image015.png)

Per vedere i dettagli del processo fare clic sul processo di backup nel portale di Azure. Qui è possibile osservare le due fasi. Il completamento della fase di snapshot potrebbe richiedere alcuni minuti. La fase di trasferimento dei dati è quella che impiega la maggior parte del tempo.

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>Automazione del backup di VM HANA tramite il servizio Backup di Azure

È possibile confermare lo snapshot di SAP HANA manualmente dopo il completamento della fase di snapshot di Backup di Azure, come descritto in precedenza, ma è utile considerare la possibilità dell'automazione perché un amministratore potrebbe non monitorare l'elenco dei processi di backup nel portale di Azure.

Di seguito è riportata una spiegazione di come si potrebbe fare questo mediante i cmdlet di Azure PowerShell.

![È stato creato un servizio Backup di Azure con il nome hana-backup-vault](media/sap-hana-backup-storage-snapshots/image016.png)

È stato creato un servizio Backup di Azure con il nome &quot;hana-backup-vault&quot;. Il comando PS **Get-AzureRmRecoveryServicesVault -Name hana-backup-vault** recupera l'oggetto corrispondente. Questo oggetto viene quindi usato per impostare il contesto di backup, come illustrato nella figura riportata di seguito.

![È possibile controllare il processo di backup in corso](media/sap-hana-backup-storage-snapshots/image017.png)

Dopo aver impostato il contesto corretto, è possibile controllare il processo di backup in corso e quindi cercare i dettagli del processo. L'elenco delle sottoattività mostra se la fase di snapshot del processo di backup di Azure è già completata:

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![Eseguire il polling del valore in un ciclo fino a quando non diventa Completed](media/sap-hana-backup-storage-snapshots/image018.png)

Quando i dettagli del processo sono archiviati in una variabile, basta la semplice sintassi PS per ottenere il primo elemento della matrice e recuperare il valore di stato. Per completare lo script di automazione eseguire il polling del valore in un ciclo fino a quando non diventa &quot;Completed&quot;.

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Chiave di licenza di HANA e ripristino di VM mediante il servizio Backup di Azure

Il servizio Backup di Azure è progettato in modo da creare una nuova VM durante il ripristino. Al momento non è prevista la possibilità di eseguire un ripristino &quot;sul posto&quot; di una VM di Azure esistente.

![La figura illustra l'opzione di ripristino del servizio di Azure nel portale di Azure](media/sap-hana-backup-storage-snapshots/image019.png)

La figura illustra l'opzione di ripristino del servizio di Azure nel portale di Azure. È possibile scegliere tra creare una VM durante il ripristino e ripristinare i dischi. Dopo il ripristino dei dischi è comunque necessario creare una nuova VM su tale ripristino. Ogni volta che viene creata una nuova VM in Azure, l'ID VM univoco cambia (vedere [Accesso e uso dell'ID univoco di una VM di Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![La figura mostra l'ID VM univoco di Azure prima e dopo il ripristino tramite il servizio Backup di Azure](media/sap-hana-backup-storage-snapshots/image020.png)

La figura mostra l'ID VM univoco di Azure prima e dopo il ripristino tramite il servizio Backup di Azure. La chiave hardware SAP, che viene usata per la gestione delle licenze SAP, utilizza questo ID VM univoco. Di conseguenza, dopo il ripristino di una VM, è necessario installare una nuova licenza SAP.

Durante la creazione di questa guida al backup è stata presentata una nuova funzionalità di Backup di Azure in modalità di anteprima. La funzionalità consente il ripristino a livello di file basato sullo snapshot di VM che è stato eseguito per il backup della VM. In questo modo si evita la necessità di distribuire una nuova VM, pertanto l'ID VM univoco rimane lo stesso e non è necessaria una nuova chiave di licenza di SAP HANA. Altre informazioni su questa funzionalità saranno fornite dopo un completo test.

Backup di Azure consentirà alla fine il backup dei singoli dischi virtuali di Azure oltre a file e directory all'interno della VM. Uno dei vantaggi principali di Backup di Azure è la gestione di tutti i backup, risparmiando al cliente di doverla eseguire lui stesso. Quando diventa necessario un ripristino, Backup di Azure seleziona il backup corretto da usare.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Backup di VM SAP HANA mediante snapshot manuali dei dischi

Anziché usare il servizio Backup di Azure è possibile configurare una singola soluzione di backup mediante la creazione manuale di snapshot BLOB di dischi rigidi virtuali di Azure tramite PowerShell. Per la descrizione dei passaggi, vedere [Using blob snapshots with PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) (Usare gli snapshot BLOB con PowerShell).

Offre maggiore flessibilità ma non consente di risolvere i problemi descritti in precedenza in questo documento:

- È comunque necessario assicurarsi che SAP HANA sia in uno stato coerente
- Il disco del sistema operativo non può essere sovrascritto anche se la VM viene deallocata a causa di un errore che indica l'esistenza di un lease. Funziona solo dopo aver eliminato la VM, che potrebbe causare la creazione di un nuovo ID VM univoco e la necessità di installare una nuova licenza SAP.

![È possibile ripristinare solo i dischi di dati di una VM di Azure](media/sap-hana-backup-storage-snapshots/image021.png)

È possibile ripristinare solo i dischi di dati di una VM di Azure, evitando il problema della creazione di un nuovo ID VM univoco e, pertanto, dell'invalidamento della licenza SAP:

- Per il test sono stati collegati due dischi dati di Azure a una VM e un RAID software è stato definito su questi 
- La coerenza di SAP HANA è stata verificata mediante la funzionalità di snapshot di SAP HANA
- Blocco del file system (vedere _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ nell'articolo correlato [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md))
- Sono stati eseguiti snapshot BLOB da entrambi i dischi dati
- Sblocco del file system
- Conferma dello snapshot SAP HANA
- Per ripristinare i dischi dati, la VM è stata arrestata ed entrambi i dischi disconnessi
- Dopo la disconnessione dei dischi, questi sono stati sovrascritti con gli snapshot BLOB precedenti
- Quindi i dischi virtuali ripristinati sono stati collegati nuovamente alla VM
- Dopo l'avvio della VM, sul RAID software tutto ha funzionato correttamente ed è stato reimpostato sull'ora dello snapshot BLOB
- HANA è stato reimpostato sullo snapshot HANA

Se fosse possibile arrestare SAP HANA prima degli snapshot BLOB, la procedura sarebbe meno complessa. In questo caso si potrebbe saltare lo snapshot HANA e, se non succede nient'altro nel sistema, saltare anche il blocco del file system. Quando è necessario creare snapshot mentre tutto è online, la complessità è maggiore. Vedere _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ nell'articolo correlato [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Passaggi successivi
* La [Guida del backup di SAP HANA in Macchine virtuali di Azure](sap-hana-backup-guide.md) offre una panoramica e informazioni introduttive.
* [SAP HANA backup based on file level](sap-hana-backup-file-level.md) (Backup di SAP HANA a livello di file) descrive l'opzione di backup basato su snapshot di archiviazione.
* Per informazioni su come stabilire la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md).

