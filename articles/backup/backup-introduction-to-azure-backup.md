---
title: Informazioni su Backup di Azure | Microsoft Docs
description: "Con Azure Backup e Servizi di ripristino è possibile eseguire il backup e il ripristino di dati e applicazioni da server Windows, computer client Windows, server System Center DPM e macchine virtuali di Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keywords: backup e ripristino; servizi di ripristino; soluzioni di backup
ms.assetid: 0d2a7f08-8ade-443a-93af-440cbf7c36c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/6/2016
ms.author: jimpark; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: b9737c3da308aecf25d5f18088f96c319edeafd5
ms.openlocfilehash: 76ec51a75240710b24c0e91042d6229e60eeada9


---
# <a name="what-is-azure-backup"></a>Informazioni su Backup di Azure
Backup di Azure è il servizio basato su Azure che consente di eseguire il backup, la protezione e il ripristino dei dati in Microsoft Cloud. Backup di Azure sostituisce la soluzione di backup locale o esterna esistente con una soluzione basata sul cloud affidabile, sicura e conveniente. Backup di Azure offre più componenti che vengono scaricati e distribuiti nel computer o server appropriato o nel cloud. Il componente o l'agente distribuito dipende da ciò che si intende proteggere. Tutti i componenti di Backup di Azure consentono di eseguire il backup dei dati in un insieme di credenziali di Backup in Azure, a prescindere che i dati da proteggere si trovino in locale o nel cloud. Vedere la [tabella dei componenti di Backup di Azure](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use) più avanti in questo articolo per informazioni sul componente da usare per proteggere dati, applicazioni o carichi di lavoro specifici.

[Panoramica video di Backup di Azure](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## <a name="why-use-azure-backup"></a>Perché usare Backup di Azure
Le soluzioni di backup tradizionali si sono evolute e considerano ora il cloud come un endpoint o una destinazione di archiviazione statica, in modo simile a un disco o un nastro. Questo approccio, anche se semplice, è limitato e non permette di sfruttare appieno la piattaforma cloud sottostante. Ne consegue una soluzione costosa e inefficiente. Altre soluzioni sono costose perché può accadere di pagare il tipo di archiviazione errato oppure risorse di archiviazione superflue. Altre soluzioni sono spesso inefficienti perché non offrono il tipo o lo spazio di archiviazione necessario oppure le attività amministrative richiedono troppo tempo. Backup di Azure offre invece i vantaggi principali seguenti:

**Gestione automatica dell'archiviazione**. Gli ambienti ibridi richiedono spesso un'archiviazione eterogenea, in parte in locale e in parte nel cloud. Con Backup di Azure non sono previsti costi per l'uso di dispositivi di archiviazione locale. Backup di Azure alloca e gestisce automaticamente le risorse di archiviazione di backup e usa un modello di pagamento in base al consumo. Con il pagamento in base al consumo si pagano solo le risorse di archiviazione effettivamente usate. Per altre informazioni, vedere la pagina relativa ai [prezzi di Azure](https://azure.microsoft.com/pricing/details/backup).

**Scalabilità illimitata**. Backup di Azure usa le potenzialità e la scalabilità illimitata del cloud di Azure per offrire disponibilità elevata, senza costi generali di manutenzione o monitoraggio. È possibile impostare avvisi per fornire informazioni sugli eventi, ma non è necessario preoccuparsi della disponibilità elevata dei dati nel cloud.

**Più opzioni di archiviazione**. Un aspetto della disponibilità elevata è la replica di archiviazione. Backup di Azure offre due tipi di replica: [archiviazione con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage) e [archiviazione con replica geografica](../storage/storage-redundancy.md#geo-redundant-storage). Scegliere l'opzione di archiviazione di backup in base alle esigenze:

* L'archiviazione con ridondanza locale replica i dati tre volte (crea tre copie dei dati) in un data center associato nella stessa area. L'archiviazione con ridondanza locale è un'opzione a basso costo ideale per i clienti attenti ai prezzi perché protegge i dati da errori hardware locali.
* L'archiviazione con ridondanza geografica replica i dati in un'area secondaria a centinaia di chilometri di distanza dalla posizione primaria dei dati di origine. L'archiviazione con ridondanza geografica è più costosa dell'archiviazione con ridondanza locale, ma offre un livello più elevato di durabilità per i dati, anche in presenza di un'interruzione di area.

**Trasferimento dati illimitato**. Backup di Azure non limita la quantità di dati trasferiti in ingresso o in uscita. Backup di Azure non addebita costi per i dati trasferiti. Se tuttavia si usa il servizio Importazione/esportazione di Azure per importare grandi quantità di dati, viene applicato un costo per i dati in ingresso. Per altre informazioni su questi costi, vedere [Flusso di lavoro del backup offline in Backup di Azure](backup-azure-backup-import-export.md). I dati in uscita sono i dati trasferiti da un insieme di credenziali di Backup durante un'operazione di ripristino.

**Crittografia dei dati**. La crittografia dei dati consente la trasmissione e l'archiviazione sicure dei dati nel cloud pubblico. La passphrase di crittografia viene archiviata in locale e non viene mai trasmessa o archiviata in Azure. Se è necessario ripristinare i dati, solo il cliente è in possesso della passphrase o della chiave di crittografia.

**Backup coerente con l'applicazione**. A prescindere che venga eseguito il backup di un file server, di una macchina virtuale o di un database SQL, è necessario sapere che un punto di ripristino ha tutti i dati necessari per ripristinare la copia di backup. Backup di Azure offre backup coerenti con l'applicazione, che eliminano la necessità di correzioni aggiuntive per ripristinare i dati. Il ripristino di dati coerenti con l'applicazione riduce il tempo di ripristino e consente quindi di tornare rapidamente allo stato operativo.

**Conservazione a lungo termine**. È possibile eseguire il backup dei dati in Azure per 99 anni. Invece di passare le copie di backup dal disco al nastro e quindi di trasferire il nastro in una posizione esterna per l'archiviazione a lungo termine, è possibile usare Azure per la conservazione a breve e lungo termine.

## <a name="which-azure-backup-components-should-i-use"></a>Quali componenti di Backup di Azure è opportuno usare?
Se non si è certi del componente di Backup di Azure più adatto alle proprie esigenze, vedere la tabella seguente per informazioni su ciò che è possibile proteggere con ogni componente. Il portale di Azure offre una procedura guidata per la scelta del componente da scaricare e distribuire. La procedura guidata, che fa parte della creazione dell'insieme di credenziali di Servizi di ripristino, consente di selezionare un obiettivo di backup e di scegliere i dati o l'applicazione da proteggere.

| Componente | Vantaggi | Limiti | Quali elementi vengono protetti? | Dove vengono archiviati i backup? |
| --- | --- | --- | --- | --- |
| Agente di Backup di Azure (MARS) |<li>Backup di file e cartelle nel sistema operativo Windows fisico o virtuale. Le VM possono trovarsi in locale o in Azure.<li>Non è necessario un server di backup separato. |<li>Backup 3 volte al giorno <li>Senza riconoscimento dell'applicazione; ripristino solo a livello di file, cartelle e volumi, <li>  Nessun supporto per Linux. |<li>File, <li>Cartelle |Insieme di credenziali di Backup di Azure |
| System Center DPM |<li>Snapshot con riconoscimento dell'app (servizio Copia Shadow del volume)<li>Flessibilità completa in merito a quando eseguire i backup<li>Granularità ripristino (tutto)<li>Può usare l'insieme di credenziali di Backup di Azure<li>Supporto di Linux in macchine virtuali Hyper-V e VMware <li>Protezione di VM VMware con DPM 2012 R2 |Non è possibile eseguire il backup del carico di lavoro di Oracle.|<li>File, <li>Cartelle,<li> Volumi, <li>Macchine virtuali,<li> Applicazioni,<li> Carichi di lavoro |<li>Insieme di credenziali di Backup di Azure,<li> Disco collegato al computer locale,<li>  Nastro (solo in locale) |
| Server di backup di Azure |<li>Snapshot con riconoscimento dell'app (servizio Copia Shadow del volume)<li>Flessibilità completa in merito a quando eseguire i backup<li>Granularità ripristino (tutto)<li>Può usare l'insieme di credenziali di Backup di Azure<li>Supporto per Linux (se ospitato in Hyper-V)<li>Protezione di VM VMware con DPM 2012 R2<li>Non richiede una licenza per System Center |<li>Non è possibile eseguire il backup del carico di lavoro di Oracle.<li>Richiede sempre una sottoscrizione di Azure attiva<li>Nessun supporto per il backup su nastro |<li>File, <li>Cartelle,<li> Volumi, <li>Macchine virtuali,<li> Applicazioni,<li> Carichi di lavoro |<li>Insieme di credenziali di Backup di Azure,<li> Disco collegato al computer locale |
| Backup di VM IaaS di Azure |<li>Backup nativi per Windows/Linux<li>Non è richiesta l'installazione un agente specifico<li>Backup a livello di infrastruttura senza che sia necessaria un'infrastruttura di backup |<li>Backup delle VM una volta al giorno <li>Ripristino delle VM solo a livello di disco<li>Non può eseguire il backup in locale |<li>Macchine virtuali, <li>Tutti i dischi (tramite PowerShell) |<p>Insieme di credenziali di Backup di Azure</p> |

## <a name="what-are-the-deployment-scenarios-for-each-component"></a>Quali sono gli scenari di distribuzione di ogni componente?
| Componente | Può essere distribuito in Azure? | Può essere distribuito in locale? | Archiviazione di destinazione supportata |
| --- | --- | --- | --- |
| Agente di Backup di Azure (MARS) |<p>**Sì**</p> <p>L'agente di Backup di Azure può essere distribuito in qualsiasi VM Windows Server eseguita in Azure.</p> |<p>**Sì**</p> <p>L'agente di backup può essere distribuito in qualsiasi computer fisico o VM Windows Server.</p> |<p>Insieme di credenziali di Backup di Azure</p> |
| System Center DPM |<p>**Sì**</p><p>Altre informazioni su [come proteggere i carichi di lavoro in Azure con System Center DPM](backup-azure-dpm-introduction.md).</p> |<p>**Sì**</p> <p>Altre informazioni su [come proteggere i carichi di lavoro e le VM nel data center](https://technet.microsoft.com/en-us/system-center-docs/dpm/data-protection-manager).</p> |<p>Disco collegato al computer locale,</p> <p>Insieme di credenziali di Backup di Azure,</p> <p>nastro (solo in locale)</p> |
| Server di backup di Azure |<p>**Sì**</p><p>Altre informazioni su [come proteggere i carichi di lavoro in Azure con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md).</p> |<p>**Sì**</p> <p>Altre informazioni su [come proteggere i carichi di lavoro in Azure con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md).</p> |<p>Disco collegato al computer locale,</p> <p>Insieme di credenziali di Backup di Azure</p> |
| Backup di VM IaaS di Azure |<p>**Sì**</p><p>Parte dell'infrastruttura di Azure</p><p>Specializzato per il [backup di macchine virtuali di infrastruttura distribuita come servizio (IaaS) di Azure](backup-azure-vms-introduction.md).</p> |<p>**No**</p> <p>Usare System Center DPM per il backup delle macchine virtuali nel data center.</p> |<p>Insieme di credenziali di Backup di Azure</p> |

## <a name="which-applications-and-workloads-can-be-backed-up"></a>Applicazioni e carichi di lavoro di cui è possibile eseguire il backup
La tabella seguente descrive una matrice dei dati e dei carichi di lavoro che possono essere protetti con Backup di Azure. La colonna della soluzione di Backup di Azure include collegamenti alla documentazione della distribuzione per tale soluzione. Ogni componente di Backup di Azure può essere distribuito con un modello di distribuzione classica (distribuzione di Service Manager) o di Resource Manager.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

| Dati o carico di lavoro | Ambiente di origine | Soluzione di Backup di Azure |
| --- | --- | --- |
| File e cartelle |Windows Server |<p>[Agente di Backup di Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ agente di Backup di Azure),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md) (include l'agente di Backup di Azure)</p> |
| File e cartelle |Computer Windows |<p>[Agente di Backup di Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ agente di Backup di Azure),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md) (include l'agente di Backup di Azure)</p> |
| Macchina virtuale Hyper-V (Windows) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ agente di Backup di Azure),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md) (include l'agente di Backup di Azure)</p> |
| Macchina virtuale Hyper-V (Linux) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ agente di Backup di Azure),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md) (include l'agente di Backup di Azure)</p> |
| Microsoft SQL Server |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ agente di Backup di Azure),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md) (include l'agente di Backup di Azure)</p> |
| Microsoft SharePoint |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ agente di Backup di Azure),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md) (include l'agente di Backup di Azure)</p> |
| Microsoft Exchange |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ agente di Backup di Azure),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md) (include l'agente di Backup di Azure)</p> |
| VM IaaS di Azure (Windows) |in esecuzione in Azure |[Backup di Azure (estensione VM)](backup-azure-vms-introduction.md) |
| VM IaaS di Azure (Linux) |in esecuzione in Azure |[Backup di Azure (estensione VM)](backup-azure-vms-introduction.md) |

## <a name="linux-support"></a>Supporto Linux
La tabella seguente illustra i componenti di Backup di Azure che hanno il supporto per Linux.  

| Componente | Supporto Linux (approvato per Azure) |
| --- | --- |
| Agente di Backup di Azure (MARS) |No (solo agente basato su Windows) |
| System Center DPM |Backup coerente a livello di file solo in Hyper-V<br/> (non disponibile per le VM di Azure) |
| Server di backup di Azure |Backup coerente a livello di file solo in Hyper-V<br/> (non disponibile per le VM di Azure) |
| Backup di VM IaaS di Azure |Sì |

## <a name="using-premium-storage-vms-with-azure-backup"></a>Uso di macchine virtuali di Archiviazione Premium con Backup di Azure
Backup di Azure protegge le macchine virtuali di Archiviazione Premium. Archiviazione Premium di Azure è una soluzione di archiviazione basata su unità SSD progettata per supportare carichi di lavoro con attività di I/O intensive. Archiviazione Premium è una soluzione interessante per i carichi di lavoro delle macchine virtuali. Per altre informazioni su Archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md)

### <a name="back-up-premium-storage-vms"></a>Backup di macchine virtuali di Archiviazione Premium
Durante il backup di macchine virtuali di Archiviazione Premium, il servizio Backup crea un percorso di gestione temporanea nell'account di archiviazione Premium. Il percorso di gestione temporanea, denominato "AzureBackup-", è uguale alla dimensione totale dei dati dei dischi Premium collegati alla macchina virtuale. Controllare se è presente spazio libero sufficiente per un percorso temporaneo di gestione temporanea nell'account di archiviazione. Per altre informazioni, vedere l'articolo [premium storage limitations](../storage/storage-premium-storage.md#premium-storage-scalability-and-performance-targets) (Limiti di Archiviazione Premium).

> [!NOTE]
> Non modificare in alcun modo il percorso di gestione temporanea.
>
>

Al termine del processo di backup, il percorso di gestione temporanea viene eliminato. Il prezzo della risorsa di archiviazione usata per il percorso di gestione temporanea è in linea con gli altri [prezzi di Archiviazione Premium](../storage/storage-premium-storage.md#pricing-and-billing).

### <a name="restore-premium-storage-vms"></a>Ripristino di macchine virtuali di Archiviazione Premium
Le VM di Archiviazione Premium possono essere ripristinate in Archiviazione Premium o in una normale risorsa di archiviazione. Il processo tipo consiste nel ripristino di un punto di ripristino della macchina virtuale di Archiviazione Premium in Archiviazione Premium. Tuttavia, può risultare più economico ripristinare un punto di ripristino della macchina virtuale di Archiviazione Premium in Archiviazione Standard. Si può usare questo tipo di ripristino se è necessario un subset di file dalla macchina virtuale.

## <a name="what-are-the-features-of-each-backup-component"></a>Quali sono le funzionalità di ogni componente di Backup?
Le sezioni seguenti indicano tabelle che riepilogano la disponibilità o il supporto di diverse funzionalità in ogni componente di Backup di Azure. Vedere le informazioni riportate dopo ogni tabella per altri dettagli.

### <a name="storage"></a>Archiviazione
| Funzionalità | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di VM IaaS di Azure |
| --- | --- | --- | --- | --- |
| Insieme di credenziali di Backup di Azure |![sì][green] |![Sì][green] |![Sì][green] |![sì][green] |
| Archiviazione su disco | |![sì][green] |![sì][green] | |
| Archiviazione su nastro | |![Sì][green] | | |
| Compressione <br/>(nell'insieme di credenziali di Backup) |![Sì][green] |![Sì][green] |![sì][green] | |
| Backup incrementale |![sì][green] |![Sì][green] |![Sì][green] |![sì][green] |
| Deduplicazione dei dischi | |![Parzialmente][yellow] |![Parzialmente][yellow] | |

![chiave tabella](./media/backup-introduction-to-azure-backup/table-key.png)

L'insieme di credenziali di Backup è la destinazione di archiviazione preferita in tutti i componenti. System Center DPM e il server di Backup di Azure offrono anche la possibilità di avere una copia del disco locale. Solo System Center DPM consente tuttavia di scrivere i dati su un dispositivo di archiviazione su nastro.

#### <a name="compression"></a>Compressione
I backup vengono compressi per ridurre lo spazio di archiviazione necessario. L'estensione della macchina virtuale è il solo componente che non usa alcuna compressione. L'estensione macchina virtuale copia tutti i dati di backup dall'account di archiviazione all'insieme di credenziali di Backup nella stessa area. Durante il trasferimento dei dati non viene usata alcuna compressione. Il trasferimento dei dati senza compressione comporta un leggero aumento dello spazio di archiviazione usato. L'archiviazione dei dati senza compressione, tuttavia, consente un ripristino più rapido nel caso in cui sia necessario tale punto di ripristino.

#### <a name="incremental-backup"></a>Backup incrementale
Ogni componente supporta il backup incrementale indipendentemente dall'archivio di destinazione, che sia un disco, un nastro o un insieme di credenziali di backup. Il backup incrementale garantisce l'archiviazione e la rapidità dei backup, trasferendo solo le modifiche apportate dall'ultimo backup.

#### <a name="disk-deduplication"></a>Deduplicazione dei dischi
È possibile usare la deduplicazione durante la distribuzione di System Center DPM o del server di Backup di Azure [in una macchina virtuale Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). Windows Server esegue la deduplicazione dei dati a livello di host nei dischi rigidi virtuali (VHD) collegati alla macchina virtuale come archivio di backup.

> [!NOTE]
> La deduplicazione non è disponibile in Azure per i componenti di Backup. Quando System Center DPM e il server di Backup vengono distribuiti in Azure, i dischi di archiviazione collegati alla macchina virtuale non possono essere deduplicati.
>
>

### <a name="security"></a>Sicurezza
| Funzionalità | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di VM IaaS di Azure |
| --- | --- | --- | --- | --- |
| Sicurezza di rete<br/> (in Azure) |![Sì][green] |![Sì][green] |![sì][green] |![Parzialmente][yellow] |
| Sicurezza dei dati<br/> (in Azure) |![Sì][green] |![Sì][green] |![sì][green] |![Parzialmente][yellow] |

![chiave tabella](./media/backup-introduction-to-azure-backup/table-key.png)

#### <a name="network-security"></a>Sicurezza di rete
Tutto il traffico di backup dai server all'insieme di credenziali di Backup viene crittografato con Advanced Encryption Standard 256. I dati di backup vengono inviati tramite un collegamento HTTPS sicuro. I dati di backup vengono archiviati anche nell'insieme di credenziali di Backup in formato crittografato. Solo il cliente di Azure ha la passphrase per sbloccare i dati. Microsoft non può decrittografare i dati di backup

> [!WARNING]
> Dopo aver stabilito l'insieme di credenziali di Backup, solo il cliente avrà accesso alla chiave di crittografia. Microsoft non conserva mai una copia della chiave di crittografia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.
>
>

#### <a name="data-security"></a>Sicurezza dei dati
Per il backup delle VM di Azure, è necessario configurare la crittografia *nella* macchina virtuale. Usare BitLocker nelle macchine virtuali Windows e **DM-Crypt** nelle macchine virtuali Linux. Backup di Azure non esegue automaticamente la crittografia dei dati di backup provenienti da questo percorso.

### <a name="network"></a>Rete
| Funzionalità | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di VM IaaS di Azure |
| --- | --- | --- | --- | --- |
| Compressione di rete <br/>(nel **server di backup**) | |![Sì][green] |![Sì][green] | |
| Compressione di rete <br/>(nell'**insieme di credenziali di Backup**) |![Sì][green] |![Sì][green] |![Sì][green] | |
| Protocollo di rete <br/>(nel **server di backup**) | |TCP |TCP | |
| Protocollo di rete <br/>(nell'**insieme di credenziali di Backup**) |HTTPS |HTTPS |HTTPS |HTTPS |

![chiave tabella](./media/backup-introduction-to-azure-backup/table-key-2.png)

L'estensione della macchina nella VM IaaS legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione, quindi non è necessario comprimere il traffico.

Se si esegue il backup dei dati in System Center DPM o in un server di Backup di Azure, comprimere i dati in transito dal server primario al server di backup. La compressione dei dati prima del relativo backup in DPM o nel server di Backup di Azure consente di risparmiare larghezza di banda.

#### <a name="network-throttling"></a>Limitazione della larghezza di banda della rete
L'agente di Backup di Azure consente di limitare la larghezza di banda per controllare la modalità d'uso della larghezza di banda della rete durante il trasferimento dei dati. La limitazione può essere utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma senza che il processo di backup interferisca con il resto del traffico Internet. La limitazione per il trasferimento dati si applica alle attività di backup e ripristino.

### <a name="backup-and-retention"></a>Backup e conservazione

Backup di Azure ha un limite di 9999 punti di ripristino, noti anche come copie di backup o snapshot, per ogni insieme di credenziali di backup. La tabella seguente illustra la frequenza massima di backup (nell'insieme di credenziali) per ogni componente. La configurazione dei criteri di backup determina la rapidità con cui si utilizzano i punti di ripristino. Ad esempio, se si crea un punto di ripristino ogni giorno, è possibile mantenere i punti di ripristino per 27 anni prima di eseguirli. Se si gestisce un punto di ripristino mensile, è possibile mantenere i punti di ripristino per 833 anni prima di eseguirli. Il servizio Backup non imposta un limite di tempo di scadenza su un punto di ripristino.

|  | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di VM IaaS di Azure |
| --- | --- | --- | --- | --- |
| Frequenza di backup<br/> (nell'insieme di credenziali di Backup) |3 backup al giorno |2 backup al giorno |2 backup al giorno |1 backup al giorno |
| Frequenza di backup<br/> (nel disco) |Non applicabile |<li>Ogni 15 minuti per SQL Server <li>Ogni ora per altri carichi di lavoro |<li>Ogni 15 minuti per SQL Server <li>Ogni ora per altri carichi di lavoro</p> |Non applicabile |
| Opzioni di conservazione |Giornaliera, settimanale, mensile, annuale |Giornaliera, settimanale, mensile, annuale |Giornaliera, settimanale, mensile, annuale |Giornaliera, settimanale, mensile, annuale |
| Punti di ripristino massimo per ogni server |9999|9999|9999|9999|
| Periodo massimo di conservazione |Dipende dalla frequenza dei backup |Dipende dalla frequenza dei backup |Dipende dalla frequenza dei backup |Dipende dalla frequenza dei backup |
| Punti di ripristino nel disco locale |Non applicabile |<li>64 per i file server<li>448 per i server applicazioni |<li>64 per i file server<li>448 per i server applicazioni |Non applicabile |
| Punti di ripristino su nastro |Non applicabile |Senza limiti |Non applicabile |Non applicabile |

## <a name="what-is-the-vault-credential-file"></a>Informazioni sul file delle credenziali di insieme
Il file delle credenziali dell'insieme di credenziali è un certificato generato dal portale per ogni insieme di credenziali di backup. Il portale carica quindi la chiave pubblica nel Servizio di controllo di accesso (o ACS). La chiave privata viene indicata all'utente quando si scaricano le credenziali. Usarla per registrare i computer da proteggere. La chiave privata consente di autenticare i server o i computer per l'invio di dati di backup a uno specifico insieme di credenziali di Backup.

Le credenziali dell'insieme di credenziali vengono usate solo per registrare i server o i computer. Prestare tuttavia attenzione alle credenziali dell'insieme di credenziali perché se smarrite o acquisite da altri possono essere usate per registrare altri computer nello stesso insieme di credenziali. Poiché i dati di backup sono crittografati con una passphrase accessibile solo al cliente, i dati di backup esistenti non possono essere compromessi. Le credenziali dell'insieme di credenziali scadono dopo 48 ore. Anche se è possibile scaricare le credenziali dell'insieme di credenziali di Backup per un numero illimitato di volte, per la registrazione possono essere usate solo le credenziali più recenti.

## <a name="how-does-azure-backup-differ-from-azure-site-recovery"></a>Differenze tra Backup di Azure e Azure Site Recovery
Backup di Azure e Azure Site Recovery sono correlate in quanto entrambi i servizi eseguono il backup e il ripristino dei dati, ma la proposta di valore alla base è diversa.

Backup di Azure protegge i dati in locale e nel cloud. Azure Site Recovery coordina la replica, il failover e il failback di macchine virtuali e server fisici. Entrambi i servizi sono importanti perché la soluzione di ripristino di emergenza deve fare in modo che i dati siano protetti e ripristinabili (Backup) *e* mantenere i carichi di lavoro disponibili (Site Recovery) quando si verificano interruzioni.

I concetti seguenti consentono di prendere importanti decisioni relative al backup e al ripristino di emergenza.

| Concetto | Dettagli | Backup | Ripristino di emergenza |
| --- | --- | --- | --- |
| Obiettivo del punto di ripristino (RPO) |Quantità di perdita di dati accettabile se si rende necessario un ripristino. |Le soluzioni di backup presentano un'ampia variabilità a livello di RPO accettabile. I backup delle macchine virtuali hanno in genere un RPO di un giorno, mentre i backup dei database hanno RPO fino a un minimo di 15 minuti. |Le soluzioni di ripristino di emergenza hanno RPO bassi. La copia di ripristino di emergenza può risalire a pochi minuti o pochi secondi prima. |
| Obiettivo del tempo di ripristino (RTO) |Quantità di tempo necessario per completare un ripristino o un recupero. |Dato l'RPO più alto, la quantità di dati che deve essere elaborata dalla soluzione di backup è in genere molto più grande e questo comporta un RTO maggiore. Ad esempio, il ripristino dei dati dai nastri può richiedere giorni, a seconda del tempo richiesto per il trasporto del nastro da una posizione esterna. |Le soluzioni di ripristino di emergenza hanno RTO più bassi, perché il livello di sincronizzazione con l'origine è maggiore e il numero di modifiche da elaborare è ridotto. |
| Conservazione |Periodo di tempo per il quale i dati devono essere archiviati. |Per scenari che richiedono il ripristino operativo, per danneggiamento dei dati, eliminazione accidentale dei file o errori del sistema operativo, i dati di backup vengono in genere conservati per un massimo di 30 giorni.<br>Ai fini della conformità potrebbe essere necessario archiviare i dati per mesi o addirittura anni. In questi casi, i dati di backup sono particolarmente adatti per l'archiviazione. |Il ripristino di emergenza richiede soltanto dati di ripristino operativo. I tempi sono in genere di poche ore o di un giorno al massimo. A causa degli intervalli di acquisizione dei dati così ridotti nelle soluzioni di ripristino di emergenza, l'uso di questi dati per la conservazione a lungo termine non è consigliabile. |

## <a name="next-steps"></a>Passaggi successivi
Usare una delle esercitazioni seguenti per ottenere istruzioni dettagliate per la protezione dei dati in Windows Server o di una macchina virtuale in Azure:

* [Eseguire il backup di file e cartelle](backup-try-azure-backup-in-10-mins.md)
* [Eseguire il backup di macchine virtuali di Azure](backup-azure-vms-first-look-arm.md)

Per informazioni dettagliate sulla protezione di altri carichi di lavoro, vedere uno di questi articoli:

* [Eseguire il backup di Windows Server](backup-configure-vault.md)
* [Eseguire il backup di carichi di lavoro delle applicazioni](backup-azure-microsoft-azure-backup.md)
* [Eseguire il backup di VM IaaS di Azure](backup-azure-vms-prepare.md)

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png



<!--HONumber=Dec16_HO2-->


