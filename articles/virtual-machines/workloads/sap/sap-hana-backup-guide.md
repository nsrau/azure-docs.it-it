---
title: Guida del backup di SAP HANA in macchine virtuali di Azure | Documentazione Microsoft
description: La guida del backup per SAP HANA offre due opzioni principali per il backup di SAP HANA su macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 9e5b124643b753f404ba6012d3df998f567be59a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guida del backup di SAP HANA in macchine virtuali di Azure

## <a name="getting-started"></a>Introduzione

La guida del backup per SAP HANA in esecuzione su macchine virtuali di Azure illustra esclusivamente argomenti specifici di Azure. Per gli elementi correlati al backup generale di SAP HANA, consultare la documentazione di SAP HANA (vedere _Documentazione di backup per SAP HANA_ più avanti in questo articolo).

Questo articolo illustra le due principali opzioni di backup per SAP HANA in macchine virtuali di Azure:

- Backup di HANA nel file system di una macchina virtuale Linux in Azure (vedere [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md))
- Backup di HANA basato su snapshot di archiviazione attraverso la funzionalità di snapshot del BLOB del servizio di archiviazione di Azure manualmente o con il servizio di Backup di Azure (vedere [Backup di SAP HANA basato su snapshot di archiviazione](sap-hana-backup-storage-snapshots.md))

SAP HANA offre un'API di backup, che consente agli strumenti di backup di terze parti di integrarsi direttamente con SAP HANA (questo argomento non è compreso nella presente guida). Al momento non è prevista alcuna integrazione diretta di SAP HANA con il servizio di Backup di Azure basato su tale API.

SAP HANA è ufficialmente supportato nel tipo di VM di Azure GS5 come singola istanza con un vincolo aggiuntivo sui carichi di lavoro OLAP (vedere [Find Certified IaaS Platforms](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Trova piattaforme IaaS certificate) sul sito Web di SAP). Questo articolo verrà aggiornato non appena saranno disponibili nuove offerte per SAP HANA in Azure.

Azure offre anche una soluzione ibrida SAP HANA, in cui SAP HANA viene eseguito in modo non virtualizzato su server fisici. La presente guida del backup di SAP HANA in Azure descrive un ambiente di Azure genuino in cui SAP HANA viene eseguito in una VM di Azure, non su &quot;istanze di grandi dimensioni&quot;. Vedere [SAP HANA (large instances) overview and architecture on Azure](hana-overview-architecture.md) (Panoramica e architettura di SAP HANA (istanze di grandi dimensioni) in Azure) per altre informazioni su questa soluzione di backup per &quot;istanze di grandi dimensioni&quot; basate su snapshot di archiviazione.

Informazioni generali sui prodotti SAP supportati in Azure sono reperibili in [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533) (Nota SAP 1928533).

Le seguenti tre figure offrono una panoramica delle opzioni di backup per SAP HANA attraverso funzionalità native di Azure attualmente disponibili e illustrano anche tre potenziali scenari di backup futuri. Gli articoli correlati [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md) e [Backup di SAP HANA basato su snapshot di archiviazione](sap-hana-backup-storage-snapshots.md) descrivono queste opzioni in modo più dettagliato, con alcune considerazioni sulle dimensioni e le prestazioni per i backup SAP HANA con dimensioni a più terybite.

![La figura seguente mostra due possibilità per salvare lo stato corrente della VM](media/sap-hana-backup-guide/image001.png)

La figura seguente mostra la possibilità di salvare lo stato corrente della VM tramite il servizio di Backup di Azure o tramite lo snapshot manuale dei dischi della VM. Con questo approccio, non è necessario gestire i backup di SAP HANA. La sfida posta dallo scenario dello snapshot del disco risiede nella coerenza del file system e in uno stato del disco coerente con l'applicazione. Sulla coerenza si discuterà nella sezione _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_ più avanti in questo articolo. Più avanti nel seguente articolo vengono illustrate anche le funzionalità e le restrizioni del servizio di Backup di Azure correlate ai backup di SAP HANA.

![La figura seguente mostra le opzioni per l'esecuzione di un backup dei file di SAP HANA nella macchina virtuale](media/sap-hana-backup-guide/image002.png)

La figura seguente mostra le opzioni per eseguire un backup dei file SAP HANA nella macchina virtuale e quindi archiviare i file di backup HANA in una posizione diversa tramite strumenti differenti. L'esecuzione di un backup HANA richiede più tempo rispetto a una soluzione di backup basato su snapshot, ma presenta vantaggi riguardanti l'integrità e la coerenza. Altri dettagli sono disponibili più avanti in questo articolo.

![La figura seguente mostra un potenziale scenario futuro di backup di SAP HANA](media/sap-hana-backup-guide/image003.png)

La figura seguente mostra un potenziale scenario futuro di backup di SAP HANA. Se SAP HANA ha consentito l'esecuzione di backup da una replica secondaria, vengono aggiunte altre opzioni per le strategie di backup. Attualmente tale operazione non è possibile, come specificato in un post nella Wiki di SAP HANA:

_&quot;È possibile eseguire i backup sul lato secondario?_

_No, attualmente è possibile eseguire solo backup di dati e log sul lato primario. Se il backup automatico dei log è abilitato, dopo l'acquisizione sul lato secondario, i backup dei log verranno scritti automaticamente in questa posizione.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Risorse SAP per il backup di HANA

### <a name="sap-hana-backup-documentation"></a>Documentazione sul backup di SAP HANA

- [Introduction to SAP HANA Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US) (Introduzione all'amministrazione di SAP HANA)
- [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Pianificazione del backup e strategia di ripristino)
- [Schedule HANA Backup using ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html) (Pianificare il backup di HANA tramite ABAP DBACOCKPIT)
- [Schedule Data Backups (SAP HANA Cockpit)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm) (Pianificare il backup dei dati (Pannello di controllo di SAP HANA)
- Domande frequenti sul backup di SAP HANA in [Nota SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Domande frequenti sugli snapshot di archiviazione e dei database di SAP HANA in [Nota SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- File system di rete non appropriati per il backup e il ripristino in [Nota SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Perché eseguire il backup di SAP HANA?

Archiviazione di Azure offre disponibilità e affidabilità pronte all'uso (vedere [Introduzione ad Archiviazione di Microsoft Azure](../../../storage/common/storage-introduction.md) per altre informazioni sull'archiviazione di Azure).

Il requisito minimo per il &quot;backup&quot; è affidarsi ai contratti di servizio Azure, mantenendo i file di dati e log di SAP HANA nei dischi rigidi virtuali di Azure collegati alla macchina virtuale del server di SAP HANA. Questo approccio copre gli errori della macchina virtuale, ma non i possibili danni ai file di dati e log di SAP HANA o gli errori logici, ad esempio l'eliminazione accidentale di dati o file. I backup sono necessari anche per motivi legali o di conformità. In breve, è sempre necessario eseguire backup di SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Come verificare la correttezza del backup di SAP HANA
Quando si usano gli snapshot di archiviazione, è consigliabile eseguire un ripristino di prova su un sistema differente. Questo approccio offre un modo per verificare che la copia di backup sia corretta e che i processi interni per il backup e il ripristino funzionino come previsto. Mentre tale operazione, in locale, rappresenta un notevole ostacolo, è molto più semplice da eseguire nel cloud fornendo temporaneamente le risorse necessarie per questo scopo.

Tenere presente che eseguire un semplice ripristino e verificare che HANA sia attivo e in esecuzione non è sufficiente. In teoria, si dovrebbe eseguire una verifica della coerenza della tabella per verificare che il database sia stato ripristinato correttamente. SAP HANA offre diversi tipi di verifiche della coerenza, descritti nella [Nota SAP 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informazioni sulla verifica della coerenza della tabella sono reperibili anche nel sito Web di SAP in [Table and Catalog Consistency Checks](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b) (Verifiche della coerenza di tabelle e cataloghi).

Per il backup di file standard, non è necessario eseguire un ripristino di prova. Sono disponibili due strumenti di SAP HANA che consentono di controllare quale backup può essere usato per il ripristino: hdbbackupdiag e hdbbackupcheck. Vedere [Manually Checking Whether a Recovery is Possible](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) (Verifica manuale della fattibilità di un ripristino) per altre informazioni su questi strumenti.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Vantaggi e svantaggi dei backup di HANA rispetto agli snapshot di archiviazione

SAP non esprime preferenze tra l'esecuzione del backup di HANA e lo snapshot di archiviazione. SAP elenca vantaggi e svantaggi, in modo che l'utente possa determinare quale usare a seconda della situazione e della tecnologia di archiviazione disponibile. Vedere [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Pianificazione del backup e strategia di ripristino).

In Azure, tenere in considerazione il fatto che la funzionalità dello snapshot del BLOB di Azure non garantisce coerenza dei file system. Vedere [Using blob snapshots with PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)(Uso degli snapshot BLOB con PowerShell). Nella sezione successiva, _Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione_, vengono presentate alcune considerazioni su questa funzionalità.

È anche necessario comprendere le implicazioni di fatturazione quando si lavora spesso con gli snapshot del BLOB, come descritto in questo articolo: [Informazioni sull'incremento dei costi dovuto agli snapshot](/rest/api/storageservices/understanding-how-snapshots-accrue-charges). Non è scontato come per l'uso dei dischi virtuali di Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione

La coerenza del file system e dell'applicazione è un problema complesso quando si eseguono snapshot di archiviazione. Il modo più semplice per evitare problemi è quello di arrestare SAP HANA o l'intera macchina virtuale. L'arresto potrebbe essere eseguibile tramite demo, prototipo o anche tramite un sistema di sviluppo, ma non è un'opzione per un sistema di produzione.

In Azure è necessario tenere presente che la funzionalità di snapshot del BLOB di Azure non garantisce la coerenza del file system. Tuttavia, funziona correttamente usando la funzionalità di snapshot di SAP HANA, a condizione che sia coinvolto un singolo disco virtuale. Anche con un singolo disco, è necessario controllare alcuni elementi aggiuntivi. La [Nota SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883) contiene importanti informazioni sui backup di SAP HANA tramite gli snapshot di archiviazione. Ad esempio, la nota fa riferimento al fatto che, con il file system XFS, è necessario eseguire **xfs\_freeze** prima di avviare uno snapshot di archiviazione per garantire la coerenza (vedere [xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze) per informazioni dettagliate su **xfs\_freeze**).

L'argomento della coerenza diventa anche più complesso nel caso in cui un singolo file system si estenda su più dischi/volumi. Ad esempio, tramite mdadm o LVM e lo striping. La nota SAP indicata in precedenza afferma:

_&quot;Tenere tuttavia presente che il sistema di archiviazione deve garantire coerenza I/O durante la creazione di uno snapshot di archiviazione per ogni volume di dati di SAP HANA, ovvero l'esecuzione dello snapshot di un volume di dati specifico del servizio SAP HANA deve essere un'operazione atomica.&quot;_

Presupponendo che sia presente un file system XFS che si estende su quattro dischi virtuali di Azure, i passaggi seguenti offrono uno snapshot coerente che rappresenta l'area dati HANA:

- Preparazione dello snapshot HANA
- Bloccare il file system (ad esempio, usare **xfs\_freeze**).
- Creare tutti gli snapshot BLOB necessari in Azure.
- Sbloccare il file system.
- Confermare lo snapshot HANA.

Si consiglia di usare la procedura indicata in alto in tutti i casi per essere al sicuro, indipendentemente dal file system. In alternativa, se si tratta di un singolo disco o dello striping, usare mdadm o LVM nei diversi dischi.

È importante confermare lo snapshot HANA. A causa di &quot;Copy-on-Write&quot; SAP HANA potrebbe non richiedere altro spazio sul disco in questa modalità di preparazione dello snapshot. Non è neanche possibile avviare nuovi backup fino a quando lo snapshot di SAP HANA non viene confermato.

Il servizio di Backup di Azure usa le estensioni della macchina virtuale di Azure per occuparsi della coerenza dei file system. Queste estensioni della macchina virtuale non sono disponibili per l'uso autonomo. È comunque necessario gestire la coerenza di SAP HANA. Vedere l'articolo correlato [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md) per altre informazioni.

### <a name="sap-hana-backup-scheduling-strategy"></a>Strategia di pianificazione del backup di SAP HANA

L'articolo di SAP HANA [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Pianificazione del backup e strategia di ripristino) indica un piano di base per eseguire i backup:

- Snapshot di archiviazione (giornaliero)
- Backup completo dei dati tramite il formato di file o backint (una volta alla settimana)
- Backup automatici dei log

Facoltativamente, è possibile procedere completamente senza snapshot di archiviazione. Questi possono essere sostituiti da backup delta di HANA, ad esempio backup incrementali o differenziali. Vedere [Delta Backups](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm) (Backup delta).

La guida all'amministrazione di HANA offre un elenco di esempio. Si consiglia di ripristinare SAP HANA in uno specifico punto nel tempo usando la sequenza di backup seguente:

1. Backup completo dei dati
2. Backup differenziale
3. Backup incrementale 1
4. Backup incrementale 2
5. Backup dei log

Per quanto riguarda una pianificazione esatta di quando e con quale frequenza deve essere svolto un tipo di backup specifico, non è possibile fornire linee guida generali: si tratta di un elemento specifico del cliente, che dipende dal numero di modifiche dei dati che si verifica nel sistema. Un consiglio di base dal lato SAP, che può essere considerato come linea guida generale, è quello di eseguire un backup HANA completo una volta alla settimana.
Per quanto riguarda i backup dei log, vedere la documentazione di SAP HANA [Log Backups](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm) (Backup dei log).

SAP consiglia anche di eseguire alcune attività di manutenzione del catalogo di backup per impedirgli di crescere all'infinito. Vedere [Housekeeping for Backup Catalog and Backup Storage](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm) (Manutenzione del catalogo e dell'archiviazione dei backup).

### <a name="sap-hana-configuration-files"></a>File di configurazione di SAP HANA

Come indicato nelle domande frequenti contenute nella [Nota SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148), i file di configurazione SAP HANA non fanno parte di un backup HANA standard. Non sono essenziali per il ripristino di un sistema. La configurazione HANA può essere modificata manualmente dopo il ripristino. Nel caso in cui si desideri ottenere la stessa configurazione personalizzata durante il processo di ripristino, è necessario eseguire il backup dei file di configurazione HANA separatamente.

Se i backup HANA standard passano a un file system di backup HANA dedicato, è anche possibile copiare i file di configurazione nello stesso file system del backup e quindi copiare tutti gli elementi insieme nella destinazione di archiviazione finale come l'archiviazione BLOB per accesso sporadico.

### <a name="sap-hana-cockpit"></a>Pannello di controllo SAP HANA

Il pannello di controllo di SAP HANA offre la possibilità di monitorare e gestire di SAP HANA attraverso un browser. Consente anche la gestione dei backup di SAP HANA e pertanto può essere usato come alternativa a SAP HANA Studio e ad ABAP DBACOCKPIT. Vedere [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) (Pannello di controllo SAP HANA) per altre informazioni.

![Nella figura viene mostrata la schermata di gestione del database del pannello di controllo SAP HANA](media/sap-hana-backup-guide/image004.png)

Nella figura viene mostrata la schermata di gestione del database del pannello di controllo di SAP HANA e il riquadro del backup sulla sinistra. Per visualizzare il riquadro del backup sono necessarie adeguate autorizzazioni per l'utente sull'account di accesso.

![I backup possono essere monitorati nel pannello di controllo di SAP HANA durante l'esecuzione.](media/sap-hana-backup-guide/image005.png)

I backup possono essere monitorati nel pannello di controllo di SAP HANA durante l'esecuzione e, al termine, saranno disponibili tutti i dettagli sul backup.

![Esempio di uso di Firefox in una macchina virtuale 12 SLES di Azure con desktop Gnome](media/sap-hana-backup-guide/image006.png)

Le schermate precedenti sono state acquisite da una macchina virtuale Windows Azure. Questo è un esempio di uso di Firefox in una macchina virtuale 12 SLES di Azure con desktop Gnome, che mostra l'opzione per definire le pianificazioni di backup di SAP HANA nel pannello di controllo di SAP HANA. Come si può osservare, è consigliabile inserire data/ora come prefisso per i file di backup. In SAP HANA Studio il prefisso predefinito è &quot;COMPLETE\_DATA\_BACKUP&quot; per un backup completo del file. Si consiglia l'uso di un prefisso unico.

### <a name="sap-hana-backup-encryption"></a>Crittografia del backup di SAP HANA

SAP HANA offre la crittografia di dati e log. Se i dati e i log di SAP HANA non sono crittografati, allora neanche i backup sono crittografati. Il cliente potrà usare soluzioni di terze parti per crittografare i backup di SAP HANA. Vedere [Data and Log Volume Encryption](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) (Crittografia del volume di dati e log) per altre informazioni sulla crittografia di SAP HANA.

In Microsoft Azure è possibile eseguire la crittografia tramite la funzionalità di crittografia della macchina virtuale IaaS. Ad esempio, si potrebbero usare dei dischi di dati appositi collegati alla macchina virtuale, usati per archiviare i backup di SAP HANA, quindi creare delle copie di tali dischi.

Il servizio di Backup di Azure può gestire macchine virtuali e dischi crittografati (vedere [Come eseguire il backup e il ripristino delle macchine virtuali crittografate con il Backup di Azure](../../../backup/backup-azure-vms-encryption.md)).

Un'altra opzione è quella di gestire la macchina virtuale di SAP HANA e i relativi dischi senza crittografia e di archiviare i file di backup di SAP HANA in un account di archiviazione per cui sia stata abilitata la crittografia (vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Impostazione del test

### <a name="test-virtual-machine-on-azure"></a>Eseguire il test della macchina virtuale in Azure.

Per i test di backup e ripristino seguenti è stata usata un'installazione di SAP HANA in una macchina virtuale GS5 Azure.

![In questa figura viene illustrata parte della panoramica del portale di Azure per le macchine virtuali del test HANA](media/sap-hana-backup-guide/image007.png)

In questa figura viene illustrata parte della panoramica del portale di Azure per le macchine virtuali del test HANA.

### <a name="test-backup-size"></a>Testare la dimensione del backup

![Questa figura è stata ottenuta dalla console di backup in HANA Studio e mostra le dimensioni del file di backup di 229 GB per il server dell'indice HANA.](media/sap-hana-backup-guide/image008.png)

Una tabella fittizia è stata riempita con i dati per ottenere una dimensione di backup di dati totale di oltre 200 GB al fine di derivare i dati delle prestazioni realistiche. La figura è stata ottenuta dalla console di backup in HANA Studio e mostra le dimensioni del file di backup di 229 GB per il server dell'indice HANA. Per i test, è stato usato il prefisso di backup predefinito "COMPLETE_DATA_BACKUP" in SAP HANA Studio. Nei sistemi di produzione reali, deve essere definito un prefisso più utile. Il pannello di controllo SAP HANA suggerisce data/ora.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Strumento di test per copiare i file direttamente in Archiviazione di Azure

Per trasferire i file di backup di SAP HANA direttamente in Archiviazione BLOB di Azure o nelle condivisioni di file di Azure, è stato usato lo strumento blobxfer poiché supporta entrambe le destinazioni e può essere facilmente integrato negli script di automazione grazie all'interfaccia della riga di comando. Lo strumento blobxfer è disponibile in [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Testare la stima della dimensione del backup

È importante stimare la dimensione del backup di SAP HANA. Questa stima consente di migliorare le prestazioni definendo la dimensione massima del file di backup per un numero di file di backup, grazie al parallelismo durante una copia del file. Questi dettagli verranno spiegati più avanti nell'articolo. Si deve anche decidere se eseguire un backup completo o un backup delta (incrementale o differenziale).

Fortunatamente, esiste una semplice istruzione SQL che stima la dimensione dei file di backup: **selezionare \* da M\_BACKUP\_DIMENSIONE\_STIME**. Vedere [Estimate the Space Needed in the File System for a Data Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm) (Stimare lo spazio necessario nel file system per un backup di dati).

![L'output dell'istruzione SQL corrisponde quasi esattamente alla dimensione effettiva del backup completo dei dati sul disco](media/sap-hana-backup-guide/image009.png)

Per il sistema di test, l'output dell'istruzione SQL corrisponde quasi esattamente alla dimensione effettiva del backup completo dei dati sul disco.

### <a name="test-hana-backup-file-size"></a>Testare la dimensione del file di backup HANA

![La console di backup HANA Studio consente di limitare la dimensione massima del file dei file di backup di HANA.](media/sap-hana-backup-guide/image010.png)

La console di backup HANA Studio consente di limitare la dimensione massima dei file di backup di HANA. Nell'ambiente di esempio tale funzionalità consente di ottenere più file di backup più piccoli invece di un file di backup di 230 GB. Le dimensioni ridotte del file hanno un impatto significativo sulle prestazioni (vedere l'articolo correlato [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Summary

In base ai risultati dei test, le tabelle seguenti mostrano i vantaggi e gli svantaggi delle soluzioni per eseguire il backup di un database SAP HANA in esecuzione su macchine virtuali di Azure.

**Eseguire il backup di SAP HANA nel file system e copiare i file di backup in un secondo momento nella destinazione finale di backup**

|Soluzione                                           |Vantaggi                                 |Svantaggi                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Mantenere i backup di HANA sui dischi della macchina virtuale                      |Nessuno sforzo di gestione aggiuntivo     |Consuma spazio sul disco della macchina virtuale locale           |
|Strumento Blobxfer per copiare i file di backup nell'archiviazione BLOB |Parallelismo per copiare più file, scegliere di usare l'archiviazione BLOB per accesso sporadico | Manutenzione aggiuntiva dello strumento e script personalizzato | 
|Copia di BLOB tramite Powershell o interfaccia della riga di comando                    |Nessuno strumento aggiuntivo necessario, è possibile procedere con l'esecuzione tramite Azure Powershell o interfaccia della riga di comando |Processo manuale, il cliente si deve occupare dello script e della gestione di BLOB copiati per il ripristino|
|Copia nella condivisione NFS                                  |Post-elaborazione dei file di backup in altre macchine virtuali senza alcun impatto sul server HANA|Processo di copia lenta|
|Copia di blobxfer nel Servizio file di Azure                |Non consuma spazio sui dischi della macchina virtuale locale|Nessun supporto di scrittura diretto dal backup di HANA, la restrizione sulle dimensioni della condivisione di file è attualmente di 5 TB|
|Agente di Backup di Azure                                 | Soluzione preferibile         | Attualmente non disponibile su Linux    |



**Backup di SAP HANA basato su snapshot di archiviazione**

|Soluzione                                           |Vantaggi                                 |Svantaggi                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Servizio Backup di Azure                               | Consente il backup delle macchine virtuali basato su snapshot BLOB | Quando non si usa il ripristino a livello di file, è necessaria la creazione di una nuova macchina virtuale per il processo di ripristino, che quindi implica la necessità di una nuova chiave di licenza di SAP HANA.|
|Snapshot BLOB manuali                              | Flessibilità per creare e ripristinare i dischi di macchina virtuale specifici senza modificare l'ID univoco della macchina virtuale.|Tutte attività manuali, che devono essere eseguite dal cliente|

## <a name="next-steps"></a>Passaggi successivi
* [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md) descrive l'opzione di backup basato su file.
* [Backup di SAP HANA basato su snapshot di archiviazione](sap-hana-backup-storage-snapshots.md) descrive l'opzione di backup basato su snapshot di archiviazione.
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md).
