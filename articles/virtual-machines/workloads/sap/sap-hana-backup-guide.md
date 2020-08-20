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
ms.openlocfilehash: b5a83b3976dd3d3af1bfd5695815f7571d73dd9d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88652186"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guida del backup di SAP HANA in macchine virtuali di Azure

## <a name="getting-started"></a>Introduzione

La guida del backup per SAP HANA in esecuzione su macchine virtuali di Azure illustra esclusivamente argomenti specifici di Azure. Per informazioni generali sugli elementi correlati al backup SAP HANA, vedere la documentazione di SAP HANA. Si prevede di avere familiarità con le principali strategie di backup dei database, i motivi e le motivazioni per avere una strategia di backup valida e valida e che siano consapevoli dei requisiti dell'azienda per la procedura di backup, il periodo di conservazione dei backup e la procedura di ripristino.

SAP HANA è ufficialmente supportato in vari tipi di macchine virtuali di Azure, ad esempio Azure di serie M. Per un elenco completo delle macchine virtuali di Azure SAP HANA certificate e delle unità di istanze large di HANA, vedere [trovare piattaforme IaaS certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Microsoft Azure offre un numero di unità in cui SAP HANA viene eseguito non virtualizzato su server fisici. Questo servizio è denominato [istanze large di Hana](hana-overview-architecture.md). Questa guida non includerà i processi e gli strumenti di backup per le istanze large di HANA. Ma sarà limitato alle macchine virtuali di Azure. Per informazioni dettagliate sui processi di backup/ripristino con istanze large di HANA, vedere l'articolo [backup e ripristino di HLI](./hana-backup-restore.md).

Questo articolo è incentrato su tre possibilità di backup per SAP HANA in macchine virtuali di Azure:

- Backup di HANA tramite i [servizi di backup di Azure](../../../backup/backup-overview.md) 
- Backup di HANA nel file system di una macchina virtuale Linux in Azure (vedere [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md))
- Backup di HANA basato su snapshot di archiviazione usando la funzionalità snapshot del BLOB di archiviazione di Azure manualmente o il servizio backup di Azure


SAP HANA offre un'API di backup, che consente agli strumenti di backup di terze parti di integrarsi direttamente con SAP HANA I prodotti come il servizio backup di Azure o [CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) usano questa interfaccia proprietaria per attivare SAP Hana backup del log del database o di rollforward. 


Per informazioni sul modo in cui è possibile trovare il software SAP supportato in Azure, vedere l'articolo [relativo al software SAP supportato per le distribuzioni di Azure](./sap-supported-product-on-azure.md).

## <a name="azure-backup-service"></a>Servizio Backup di Azure

Il primo scenario illustrato è uno scenario in cui il servizio backup di Azure usa l' `backint` interfaccia SAP HANA per eseguire un backup di flusso con da un database SAP Hana. In alternativa, è possibile usare una funzionalità più generica del servizio backup di Azure per creare uno snapshot del disco coerente con l'applicazione e trasferirlo al servizio backup di Azure.

Backup di Azure si integra ed è certificato come soluzione di backup per SAP HANA usando l'interfaccia proprietaria SAP HANA denominata [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Per altri dettagli sulla soluzione, sulle funzionalità e sulle aree di Azure in cui è disponibile, leggere l'articolo [matrice di supporto per il backup di database SAP Hana nelle VM di Azure](../../../backup/sap-hana-backup-support-matrix.md#scenario-support). Per informazioni dettagliate e principi relativi al servizio backup di Azure per HANA, vedere l'articolo [relativo al backup di database SAP Hana nelle VM di Azure](../../../backup/sap-hana-db-about.md). 

La seconda possibilità per sfruttare il servizio backup di Azure consiste nel creare un backup coerente con l'applicazione usando snapshot del disco di archiviazione Premium di Azure. Altre archiviazioni di Azure certificate HANA, come [Azure ultra disk](../../disks-enable-ultra-ssd.md) e [Azure NetApp files](https://azure.microsoft.com/services/netapp/) non supportano questo tipo di snapshot tramite il servizio backup di Azure. Leggere questi articoli:

- [Pianificare l'infrastruttura di backup delle VM in Azure](../../../backup/backup-azure-vms-introduction.md)
- [Backup coerente con le applicazioni per macchine virtuali Linux in Azure](../../../backup/backup-azure-linux-app-consistent.md) 

Questa sequenza di attività emerge:

- Backup di Azure deve eseguire uno script pre-snapshot che inserisce l'applicazione, in questo caso SAP HANA, in uno stato coerente
- Poiché questo stato consistente è confermato, backup di Azure eseguirà gli snapshot del disco
- Dopo aver terminato gli snapshot, backup di Azure Annulla l'attività che ha eseguito nello script pre-snapshot
- Al termine dell'esecuzione, backup di Azure eseguirà il flusso dei dati nell'insieme di credenziali di backup

In caso di SAP HANA, la maggior parte dei clienti USA acceleratore di scrittura di Azure per i volumi che contengono il registro SAP HANA rollforward. Il servizio backup di Azure escluderà automaticamente questi volumi dagli snapshot. Questa esclusione non nuoce alla possibilità di eseguire il ripristino di HANA. Anche se impedisce la possibilità di eseguire il ripristino con quasi tutti gli altri sistemi DBMS supportati da SAP.

Lo svantaggio di questa possibilità è il fatto che è necessario sviluppare uno script di pre-snapshot e post-snapshot. Lo script pre-snapshot deve creare uno snapshot HANA e gestire eventuali casi di eccezione. Mentre lo script post-snapshot deve eliminare di nuovo lo snapshot HANA. Per altri dettagli sulla logica necessaria, iniziare con la [Nota di supporto SAP #2039883](https://launchpad.support.sap.com/#/notes/2039883). Le considerazioni della sezione "SAP HANA coerenza dei dati quando si eseguono gli snapshot di archiviazione" in questo articolo si applicano completamente a questo tipo di backup.

> [!NOTE]
> Backup basati su snapshot del disco per SAP HANA nelle distribuzioni in cui vengono usati più contenitori di database, richiede una versione minima di HANA 2,0 SP04
> 

Per informazioni dettagliate sugli snapshot di archiviazione, vedere più avanti in questo documento.

![La figura seguente mostra due possibilità per salvare lo stato corrente della VM](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Altri metodi di backup HANA
È possibile prendere in considerazione altri tre metodi o percorsi di backup:

- Esecuzione del backup su una condivisione NFS basata su Azure NetApp Files (e). E di nuovo è in grado di creare snapshot dei volumi in cui vengono archiviati i backup. Data la velocità effettiva necessaria per la scrittura dei backup, questa soluzione potrebbe diventare un metodo costoso. Sebbene sia facile da stabilire perché HANA può scrivere i backup direttamente nella condivisione NFS nativa di Azure
- Esecuzione del backup di HANA su dischi collegati alla macchina virtuale di SDD Standard o archiviazione Premium di Azure. Come passaggio successivo è possibile copiare i file di backup nell'archiviazione BLOB di Azure. Questa strategia potrebbe essere una scelta interessante per i prezzi
- Esecuzione del backup di HANA su dischi collegati alla macchina virtuale di SDD Standard o archiviazione Premium di Azure. Come passaggio successivo, il disco viene snapshotted a intervalli regolari. Dopo il primo snapshot, è possibile usare gli snapshot incrementali per ridurre i costi

![La figura seguente mostra le opzioni per l'esecuzione di un backup dei file di SAP HANA nella macchina virtuale](media/sap-hana-backup-guide/other-hana-backup-paths.png)

La figura seguente mostra le opzioni per eseguire un backup dei file SAP HANA nella macchina virtuale e quindi archiviare i file di backup HANA in una posizione diversa tramite strumenti differenti. Tuttavia, tutte le soluzioni che non coinvolgono un servizio di backup di terze parti o un servizio di backup di Azure presentano diversi ostacoli in comune. Alcune di esse possono essere elencate, ad esempio l'amministrazione della conservazione, il processo di ripristino automatico e il recupero temporizzato automatico come servizio backup di Azure o altri gruppi e servizi di backup di terze parti specializzati. Molti di questi servizi di terze parti possono essere eseguiti in Azure. 


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
Indipendentemente dal metodo di backup, l'esecuzione di un ripristino di test in un sistema diverso è una necessità assoluta. Questo approccio offre un modo per verificare che la copia di backup sia corretta e che i processi interni per il backup e il ripristino funzionino come previsto. Il ripristino dei backup potrebbe essere un ostacolo locale a causa del requisito dell'infrastruttura, ma è molto più semplice da eseguire nel cloud fornendo temporaneamente le risorse necessarie a questo scopo. È corretto che siano disponibili strumenti con HANA in grado di controllare i file di backup in caso di possibilità di ripristino. Tuttavia, lo scopo degli esercizi di ripristino frequenti è testare il processo di ripristino del database ed eseguire il training del processo con il personale operativo.

Tenere presente che eseguire un semplice ripristino e verificare che HANA sia attivo e in esecuzione non è sufficiente. È consigliabile eseguire una verifica di coerenza della tabella per assicurarsi che il database ripristinato sia corretto. SAP HANA offre diversi tipi di verifiche della coerenza, descritti nella [Nota SAP 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informazioni sulla verifica della coerenza della tabella sono reperibili anche nel sito Web di SAP in [Table and Catalog Consistency Checks](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b) (Verifiche della coerenza di tabelle e cataloghi).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Vantaggi e svantaggi dei backup di HANA rispetto agli snapshot di archiviazione

SAP non esprime preferenze tra l'esecuzione del backup di HANA e lo snapshot di archiviazione. SAP elenca vantaggi e svantaggi, in modo che l'utente possa determinare quale usare a seconda della situazione e della tecnologia di archiviazione disponibile. Vedere [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Pianificazione del backup e strategia di ripristino).

In Azure, tenere presente che la&#39;funzionalità di snapshot dei BLOB di Azure non fornisce file system coerenza tra più dischi (vedere [uso di snapshot BLOB con PowerShell](/archive/blogs/cie/using-blob-snapshots-with-powershell)). 

È anche necessario comprendere le implicazioni di fatturazione quando si lavora spesso con gli snapshot del BLOB, come descritto in questo articolo: [Informazioni sull'incremento dei costi dovuto agli snapshot](/rest/api/storageservices/understanding-how-snapshots-accrue-charges). Non è scontato come per l'uso dei dischi virtuali di Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Coerenza dei dati SAP HANA durante l'esecuzione degli snapshot di archiviazione

Come descritto in precedenza, la descrizione delle funzionalità di backup di snapshot di backup di Azure, file system e la coerenza delle applicazioni è obbligatoria durante l'esecuzione degli snapshot di archiviazione. Il modo più semplice per evitare problemi è quello di arrestare SAP HANA o l'intera macchina virtuale. Un elemento che non è fattibile per un'istanza di produzione.

> [!NOTE]
> Backup basati su snapshot del disco per SAP HANA nelle distribuzioni in cui vengono usati più contenitori di database, richiede una versione minima di HANA 2,0 SP04
> 

Archiviazione di Azure non fornisce file system coerenza tra più dischi o volumi collegati a una macchina virtuale durante il processo di snapshot. Ciò significa che la coerenza dell'applicazione durante lo snapshot deve essere recapitata dall'applicazione, in questo caso SAP HANA. La [Nota SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883) contiene informazioni importanti sui backup SAP Hana da snapshot di archiviazione. Con i file system XFS, ad esempio, è necessario eseguire **XFS \_ Freeze** prima di avviare uno snapshot di archiviazione per garantire la coerenza dell'applicazione (vedere [XFS \_ Freeze (8)-Linux man page](https://linux.die.net/man/8/xfs_freeze) per informazioni dettagliate su **XFS \_ Freeze**).

Presupponendo che sia presente un file system XFS che si estende su quattro dischi virtuali di Azure, i passaggi seguenti offrono uno snapshot coerente che rappresenta l'area dati HANA:

1. Crea preparazione snapshot dei dati HANA
1. Blocca i file System di tutti i dischi e i volumi (ad esempio, USA **XFS \_ Freeze**)
1. Creare tutti gli snapshot BLOB necessari in Azure.
1. Sbloccare il file system.
1. Confermare lo snapshot dei dati HANA (eliminerà lo snapshot)

Quando si usa la funzionalità di backup di Azure per eseguire backup di snapshot coerenti con l'applicazione, i passaggi #1 devono essere codificati/scritti dall'utente in per lo script pre-snapshot. Il servizio backup di Azure eseguirà i passaggi #2 e #3. I passaggi #4 e #5 devono essere forniti nuovamente dal codice nello script post-snapshot. Se non si usa il servizio backup di Azure, è anche necessario codificare/eseguire lo script dei passaggi #2 e #3 autonomamente.
Altre informazioni sulla creazione di snapshot dei dati HANA sono disponibili negli articoli seguenti:

- [Snapshot di dati HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Per informazioni dettagliate sull'esecuzione di un passaggio #1 vedere [l'articolo creare uno snapshot dei dati (SQL nativo)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Per informazioni dettagliate su come confermare/eliminare gli snapshot di dati HANA, vedere il passaggio #5 sono disponibili nell'articolo [creare uno snapshot dei dati (SQL nativo)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

È importante confermare lo snapshot HANA. A causa di &quot;Copy-on-Write&quot; SAP HANA potrebbe non richiedere altro spazio sul disco in questa modalità di preparazione dello snapshot. Non è neanche possibile avviare nuovi backup fino a quando lo snapshot di SAP HANA non viene confermato.


### <a name="sap-hana-backup-scheduling-strategy"></a>Strategia di pianificazione del backup di SAP HANA

L'articolo SAP HANA [pianificazione della strategia di backup e ripristino](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) dichiara un piano di base per eseguire i backup. Affidati alla documentazione SAP relativa a HANA e alle tue esperienze con altri sistemi DBMS per definire la strategia di backup/ripristino e il processo per SAP HANA. La sequenza di diversi tipi di backup e il periodo di memorizzazione dipendono fortemente dai contratti di contratto che è necessario fornire.


### <a name="sap-hana-backup-encryption"></a>Crittografia del backup di SAP HANA

SAP HANA offre la crittografia di dati e log. Se SAP HANA dati e il log non sono crittografati, i backup non vengono crittografati per impostazione predefinita. Tuttavia, SAP HANA offre una crittografia di backup separata, come documentato in [SAP Hana crittografia dei backup](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Se si eseguono versioni precedenti di SAP HANA, potrebbe essere necessario controllare se la crittografia del backup era parte della funzionalità già disponibile.  


## <a name="next-steps"></a>Passaggi successivi
* [Backup di SAP HANA di Azure a livello di file](sap-hana-backup-file-level.md) descrive l'opzione di backup basato su file.
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [disponibilità elevata e ripristino di emergenza di SAP Hana (istanze large) in Azure](hana-overview-high-availability-disaster-recovery.md).
