---
title: Guida del backup di SAP HANA in macchine virtuali di Azure | Documentazione Microsoft
description: La guida del backup per SAP HANA offre due opzioni principali per il backup di SAP HANA su macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255247"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guida del backup di SAP HANA in macchine virtuali di Azure

## <a name="getting-started"></a>Introduzione

La guida del backup per SAP HANA in esecuzione su macchine virtuali di Azure illustra esclusivamente argomenti specifici di Azure. Per gli elementi correlati al backup SAP HANA generali, consultare la documentazione di SAP HANA. Ci aspettiamo che tu abbia familiarità con le strategie di backup principali del database, i motivi e le motivazioni per avere una strategia di backup solida e valida e siamo consapevoli dei requisiti che la tua azienda ha per la procedura di backup, il periodo di conservazione dei backup e il ripristino Procedura.

SAP HANA è ufficialmente supportato in vari tipi di macchine virtuali di Azure, ad esempio Azure di serie M. Per un elenco completo delle macchine virtuali di Azure certificate SAP HANA e delle unità di istanza di grandi dimensioni HANA, vedere [Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Microsoft Azure offre una serie di unità in cui SAP HANA viene eseguito non virtualizzato su server fisici. Questo servizio è denominato [istanze di grandi dimensioni HANA](hana-overview-architecture.md). Questa guida non tratterà i processi di backup e gli strumenti per le istanze di grandi dimensioni HANA. Ma sarà limitato alle macchine virtuali di Azure.But is to be limited to Azure virtual machines. Per informazioni dettagliate sui processi di backup/ripristino con LE istanze di grandi dimensioni HANA, leggere l'articolo [Backup e ripristino HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore).

L'obiettivo di questo articolo è su tre possibilità di backup per SAP HANA nelle macchine virtuali di Azure:The focus of this article is on three backup possibilis for SAP HANA on Azure virtual machines:

- Backup HANA tramite [i servizi di backup di AzureHANA](https://docs.microsoft.com/azure/backup/backup-overview) backup through Azure Backup Services 
- Backup di HANA nel file system di una macchina virtuale Linux in Azure (vedere [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md))
- Backup HANA basato su snapshot di archiviazione usando manualmente la funzionalità snapshot del BLOB di archiviazione di Azure o il servizio Backup di AzureHANA backup based on storage snapshots using the Azure storage blob snapshot feature manually or Azure Backup service


SAP HANA offre un'API di backup, che consente agli strumenti di backup di terze parti di integrarsi direttamente con SAP HANA Prodotti come il servizio Backup di Azure o [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) usano questa interfaccia proprietaria per attivare i backup del database SAP HANA o del logdo di ripetizione. 


Informazioni su come è possibile trovare il software SAP supportato in Azure sono disponibili nell'articolo [Quale software SAP è supportato per le distribuzioni](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)di Azure .

## <a name="azure-backup-service"></a>Servizio Backup di Azure

Il primo scenario illustrato è uno scenario in cui `backint` il servizio Backup di Azure usa l'interfaccia SAP HANA per eseguire un backup di streaming con un database SAP HANA. In alternativa, si usa una funzionalità più generica del servizio Backup di Azure per creare uno snapshot del disco coerente dell'applicazione e trasferire tale funzionalità al servizio Backup di Azure.Or you use a more generic capability of Azure Backup service to create an application consistent disk snapshot and have that one transferred that one transferred to the Azure Backup service.

Backup di Azure integra ed è certificato come soluzione di backup per SAP HANA utilizzando l'interfaccia SAP HANA proprietaria denominata [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Per ulteriori dettagli sulla soluzione, le relative funzionalità e le aree di Azure in cui è disponibile, leggere l'articolo Matrice di supporto per il [backup dei database SAP HANA nelle macchine virtuali](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)di Azure. Per informazioni dettagliate e principi sul servizio Backup di Azure per HANA, leggere l'articolo Informazioni sul backup del [database SAP HANA nelle macchine virtuali](https://docs.microsoft.com/azure/backup/sap-hana-db-about)di Azure. 

La seconda possibilità di sfruttare il servizio Backup di Azure consiste nel creare un backup coerente dell'applicazione usando gli snapshot del disco di Archiviazione Premium di Azure.The second possibilità to leverage Azure Backup service is to create an application consistent backup using disk snapshots of Azure Premium Storage. Altre archivi di Azure certificati HANA, come [il disco Azure Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd) e i file NetApp di Azure non supportano questo tipo di snapshot tramite il servizio Backup di Azure.Other HANA certified Azure storages, like Azure Ultra disk and Azure [NetApp Files](https://azure.microsoft.com/services/netapp/) are not supporting this kind of snapshot through Azure Backup service. Leggere questi articoli:

- [Pianificare l'infrastruttura di backup delle VM in Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Backup coerente con le applicazioni per macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

questa sequenza di attività emerge:

- Backup di Azure deve eseguire uno script pre-snapshot che metta l'applicazione, in questo caso SAP HANA, in uno stato coerente
- Man mano che questo stato coerente viene confermato, Backup di Azure eseguirà gli snapshot del disco
- Dopo aver completato gli snapshot, Backup di Azure anunterà l'attività eseguita nello script pre-snapshot
- Dopo l'esecuzione, Backup di Azure trasmetterà i dati nell'insieme di credenziali di backup

Nel caso di SAP HANA, la maggior parte dei clienti usa Azure Write Accelerator per i volumi che contengono il log di ripetizione SAP HANA. Il servizio Backup di Azure escluderà automaticamente questi volumi dagli snapshot. Questa esclusione non danneggia la capacità di ripristino di HANA. Anche se bloccherebbe la possibilità di ripristino con quasi tutti gli altri DBMS supportati da SAP.

Lo svantaggio di questa possibilità è il fatto che è necessario sviluppare il proprio script pre e post-istantanea. Lo script pre-snapshot deve creare uno snapshot HANA e gestire eventuali casi di eccezione. Mentre lo script post-istantanea deve eliminare nuovamente lo snapshot HANA. Per ulteriori dettagli sulla logica richiesta, iniziare con [il supporto SAP nota #2039883](https://launchpad.support.sap.com/#/notes/2039883). Le considerazioni della sezione "Coerenza dei dati SAP HANA quando si scattano istantanee di archiviazione" in questo articolo si applicano completamente a questo tipo di backup.

> [!NOTE]
> I backup basati su snapshot del disco per SAP HANA nelle distribuzioni in cui vengono utilizzati più contenitori di database richiedono una versione minima di HANA 2.0 SP04
> 

Vedere i dettagli sulle istantanee di archiviazione più avanti in questo documento.

![La figura seguente mostra due possibilità per salvare lo stato corrente della VM](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Altri metodi di backup HANA
Esistono altri tre metodi o percorsi di backup che possono essere considerati:

- Backup di una condivisione NFS basata su File NetApp di Azure (ANF). ANF ha nuovamente la possibilità di creare istantanee di tali volumi in cui si archiviano i backup. Data la velocità effettiva necessaria per scrivere i backup, questa soluzione potrebbe diventare un metodo costoso. Anche se facile da stabilire poiché HANA può scrivere i backup direttamente nella condivisione NFS nativa di Azure
- Esecuzione del backup HANA su dischi collegati VM di SSD standard o Azure Premium Storage. Come passaggio successivo è possibile copiare i file di backup nell'archivio BLOB di Azure.As next step you can copy those backup files against Azure Blob storage. Questa strategia potrebbe essere un'attraente
- Esecuzione del backup HANA su dischi collegati VM di SSD standard o Azure Premium Storage. Come passo successivo il disco viene snapshotted su base regolare. Dopo il primo snapshot, è possibile utilizzare snapshot incrementali per ridurre i costi

![La figura seguente mostra le opzioni per l'esecuzione di un backup dei file di SAP HANA nella macchina virtuale](media/sap-hana-backup-guide/other-hana-backup-paths.png)

La figura seguente mostra le opzioni per eseguire un backup dei file SAP HANA nella macchina virtuale e quindi archiviare i file di backup HANA in una posizione diversa tramite strumenti differenti. Tuttavia, tutte le soluzioni che non coinvolgono un servizio di backup di terze parti o un servizio Backup di Azure presentano diversi ostacoli in comune. Alcuni di essi possono essere elencati, come l'amministrazione della conservazione, il processo di ripristino automatico e fornire il ripristino automatico temporizzato come servizio di backup di Azure o altre suite e servizi di backup specializzati di terze parti. Molti di questi servizi di terze parti sono in grado di eseguire in Azure.Many of those third-party services being able to run on Azure. 


## <a name="sap-resources-for-hana-backup"></a>Risorse SAP per il backup di HANA

### <a name="sap-hana-backup-documentation"></a>Documentazione sul backup di SAP HANA

- [Introduction to SAP HANA Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US) (Introduzione all'amministrazione di SAP HANA)
- [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Pianificazione del backup e strategia di ripristino)
- [Schedule HANA Backup using ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html) (Pianificare il backup di HANA tramite ABAP DBACOCKPIT)
- [Schedule Data Backups (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm) (Pianificare il backup dei dati (Pannello di controllo di SAP HANA)
- Domande frequenti sul backup di SAP HANA in [Nota SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Domande frequenti sugli snapshot di archiviazione e dei database di SAP HANA in [Nota SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- File system di rete non appropriati per il backup e il ripristino in [Nota SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Come verificare la correttezza del backup di SAP HANA
Indipendentemente dal metodo di backup, l'esecuzione di un ripristino di prova su un sistema diverso è una necessità assoluta. Questo approccio offre un modo per verificare che la copia di backup sia corretta e che i processi interni per il backup e il ripristino funzionino come previsto. Mentre il ripristino dei backup potrebbe essere un ostacolo in locale a causa dei requisiti dell'infrastruttura, è molto più facile da eseguire nel cloud fornendo temporaneamente le risorse necessarie per questo scopo. È corretto che ci sono strumenti forniti con HANA che possono controllare i file di backup sulla possibilità di ripristinare. Tuttavia, lo scopo di esercizi di ripristino frequenti è quello di testare il processo di ripristino del database e addestrare tale processo con il personale operativo.

Tenere presente che eseguire un semplice ripristino e verificare che HANA sia attivo e in esecuzione non è sufficiente. È consigliabile eseguire una verifica di coerenza delle tabelle per assicurarsi che il database ripristinato sia corretta. SAP HANA offre diversi tipi di verifiche della coerenza, descritti nella [Nota SAP 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informazioni sulla verifica della coerenza della tabella sono reperibili anche nel sito Web di SAP in [Table and Catalog Consistency Checks](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b) (Verifiche della coerenza di tabelle e cataloghi).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Vantaggi e svantaggi dei backup di HANA rispetto agli snapshot di archiviazione

SAP non esprime preferenze tra l'esecuzione del backup di HANA e lo snapshot di archiviazione. SAP elenca vantaggi e svantaggi, in modo che l'utente possa determinare quale usare a seconda della situazione e della tecnologia di archiviazione disponibile. Vedere [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Pianificazione del backup e strategia di ripristino).

In Azure, tenere presente che la funzionalità snapshot BLOB di Azure&#39;non fornisce la coerenza del file system tra più dischi (vedere [Uso di snapshot BLOB con PowerShell).](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) 

È anche necessario comprendere le implicazioni di fatturazione quando si lavora spesso con gli snapshot del BLOB, come descritto in questo articolo: [Informazioni sull'incremento dei costi dovuto agli snapshot](/rest/api/storageservices/understanding-how-snapshots-accrue-charges). Non è scontato come per l'uso dei dischi virtuali di Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione

Come documentato in precedenza, descrivere le funzionalità di backup degli snapshot di Backup di Azure, la coerenza del file system e delle applicazioni è obbligatoria quando si scattano snapshot di archiviazione. Il modo più semplice per evitare problemi è quello di arrestare SAP HANA o l'intera macchina virtuale. Qualcosa che non è fattibile per un'istanza di produzione.

> [!NOTE]
> I backup basati su snapshot del disco per SAP HANA nelle distribuzioni in cui vengono utilizzati più contenitori di database richiedono una versione minima di HANA 2.0 SP04
> 

Archiviazione di Azure, non fornisce coerenza del file system tra più dischi o volumi collegati a una macchina virtuale durante il processo snapshot. Ciò significa che la coerenza dell'applicazione durante lo snapshot deve essere recapitata dall'applicazione, in questo caso SAP HANA stesso. [SAP Nota 2039883](https://launchpad.support.sap.com/#/notes/2039883) contiene informazioni importanti sui backup SAP HANA da snapshot di archiviazione. Ad esempio, con i file system XFS, è necessario eseguire **xfs\_bloccare** prima di avviare uno snapshot di archiviazione per fornire la coerenza dell'applicazione (vedere [xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze) per i dettagli su **xfs\_freeze**).

Presupponendo che sia presente un file system XFS che si estende su quattro dischi virtuali di Azure, i passaggi seguenti offrono uno snapshot coerente che rappresenta l'area dati HANA:

1. Preparare lo snapshot dei dati HANACreate HANA data snapshot prepare
1. Bloccare i file system di tutti i dischi/volumi (ad esempio, utilizzare **xfs\_freeze**)
1. Creare tutti gli snapshot BLOB necessari in Azure.
1. Sbloccare il file system.
1. Confermare lo snapshot dei dati HANA (eliminerà lo snapshot)

Quando si usa la capacità di Backup di Azure di eseguire backup snapshot coerenti dell'applicazione, è necessario eseguire passaggi #1 necessario codificare/scriptper lo script pre-snapshot. Il servizio Backup di Azure eseguirà i passaggi #2 e #3. I passaggi #4 e #5 devono essere forniti nuovamente dal codice nello script post-snapshot. Se non si usa il servizio di backup di Azure, è inoltre necessario eseguire il codice/istruzione di script #2 e #3 da soli.
Ulteriori informazioni sulla creazione di snapshot di dati HANA sono disponibili in questi articoli:

- [Snapshot dei dati HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Ulteriori dettagli per eseguire #1 passaggio sono disponibili [nell'articolo Creare uno snapshot di dati (SQL nativo)More details](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) to perform step #1 can be found in article Create a Data Snapshot (Native SQL) 
- Dettagli per confermare/eliminare gli snapshot dei dati HANA in base alle esigenze del passaggio #5 sono disponibili nell'articolo [Create a Data Snapshot (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

È importante confermare lo snapshot HANA. A causa di &quot;Copy-on-Write&quot; SAP HANA potrebbe non richiedere altro spazio sul disco in questa modalità di preparazione dello snapshot. Non è neanche possibile avviare nuovi backup fino a quando lo snapshot di SAP HANA non viene confermato.


### <a name="sap-hana-backup-scheduling-strategy"></a>Strategia di pianificazione del backup di SAP HANA

L'articolo SAP HANA [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) indica un piano di base per eseguire i backup. Affidatevi alla documentazione SAP relativa a HANA e alle esperienze con altri DBMS nella definizione della strategia di backup/ripristino e del processo per SAP HANA. La sequenza dei diversi tipi di backup e il periodo di conservazione dipendono fortemente dai contratti di servizio che è necessario fornire.


### <a name="sap-hana-backup-encryption"></a>Crittografia del backup di SAP HANA

SAP HANA offre la crittografia di dati e log. Se i dati e il log SAP HANA non sono crittografati, i backup non vengono crittografati per impostazione predefinita. Tuttavia, SAP HANA offre una crittografia di backup separata come documentato in [SAP HANA Backup Encryption](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Se si eseguono versioni precedenti di SAP HANA, potrebbe essere necessario verificare se la crittografia di backup faceva già parte della funzionalità fornita.  


## <a name="next-steps"></a>Passaggi successivi
* [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md) descrive l'opzione di backup basato su file.
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere Disponibilità elevata SAP HANA (istanze di grandi dimensioni) e ripristino di emergenza in Azure.To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure.](hana-overview-high-availability-disaster-recovery.md)
