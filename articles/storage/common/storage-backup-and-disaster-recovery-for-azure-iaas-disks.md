---
title: Backup e ripristino di emergenza per dischi IaaS in Azure | Microsoft Docs
description: "Questo articolo illustra come pianificare il backup e il ripristino di emergenza di macchine virtuali e dischi IaaS in Azure. Questo documento è relativo a dischi gestiti e non gestiti."
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: luywang
ms.openlocfilehash: b675daf4874a4bfe663a77b9e1097c00f00a13f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Backup e ripristino di emergenza per dischi IaaS di Azure

Questo articolo illustra come pianificare il backup e il ripristino di emergenza di macchine virtuali (VM) e dischi IaaS in Azure. Questo documento è relativo a dischi gestiti e non gestiti.

Per prima cosa, verranno esaminate le funzionalità di tolleranza di errore predefinite della piattaforma di Azure che consentono la protezione da errori locali. Saranno quindi illustrati gli scenari di emergenza non pienamente coperti dalle funzionalità predefinite. Questo è l'argomento principale trattato nel documento. Verranno forniti anche alcuni esempi di scenari di carico di lavoro per cui è possibile applicare diverse considerazioni relative a backup e ripristino di emergenza. Saranno infine esaminate le soluzioni possibili per il ripristino di emergenza di dischi IaaS. 

## <a name="introduction"></a>Introduzione

La piattaforma Azure usa diversi metodi per la ridondanza e la tolleranza di errore, in modo da contribuire alla protezione dei clienti da errori hardware localizzati. Gli errori locali possono includere problemi relativi a un computer server di Archiviazione di Azure che archivia parte dei dati per un disco virtuale o errori di un'unità SSD o HDD su tale server. Questi errori isolati relativi ai componenti hardware si possono verificare durante il normale funzionamento. 

La piattaforma Azure è progettata per essere resiliente agli errori di questo tipo. Le emergenze gravi possono provocare errori oppure l'inaccessibilità di molti server di archiviazione o addirittura di un intero data center. Anche se le VM e i dischi sono normalmente protetti da errori localizzati, sono necessari passaggi aggiuntivi per proteggere il carico di lavoro da errori irreversibili a livello di area, ad esempio un'emergenza grave, che possono interessare le macchine virtuali e i dischi.

Oltre a potenziali errori della piattaforma, è possibile che si verifichino problemi con un'applicazione o i dati dei clienti. Ad esempio, è possibile che una nuova versione dell'applicazione apporti accidentalmente una modifica ai dati che ne causa l'interruzione. In tal caso, è consigliabile ripristinare una versione precedente dell'applicazione e dei dati che contenga l'ultimo stato valido noto. A questo scopo, sono necessari backup regolari.

Per assicurare il ripristino di emergenza a livello di area, è necessario eseguire il backup dei dischi IaaS delle VM in un'area diversa. 

Prima di esaminare le opzioni di backup e ripristino di emergenza, è utile esaminare i metodi disponibili per la gestione degli errori localizzati.

### <a name="azure-iaas-resiliency"></a>Resilienza IaaS di Azure

Per *resilienza* si intende la tolleranza degli errori normali che si verificano nei componenti hardware. La resilienza è la capacità di recupero da errori che consente un funzionamento continuativo. Non si tratta di evitare gli errori, ma di rispondere agli errori in modo da evitare inattività o perdita dei dati. L'obiettivo della resilienza consiste nel ripristinare uno stato completamente funzionale dell'applicazione dopo un errore. Le macchine virtuali e i dischi di Azure sono stati progettati per essere resilienti agli errori hardware comuni. Verrà ora esaminato il modo in cui la piattaforma IaaS di Azure offre la resilienza.

Una macchina virtuale è costituita principalmente da due parti: un server di calcolo e i dischi persistenti. Entrambi influiscono sulla tolleranza di errore di una macchina virtuale.

Se nel server host di calcolo di Azure che ospita la VM si verifica un errore hardware (eventualità rara), Azure è progettato per il ripristino automatico della VM in un altro server. Se ciò accade, il computer viene riavviato e la macchina virtuale risulterà attiva dopo una breve attesa. Azure rileva automaticamente questi errori hardware ed esegue i ripristini per assicurare che le VM dei clienti siano disponibili appena possibile.

Per quanto riguarda i dischi IaaS, la durabilità dei dati è essenziale per una piattaforma di archiviazione persistente. I clienti di Azure hanno applicazioni aziendali importanti in esecuzione su IaaS e dipendono dalla persistenza dei dati. La progettazione della protezione di Azure per questi dischi IaaS prevede tre copie ridondanti dei dati archiviate localmente. Queste copie offrono una durabilità elevata rispetto agli errori locali. In caso di errore di uno dei componenti hardware che contiene il disco, la VM non subisce ripercussioni, perché sono disponibili due copie aggiuntive per supportare le richieste del disco. Non si verificano problemi anche in caso di errore contemporaneo di due componenti hardware che supportano un disco, un'eventualità estremamente rara. 

Per assicurare che siano sempre mantenute tre repliche, Archiviazione di Azure crea automaticamente una nuova copia dei dati in background nel caso in cui una delle tre copie non risulti più disponibile. Non dovrebbe essere quindi necessario usare RAID con i dischi di Azure per ottenere la tolleranza di errore. Una semplice configurazione di tipo RAID 0 dovrebbe essere sufficiente per lo striping dei dischi, se necessario, per creare volumi più elevati.

Grazie a questa architettura, Azure ha offerto in modo costante una durabilità di livello aziendale per i dischi IaaS, con una [percentuale di frequenza di errori annualizzata](https://en.wikipedia.org/wiki/Annualized_failure_rate) pari a zero, ovvero la migliore del settore.

Gli errori hardware localizzati nell'host di calcolo o nella piattaforma di archiviazione a volte possono provocare la mancata disponibilità temporanea della VM coperta dal [Contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) per la disponibilità delle macchine virtuali. Azure offre anche un Contratto di servizio leader di settore per singole istanze di VM che usano dischi di Archiviazione Premium di Azure.

Per proteggere i carichi di lavoro dell'applicazione dall'inattività causata dalla mancata disponibilità temporanea di un disco o di una VM, i clienti possono usare i [set di disponibilità](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability). Due o più macchine virtuali in un set di disponibilità offrono la ridondanza per l'applicazione. Azure crea quindi queste VM e questi dischi in domini di errore separati con diverso tipo di alimentazione, diverse risorse di rete e diversi componenti del server. 

A causa di questi domini di errore separati, gli errori hardware localizzati in genere non interessano più VM contemporaneamente nello stesso set. La presenza di domini di errore separati offre una disponibilità elevata per l'applicazione. È consigliabile usare i set di disponibilità quando è necessaria la disponibilità elevata. La sezione successiva prende in esame l'aspetto del ripristino di emergenza.

### <a name="backup-and-disaster-recovery"></a>Backup e ripristino di emergenza

Il ripristino di emergenza è la capacità di recupero da eventi imprevisti rari ma gravi, ad esempio errori non temporanei su larga scala, come un'interruzione del servizio che interessa un'intera area. Il ripristino di emergenza include il backup dei dati e l'archiviazione e potrebbe includere l'intervento manuale, ad esempio il ripristino di un database da un backup.

È possibile che la protezione incorporata della piattaforma di Azure dagli errori localizzati non protegga completamente le VM o i dischi se un'emergenza grave provoca interruzioni su larga scala, ad esempio eventi irreversibili come uragani, terremoti, incendi o errori su larga scala delle unità hardware che colpiscono un data center. È anche possibile che si verifichino errori a causa di problemi delle applicazioni o dei dati.

Per contribuire alla protezione dei carichi di lavoro IaaS dalle interruzioni, è consigliabile pianificare la ridondanza e disporre di copie di backup per abilitare il ripristino. Per il ripristino di emergenza, è necessario eseguire il backup in un'area geografica diversa rispetto al sito primario. Ciò consente di assicurare che il backup non sia interessato dallo stesso evento che ha influito originariamente sulle VM o sui dischi. Per altre informazioni, vedere [Disaster recovery for Azure applications](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) (Ripristino di emergenza per le applicazioni basate su Azure).

Le considerazioni relative al ripristino di emergenza potrebbero includere gli aspetti seguenti:

- Disponibilità elevata: la capacità dell'applicazione di continuare a essere eseguita in uno stato integro, senza tempi di inattività significativi. Per *stato integro* si intende che l'applicazione è reattiva e gli utenti possono connettersi all'applicazione e interagire con essa. Potrebbe essere necessario che alcune applicazioni e alcuni database cruciali siano sempre disponibili, anche in caso di errori sulla piattaforma. Per questi carichi di lavoro, potrebbe essere necessario pianificare la ridondanza per l'applicazione e per i dati.

- Durabilità dei dati: in alcuni casi la considerazione principale consiste nell'assicurare che i dati vengano conservati se si verifica un'emergenza. Potrebbe quindi essere richiesto un backup dei dati in una posizione diversa. Per questi carichi di lavoro potrebbe non essere necessaria una ridondanza completa per l'applicazione, ma solo un backup regolare dei dischi.

## <a name="backup-and-dr-scenarios"></a>Scenari di backup e ripristino di emergenza

Verranno esaminati alcuni esempi tipici di scenari di carichi di lavoro delle applicazioni e saranno illustrate le considerazioni relative alla pianificazione per il ripristino di emergenza.

### <a name="scenario-1-major-database-solutions"></a>Scenario 1: Principali soluzioni di database

Si consideri un server di database di produzione, ad esempio SQL Server oppure Oracle, in grado di supportare la disponibilità elevata. Le applicazioni di produzione cruciali e gli utenti dipendono da questo database. È possibile che il piano di ripristino di emergenza per questo sistema richieda il supporto dei requisiti seguenti:

- I dati devono essere protetti e recuperabili.
- Il server deve essere disponibile per l'uso.

Il piano di ripristino di emergenza potrebbe richiedere che venga conservata una replica del database in un'area diversa rispetto al backup. In base ai requisiti per la disponibilità del server e per il ripristino dei dati, si potrebbe usare una soluzione di tipo sito di replica attiva-attiva o attiva-passiva oppure una soluzione che preveda backup offline periodici dei dati. I database relazionali, come SQL Server e Oracle, offrono diverse opzioni per la replica. Per SQL Server, usare [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx) per ottenere la disponibilità elevata.

Anche i database NoSQL, come MongoDB, supportano le [repliche](https://docs.mongodb.com/manual/replication/) per la ridondanza. Vengono usate le repliche per la disponibilità elevata.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenario 2: Cluster di VM ridondanti

Si consideri un carico di lavoro gestito da un cluster di VM che forniscono ridondanza e bilanciamento del carico, ad esempio un cluster Cassandra distribuito in un'area. Questo tipo di architettura fornisce già un livello elevato di ridondanza entro tale area. Per proteggere il carico di lavoro da un errore a livello di area, è tuttavia necessario prendere in considerazione la distribuzione del cluster su due aree o la creazione di backup periodici in un'altra area.

### <a name="scenario-3-iaas-application-workload"></a>Scenario 3: Carico di lavoro delle applicazioni IaaS

Verrà ora esaminato un carico di lavoro dell'applicazione IaaS. Ad esempio, potrebbe trattarsi di un carico di lavoro di produzione tipico in esecuzione in una VM di Azure o di un server Web o un file server che include i contenuti e altre risorse di un sito. Potrebbe anche trattarsi di un'applicazione aziendale personalizzata in esecuzione in una VM che ha archiviato i rispettivi dati, le risorse e lo stato dell'applicazione nei dischi della VM. In questo caso è importante assicurarsi di eseguire backup con regolarità. La frequenza dei backup deve essere basata sulla natura del carico di lavoro delle VM. Ad esempio, se l'applicazione viene eseguita ogni giorno e modifica i dati, è necessario eseguire il backup ogni ora.

Un altro esempio è costituito da un server di report che esegue il pull dei dati da altre origini e genera report aggregati. La perdita di questa VM o di questi dischi potrebbe comportare la perdita dei report. Potrebbe essere tuttavia possibile eseguire di nuovo il processo di creazione di report e rigenerare l'output. In questo caso non si verifica effettivamente una perdita di dati, anche in caso di emergenza per il server di report. È quindi possibile un livello di tolleranza maggiore per la perdita di parte dei dati nel server di report. In tale caso, una minore frequenza dei backup può consentire di ridurre i costi.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenario 4: Problemi relativi ai dati delle applicazioni IaaS

I problemi relativi ai dati delle applicazioni IaaS costituiscono un'altra possibilità. Si consideri un'applicazione che calcola, gestisce e fornisce dati commerciali cruciali, ad esempio le informazioni sui prezzi. Una nuova versione dell'applicazione presenta un bug del software che comporta un calcolo non corretto del prezzo e ha danneggiato i dati commerciali esistenti forniti dalla piattaforma. La soluzione ottimale in questo caso consiste nel ripristinare una versione precedente dell'applicazione e dei dati. Per abilitare questo approccio, eseguire backup periodici del sistema.

## <a name="disaster-recovery-solution-azure-backup"></a>Soluzione per il ripristino di emergenza: Backup di Azure 

[Backup di Azure](https://azure.microsoft.com/services/backup/) viene usato per i backup e il ripristino di emergenza e funziona con [dischi gestiti](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/managed-disks-overview) e [non gestiti](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks). È possibile creare un processo di backup con backup basati su orari specifici e con criteri semplici per il ripristino delle VM e la conservazione dei backup. 

Se si usano [dischi di Archiviazione Premium](https://docs.microsoft.com/en-us/azure/storage/common/storage-premium-storage), [dischi gestiti](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/managed-disks-overview) o altri tipi di dischi con l'opzione di [archiviazione con ridondanza locale](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#locally-redundant-storage), è particolarmente importante eseguire backup periodici per il ripristino di emergenza. Backup di Azure archivia i dati nell'insieme di credenziali dei servizi di ripristino per la conservazione a lungo termine. Scegliere l'opzione di [archiviazione con ridondanza geografica](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#geo-redundant-storage) per l'insieme di credenziali dei servizi di ripristino di backup. Questa opzione assicura che i backup vengano replicati in un'area di Azure differente per la protezione da emergenze a livello di area.

Per i [dischi non gestiti](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) è possibile usare il tipo di archiviazione con ridondanza locale per dischi IaaS, ma occorre assicurarsi che Backup di Azure sia abilitato con l'opzione di archiviazione con ridondanza geografica per l'insieme di credenziali dei servizi di ripristino.

> [!NOTE]
> Se si usa l'opzione di [archiviazione con ridondanza geografica](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#geo-redundant-storage) o di [archiviazione con ridondanza geografica e accesso in lettura](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#read-access-geo-redundant-storage) per i dischi non gestiti, saranno comunque necessari snapshot coerenti per il backup e il ripristino di emergenza. Usare [Backup di Azure](https://azure.microsoft.com/services/backup/) o [snapshot coerenti](#alternative-solution-consistent-snapshots).

 La tabella seguente fornisce un riepilogo delle soluzioni disponibili per il ripristino di emergenza.

| Scenario | Replica automatica | Soluzione di ripristino di emergenza |
| --- | --- | --- |
| Dischi di Archiviazione Premium | Locale ([archiviazione con ridondanza locale](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#locally-redundant-storage)) | [Backup di Azure](https://azure.microsoft.com/services/backup/) |
| Dischi gestiti | Locale ([archiviazione con ridondanza locale](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#locally-redundant-storage)) | [Backup di Azure](https://azure.microsoft.com/services/backup/) |
| Dischi non gestiti con archiviazione con ridondanza locale | Locale ([archiviazione con ridondanza locale](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#locally-redundant-storage)) | [Backup di Azure](https://azure.microsoft.com/services/backup/) |
| Dischi non gestiti con archiviazione con ridondanza geografica | Tra aree ([archiviazione con ridondanza geografica](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#geo-redundant-storage)) | [Backup di Azure](https://azure.microsoft.com/services/backup/)<br/>[Snapshot coerenti](#alternative-solution-consistent-snapshots) |
| Dischi non gestiti con archiviazione con ridondanza geografica e accesso in lettura | Tra aree ([archiviazione con ridondanza geografica e accesso in lettura](storage-redundancy.md#read-access-geo-redundant-storage)) | [Backup di Azure](https://azure.microsoft.com/services/backup/)<br/>[Snapshot coerenti](#alternative-solution-consistent-snapshots) |

La disponibilità elevata si ottiene usando dischi gestiti in un set di disponibilità insieme a Backup di Azure. Se si usano dischi non gestiti, è comunque possibile usare Backup di Azure per il ripristino di emergenza. Se non si può usare Backup di Azure, la creazione di [snapshot coerenti](#alternative-solution-consistent-snapshots), come illustrato in una sezione successiva, è una soluzione alternativa per il backup e il ripristino di emergenza.

Le opzioni per la disponibilità elevata, il backup e il ripristino di emergenza a livello di applicazione o infrastruttura possono essere rappresentate come segue:

| Level |   Disponibilità elevata   | Backup o ripristino di emergenza |
| --- | --- | --- |
| Applicazione | SQL Server AlwaysOn | Backup di Azure |
| Infrastruttura    | Set di disponibilità  | Archiviazione con ridondanza geografica con snapshot coerenti |

### <a name="using-azure-backup"></a>Uso di Backup di Azure 

[Backup di Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-introduction) può eseguire il backup delle VM che eseguono Windows o Linux nell'insieme di credenziali dei servizi di ripristino di Azure. Le operazioni di backup e ripristino dei dati aziendali critici sono ulteriormente complicate dal fatto che il backup di tali dati deve essere eseguito mentre le applicazioni che generano i dati sono in esecuzione. 

Per risolvere questo problema, Backup di Azure fornisce funzionalità di backup coerenti con l'applicazione per i carichi di lavoro Microsoft. Si avvale del Servizio Copia Shadow del volume per garantire che i dati vengano scritti correttamente nella risorsa di archiviazione. Per le macchine virtuali Linux sono possibili solo backup coerenti con i file, perché Linux non offre una funzionalità equivalente al Servizio Copia Shadow del volume.

![Flusso di Backup di Azure][1]

Quando Backup di Azure avvia un processo di backup all'orario pianificato, attiva l'estensione di backup installata nella VM, in modo che crei uno snapshot temporizzato. Uno snapshot viene acquisito in combinazione con il Servizio Copia Shadow del volume per ottenere uno snapshot coerente dei dischi nella macchina virtuale senza che sia necessario spegnerla. L'estensione di backup nella VM scarica tutte le operazioni di scrittura prima di creare uno snapshot coerente di tutti i dischi. Dopo l'acquisizione dello snapshot, Backup di Azure trasferisce i dati nell'insieme di credenziali di backup. Per aumentare l'efficienza del processo di backup, il servizio identifica e trasferisce solo i blocchi di dati che sono stati modificati dopo l'ultimo backup.

Per il ripristino, è possibile visualizzare i backup disponibili tramite Backup di Azure e quindi avviare il ripristino. È possibile creare e ripristinare i backup di Azure tramite il [portale di Azure](https://portal.azure.com/), [con PowerShell](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-automation) oppure tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/xplat-cli-install). 

### <a name="steps-to-enable-a-backup"></a>Procedura di abilitazione dei backup

Usare la procedura seguente per abilitare i backup delle VM tramite il [portale di Azure](https://portal.azure.com/). La procedura presenta alcune variazioni in base all'esatto scenario in uso. Per informazioni dettagliate, vedere la documentazione di [Backup di Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-introduction). Backup di Azure [supporta anche le VM con dischi gestiti](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Creare un insieme di credenziali dei servizi di ripristino per una macchina virtuale:

    a. Nel [portale di Azure](https://portal.azure.com/) esplorare **Tutte le risorse** e individuare **Insiemi di credenziali dei servizi di ripristino**.

    b. Dal menu **Insiemi di credenziali dei servizi di ripristino** scegliere **Aggiungi** e seguire la procedura per la creazione di un nuovo insieme di credenziali nella stessa area in cui si trova la VM. Se ad esempio la VM si trova nell'area Stati Uniti occidentali, scegliere Stati Uniti occidentali per l'insieme di credenziali.

2.  Verificare la replica delle risorse di archiviazione per l'insieme di credenziali appena creato. Accedere all'insieme di credenziali in **Insiemi di credenziali dei servizi di ripristino** e passare a **Impostazioni** > **Configurazione backup**. Assicurarsi che l'opzione di **archiviazione con ridondanza geografica** sia selezionata per impostazione predefinita. In questo modo si garantisce che l'insieme di credenziali venga replicato automaticamente in un data center secondario. Ad esempio, l'insieme di credenziali nell'area Stati Uniti occidentali viene replicato automaticamente nell'area Stati Uniti orientali.

3.  Configurare i criteri di backup e selezionare la VM dalla stessa interfaccia utente.

4.  Assicurarsi che l'agente di Backup sia installato nella VM. Se la macchina virtuale è stata creata tramite un'immagine della raccolta di Azure, l'agente di backup è già installato. In caso contrario, ovvero se si usa un'immagine personalizzata, seguire le istruzioni per [installare l'agente di macchine virtuali nella macchina virtuale](https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent-on-the-virtual-machine).

5.  Assicurarsi che la VM consenta la connettività di rete per permettere il funzionamento del servizio Backup. Seguire le istruzioni per la [connettività di rete](https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-vms-prepare#network-connectivity).

6.  Al termine della procedura precedente, il backup viene eseguito a intervalli regolari, come specificato nei criteri di backup. Se necessario, è possibile attivare manualmente il primo backup dal dashboard dell'insieme di credenziali nel portale di Azure.

Per l'automazione di Backup di Azure tramite gli script, fare riferimento ai [cmdlet di PowerShell per il backup delle VM](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-automation).

### <a name="steps-for-recovery"></a>Procedura per il ripristino

Se è necessario ripristinare o ricreare una VM, è possibile ripristinarla da qualsiasi punto di ripristino del backup nell'insieme di credenziali. Sono disponibili un paio di opzioni diverse per l'esecuzione del ripristino:

-   È possibile creare una nuova VM come rappresentazione temporizzata della macchina virtuale sottoposta a backup.

-   È possibile ripristinare i dischi e quindi usare il modello per la macchina virtuale per personalizzare e ricreare la VM ripristinata. 

Per altre informazioni, vedere le istruzioni per [usare il portale di Azure per ripristinare macchine virtuali](https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-restore-vms#restoring-a-vm-during-azure-datacenter-disaster). Questo documento illustra anche la procedura specifica per il ripristino di VM sottoposte a backup in un data center associato usando l'insieme di credenziali di backup con ridondanza geografica in caso di emergenza nel data center primario. In questo caso, Backup di Azure usa il servizio di calcolo dell'area secondaria per creare la macchina virtuale ripristinata.

È anche possibile usare PowerShell per il [ripristino di una VM](https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-restore-vms#restoring-a-vm-during-azure-datacenter-disaster) o per la [creazione di una nuova VM dai dischi ripristinati](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Soluzione alternativa: snapshot coerenti

Se non si può usare Backup di Azure, è possibile implementare un meccanismo di backup personalizzato tramite gli snapshot. La creazione di snapshot coerenti per tutti i dischi usati da una VM e quindi la replica di tali snapshot in un'altra area è un processo complicato. Azure considera quindi l'uso del servizio Backup come un'opzione migliore rispetto alla creazione di una soluzione personalizzata. 

Se si usa l'archiviazione con ridondanza geografica e accesso in lettura o l'archiviazione con ridondanza geografica per i dischi, gli snapshot vengono replicati automaticamente in un data center secondario. Se si usa l'archiviazione con ridondanza locale per i dischi, è necessario eseguire manualmente la replica dei dati. Per altre informazioni, vedere [Eseguire il backup dei dischi di VM non gestiti con snapshot incrementali](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/incremental-snapshots).

Uno snapshot è una rappresentazione di un oggetto in un punto specifico del tempo. Uno snapshot comporta la fatturazione per le dimensioni incrementali dei dati in esso contenuti. Per altre informazioni, vedere [Creare uno snapshot del BLOB](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-snapshots).

### <a name="create-snapshots-while-the-vm-is-running"></a>Creare snapshot durante l'esecuzione della VM

Anche se è possibile creare uno snapshot in qualsiasi momento, se la VM è in esecuzione, lo streaming dei dati nei dischi è ancora in corso e gli snapshot potrebbero contenere operazioni parziali che erano in corso durante la creazione dello snapshot. Se l'operazione interessa diversi dischi, è inoltre possibile che gli snapshot di dischi diversi siano stati creati in momenti diversi. Ciò significa che questi snapshot potrebbero non essere coordinati. Ciò risulta particolarmente problematico per i volumi con striping, i cui file potrebbero essere danneggiati in caso di modifiche durante il backup.

Per evitare questo problema, è necessario che il processo di backup implementi i passaggi seguenti:

1.  Blocco di tutti i dischi.

2.  Scaricamento di tutte le operazioni di scrittura in sospeso.

3.  [Creazione di uno snapshot del BLOB](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-snapshots) per tutti i dischi.

Alcune applicazioni Windows, ad esempio SQL Server, forniscono un meccanismo di backup coordinato tramite il Servizio Copia Shadow del volume per creare backup coerenti con l'applicazione. In Linux è possibile usare uno strumento come fsfreeze per coordinare i dischi. Questo strumento assicura backup coerenti con i file, ma non snapshot coerenti con l'applicazione. Questo processo è complesso, pertanto è consigliabile prendere in considerazione l'uso di [Backup di Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-introduction) o di una soluzione di backup di terze parti che implementa già questa procedura.

Il processo precedente consente di ottenere una raccolta di snapshot coordinati per tutti i dischi della VM, rappresentando una visualizzazione temporizzata specifica della VM. Questo è un punto di ripristino del backup per la VM. È possibile ripetere il processo a intervalli pianificati per creare backup periodici. Vedere [Copiare gli snapshot in un'altra area](#copy-the-snapshots-to-another-region) per la procedura per la copia degli snapshot in un'altra area per il ripristino di emergenza.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Creare snapshot con VM offline

Un'altra opzione per la creazione di backup coerenti consiste nell'arresto della VM e nella creazione di snapshot dei BLOB di ogni disco. La creazione di snapshot dei BLOB risulta più semplice rispetto alla coordinazione degli snapshot di una VM in esecuzione, ma richiede qualche minuto di inattività.

1. Arrestare la VM.

2. Creare uno snapshot di ogni BLOB di disco rigido virtuale, operazione che richiede solo pochi secondi.

    Per creare uno snapshot, è possibile usare [PowerShell](https://docs.microsoft.com/en-us/azure/storage/common/storage-powershell-guide-full#how-to-manage-azure-blob-snapshots), l'[API REST di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/xplat-cli-install) o una delle librerie client di Archiviazione di Azure, come la [libreria client di archiviazione per .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Avviare la VM, in modo da terminare il tempo di inattività. L'intero processo termina in genere in pochi minuti.

Questo processo restituisce una raccolta di snapshot coerenti per tutti i dischi, fornendo un punto di ripristino del backup per la VM.

### <a name="copy-the-snapshots-to-another-region"></a>Copiare gli snapshot in un'altra area

La sola creazione degli snapshot potrebbe non essere sufficiente per il ripristino di emergenza. È anche necessario replicare i backup degli snapshot in un'altra area.

Se si usa l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura per i dischi, gli snapshot vengono replicati automaticamente nell'area secondaria. È possibile che si verifichi un ritardo di pochi minuti prima della replica. Se il data center primario smette di funzionare prima del completamento della replica degli snapshot, non sarà possibile accedere agli snapshot dal data center secondario. La probabilità che questo problema si verifichi è bassa.

> [!NOTE] 
> Il semplice posizionamento dei dischi in un account di archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura non è sufficiente a proteggere la VM da situazioni di emergenza. È necessario creare anche snapshot coordinati o usare Backup di Azure. Questa operazione è necessaria per il ripristino di uno stato coerente della VM.

Se si usa l'archiviazione con ridondanza locale, è necessario copiare gli snapshot in un account di archiviazione diverso immediatamente dopo la creazione dello snapshot. La destinazione della copia potrebbe essere un account di archiviazione con ridondanza locale in un'area diversa. La copia si troverà quindi in un'area remota. È anche possibile copiare lo snapshot in un account di archiviazione con ridondanza geografica e accesso in lettura nella stessa area. In questo caso lo snapshot viene replicato in modo differito nell'area secondaria remota. Il backup viene protetto da emergenze nel sito primario dopo il completamento delle operazioni di copia e di replica.

Per copiare in modo efficiente gli snapshot incrementali per il ripristino di emergenza, vedere le istruzioni disponibili in [Eseguire il backup dei dischi di VM non gestiti con snapshot incrementali](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/incremental-snapshots).

![Eseguire il backup dei dischi di VM non gestiti con snapshot incrementali][2]

### <a name="recovery-from-snapshots"></a>Ripristino da snapshot

Per recuperare uno snapshot, copiarlo per creare un nuovo BLOB. Se si copia lo snapshot dall'account primario, è possibile copiare lo snapshot nel BLOB di base dello snapshot. Questo processo consente di ripristinare il disco allo snapshot. Questa operazione è nota come innalzamento di livello dello snapshot. Se si copia il backup dello snapshot da un account secondario, nel caso di un account di archiviazione con ridondanza geografica e accesso in lettura, è necessario copiarlo in un account primario. È possibile copiare uno snapshot [usando PowerShell](https://docs.microsoft.com/en-us/azure/storage/common/storage-powershell-guide-full#how-to-copy-a-snapshot-of-a-blob) oppure con l'utilità AzCopy. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy).

Per le VM con più dischi, è necessario copiare tutti gli snapshot che fanno parte dello stesso punto di ripristino coordinato. Dopo la copia degli snapshot in BLOB di dischi rigidi virtuali scrivibili, è possibile usare i BLOB per ricreare la VM usando il modello per la macchina virtuale.

## <a name="other-options"></a>Altre opzioni

### <a name="sql-server"></a>SQL Server

SQL Server in esecuzione in una VM include funzionalità predefinite per il backup del database di SQL Server nell'archivio BLOB di Azure o in una condivisione file. Se l'account di archiviazione è di tipo archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura, è possibile accedere ai backup nel data center secondario dell'account di archiviazione in caso di emergenza, con le stesse restrizioni illustrate in precedenza. Per altre informazioni, vedere [Backup e ripristino per SQL Server in macchine virtuali di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). Oltre al backup e al ripristino, i [gruppi di disponibilità AlwaysOn di SQL Server](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr) possono mantenere repliche secondarie dei database. Ciò riduce notevolmente il tempo necessario per il ripristino di emergenza.

## <a name="other-considerations"></a>Altre considerazioni

Questo articolo ha illustrato come eseguire il backup o creare snapshot delle VM e dei rispettivi dischi per supportare il ripristino di emergenza e come usarli per ripristinare i dati. Con il modello di Azure Resource Manager, molti utenti usano i modelli per creare le VM e altri tipi di infrastrutture in Azure. È possibile usare un modello per creare una VM che ha sempre la stessa configurazione. Se si usano immagini personalizzate per la creazione delle VM, è necessario anche assicurarsi che le immagini siano protette tramite l'archiviazione in un account di archiviazione con ridondanza geografica e accesso in lettura.

È quindi possibile che il processo di backup sia una combinazione di due elementi:

- Backup dei dati (dischi).
- Backup della configurazione (modelli e immagini personalizzate).

In base all'opzione di backup scelta, potrebbe essere necessario gestire il backup dei dati e della configurazione oppure consentire al servizio di backup di gestire automaticamente tali operazioni.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Appendice: informazioni sull'impatto della ridondanza dei dati

Per gli account di archiviazione di Azure sono disponibili tre tipi di ridondanza dei dati da tenere in considerazione per il ripristino di emergenza, ovvero archiviazione con ridondanza locale, archiviazione con ridondanza geografica e archiviazione con ridondanza geografica e accesso in lettura. 

L'archiviazione con ridondanza locale mantiene tre copie dei dati nello stesso data center. Quando la macchina virtuale scrive i dati, tutte e tre le copie vengono aggiornate prima della restituzione dell'esito positivo al chiamante, quindi si è sicuri che siano identici. Il disco è protetto da errori locali, perché è estremamente improbabile che tutte e tre le copie siano interessate contemporaneamente da un problema. Nel caso dell'archiviazione con ridondanza locale non è disponibile la ridondanza geografica, quindi il disco non è protetto da errori irreversibili che possono interessare un intero data center o un'unità di archiviazione.

Con l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura, vengono mantenute tre copie dei dati nell'area primaria, selezionata dall'utente, e altre tre copie dei dati vengono mantenute in un'area secondaria corrispondente, impostata da Azure. Se ad esempio si archiviano dati nell'area Stati Uniti occidentali, i dati vengono replicati nell'area Stati Uniti orientali. La conservazione delle copie viene eseguita in modo asincrono e si verifica un leggero ritardo tra gli aggiornamenti ai siti primari e secondari. Le repliche dei dischi nel sito secondario sono coerenti in base ai singoli dischi (con ritardo), ma le repliche di più dischi attivi potrebbero non essere sincronizzate tra loro. Per ottenere repliche coerenti tra più dischi, sono necessari snapshot coerenti.

La differenza principale tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura consiste nel fatto che l'archiviazione con ridondanza geografica e accesso in lettura consente di leggere la copia secondaria in qualsiasi momento. Se si verifica un problema che rende inaccessibili i dati nell'area primaria, il team di Azure si impegna per ripristinare l'accesso. Quando l'area primaria è inattiva, se è abilitata l'archiviazione con ridondanza geografica e accesso in lettura, è possibile accedere ai dati nel data center secondario. Se quindi si prevede la lettura dalla replica quando l'area primaria è inaccessibile, è consigliabile prendere in considerazione l'archiviazione con ridondanza geografica e accesso in lettura.

Se si verifica un'interruzione significativa, è possibile che il team di Azure attivi un failover geografico e modifichi le voci del DNS primario in modo che facciano riferimento alle risorse di archiviazione secondarie. A questo punto, se è abilitata l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura, è possibile accedere ai dati nell'area che era precedentemente secondaria. In altri termini, se l'account di archiviazione è di tipo archiviazione con ridondanza geografica e si verifica un problema, è possibile accedere alle risorse di archiviazione secondarie solo se è disponibile il failover geografico.

Per altre informazioni, vedere [Cosa fare se si verifica un'interruzione di Archiviazione di Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance). 

>[!NOTE] 
>Microsoft controlla se si verifica un failover. Il failover non è controllato per i singoli account di archiviazione e non viene quindi determinato dai singoli clienti. Per implementare il ripristino di emergenza per account di archiviazione o dischi di macchine virtuali specifici, è necessario usare le tecniche illustrate in precedenza in questo articolo.



[1]: ./media/storage-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/storage-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
