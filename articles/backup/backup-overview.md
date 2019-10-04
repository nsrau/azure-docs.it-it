---
title: Informazioni su Backup di Azure
description: Panoramica del servizio Backup di Azure e del suo contributo nel contesto della strategia di continuità aziendale e ripristino di emergenza.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 526c60916854d4918607a1fd1b887ac9d27cd1c7
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950020"
---
# <a name="what-is-the-azure-backup-service"></a>Informazioni sul servizio Backup di Azure

Il servizio Backup di Azure consente di eseguire il backup dei dati nel cloud di Microsoft Azure. È possibile eseguire il backup di computer e carichi di lavoro locali, nonché di macchine virtuali di Azure (VM).


## <a name="why-use-azure-backup"></a>Perché usare Backup di Azure

Backup di Azure offre i vantaggi principali seguenti:

- **Offload del backup locale**: Backup di Azure offre una soluzione semplice per eseguire il backup delle risorse locali nel cloud. Consente di ottenere backup a breve e a lungo termine senza la necessità di distribuire complesse soluzioni locali.
- **Backup delle VM IaaS di Azure**: Backup di Azure fornisce backup indipendenti e isolati per salvaguardare dalla distruzione accidentale dei dati originali. I backup vengono archiviati in un insieme di credenziali di Servizi di ripristino con la gestione predefinita dei punti di ripristino. La configurazione e la scalabilità sono semplici, i backup sono ottimizzati ed è possibile eseguire il ripristino con facilità secondo necessità.
- **Scalabilità semplificata**: Backup di Azure sfrutta le potenzialità e la scalabilità illimitata del cloud di Azure per offrire disponibilità elevata, senza costi generali di manutenzione o monitoraggio.
- **Trasferimento dati senza limiti**: Backup di Azure non prevede limiti per la quantità di dati trasferiti in ingresso o in uscita né addebiti per il trasferimento dei dati.
    - I dati in uscita sono i dati trasferiti da un insieme di credenziali di Servizi di ripristino durante un'operazione di ripristino.
    - Se si esegue un backup iniziale offline con il servizio Importazione/esportazione di Azure per importare grandi quantità di dati, viene applicato un costo per i dati in ingresso.  [Altre informazioni](backup-azure-backup-import-export.md)
- **Sicurezza dei dati**: Backup di Azure offre soluzioni per la protezione dei dati in transito e inattivi.
- **Backup coerenti con le app**: i backup coerenti con le applicazioni implicano che un punto di ripristino ha tutti i dati necessari per ripristinare la copia di backup. Backup di Azure offre backup coerenti con l'applicazione, che eliminano la necessità di correzioni aggiuntive per ripristinare i dati. Il ripristino di dati coerenti con l'applicazione riduce il tempo di ripristino e consente quindi di tornare rapidamente allo stato operativo.
- **Conservazione a breve e a lungo termine**: è possibile usare gli insiemi di credenziali di Servizi di ripristino per la conservazione dei dati a breve termine e a lungo termine. Azure non limita la durata della conservazione dei dati in un insieme di credenziali di dei Servizi di ripristino. È possibile conservare i dati per il tempo desiderato. Backup di Azure ha un limite di 9999 punti di ripristino per ogni istanza protetta. 
- **Gestione automatica dell'archiviazione**. Gli ambienti ibridi richiedono spesso un'archiviazione eterogenea, in parte in locale e in parte nel cloud. Con Backup di Azure non sono previsti costi per l'uso di dispositivi di archiviazione locale. Backup di Azure alloca e gestisce automaticamente le risorse di archiviazione di backup e usa un modello di pagamento in base al consumo in modo che si pagano solo le risorse di archiviazione effettivamente usate. [Altre informazioni](https://azure.microsoft.com/pricing/details/backup) sui prezzi.
- **Più opzioni di archiviazione**: Backup di Azure offre due tipi di replica per garantire la disponibilità elevata delle risorse di archiviazione e/o dei dati.
    - L'[archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md) replica i dati tre volte (crea tre copie dei dati) in un'unità di scala di archiviazione in un data center. Tutte le copie dei dati si trovano nella stessa area geografica. L'archiviazione con ridondanza locale è un'opzione a costo contenuto per la protezione dei dati da errori hardware locali.
    - L'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md) è l'opzione di replica predefinita e consigliata. L'archiviazione con ridondanza geografica replica i dati in un'area secondaria a centinaia di chilometri di distanza dalla posizione primaria dei dati di origine. L'archiviazione con ridondanza geografica è più costosa dell'archiviazione con ridondanza locale, ma offre un livello più elevato di durabilità per i dati, anche in presenza di un'interruzione di area.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Qual è la differenza tra Backup di Azure e Azure Site Recovery?

I servizi Backup di Azure e Azure Site Recovery contribuiscono entrambi al perseguimento di una strategia di continuità aziendale e ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) nell'organizzazione. La strategia BCDR si propone due obiettivi di ampio respiro:

- Mantenere i dati aziendali al sicuro e recuperabili in caso di interruzioni del servizio.
- Mantenere la piena operatività di app e carichi di lavoro durante i tempi di inattività pianificati e non pianificati.

I due servizi offrono funzionalità complementari ma differenti.

- **Azure Site Recovery**: Site Recovery fornisce una soluzione di ripristino di emergenza per i computer locali e per le VM di Azure. I computer vengono replicati da una posizione primaria a una secondaria. In caso di emergenza, viene eseguito il failover dei computer nella seconda posizione, da cui risultano accessibili. Quando viene ripristinata la normale operatività, viene eseguito il failback dei computer nel sito primario per il ripristino.
- **Backup di Azure**: il servizio Backup di Azure esegue i backup dei dati da computer locali e da VM di Azure. È possibile eseguire il backup e il ripristino dei dati a livello granulare, includendo file, cartelle, stato del sistema e dati con riconoscimento delle app. Backup di Azure gestisce i dati a livello più granulare rispetto a Site Recovery. Se ad esempio una presentazione in un computer portatile risulta danneggiata, è possibile usare Backup di Azure per ripristinarla. Se si vogliono mantenere i dati e le configurazioni delle VM al sicuro e accessibili, è possibile usare Site Recovery.  

Consultare la tabella seguente per identificare le proprie esigenze ai fini della strategia BCDR.

**Obiettivo** | **Dettagli** | **Confronto**
--- | --- | ---
**Backup/conservazione dei dati** | I dati di backup possono essere conservati e archiviati per giorni, mesi o anche anni se necessario ai fini della conformità. | Le soluzioni come Backup di Azure consentono di selezionare con precisione i dati di cui eseguire il backup, nonché di ottimizzare i criteri di backup e conservazione.<br/><br/> Site Recovery non offre lo stesso livello di ottimizzazione.
**Obiettivo del punto di ripristino (RPO)** | Quantità di perdita di dati accettabile se si rende necessario un ripristino. | I backup hanno RPO più variabili.<br/><br/> I backup delle VM hanno in genere un RPO di un giorno, mentre i backup dei database hanno RPO fino a un minimo di 15 minuti.<br/><br/> Site Recovery fornisce un RPO basso, perché la replica è continua o frequente, quindi il delta tra l'origine e la copia di replica è piccolo.
**Obiettivo del tempo di ripristino (RTO)** |Quantità di tempo necessario per completare un ripristino o un recupero. | Dato l'RPO più alto, la quantità di dati che deve essere elaborata dalla soluzione di backup è in genere molto più grande e questo comporta un RTO maggiore. Ad esempio, il ripristino dei dati dai nastri può richiedere giorni, a seconda del tempo richiesto per il trasporto del nastro da una posizione esterna.

## <a name="what-backup-scenarios-are-supported"></a>Quali scenari di backup sono supportati?

Backup di Azure consente di eseguire il backup sia di computer locali che di VM di Azure.

**Computer** | **Scenario di backup**
--- | ---
**Backup locale** |  1) Eseguire l'agente di Servizi di ripristino di Microsoft Azure di Backup di Azure nei computer Windows locali per eseguire il backup di singoli file e dello stato del sistema. <br/><br/>2) Eseguire il backup dei computer locali in un server di backup, ad esempio System Center Data Protection Manager (DPM) o un server di Backup di Microsoft Azure, e quindi configurarlo per eseguire il backup in un insieme di credenziali di Servizi di ripristino di Backup di Azure in Azure.
**Macchine virtuali di Azure** | 1) Abilitare il backup delle singole macchine virtuali di Azure. Quando si abilita il backup, Backup di Azure installa un'estensione dell'agente di macchine virtuali di Azure in esecuzione nella macchina virtuale. L'agente esegue il backup dell'intera macchina virtuale.<br/><br/> 2) Eseguire l'agente di Servizi di ripristino di Microsoft Azure in una macchina virtuale di Azure. Ciò è utile se si vuole eseguire il backup di singoli file e cartelle nella macchina virtuale.<br/><br/> 


## <a name="why-use-a-backup-server"></a>Perché usare un server di backup?
La scelta di eseguire il backup di computer e app in risorse di archiviazione del server DPM o del server di Backup di Microsoft Azure e quindi di eseguire il backup di tali risorse in un insieme di credenziali offre i vantaggi seguenti:

- Con il backup in server DPM o server di Backup di Microsoft Azure si ottengono backup con riconoscimento delle app ottimizzati per app comuni come SQL Server, Exchange e SharePoint, oltre a backup di file, cartelle, volumi e dello stato del computer (bare metal, stato del sistema).
- Per i computer locali non è necessario installare l'agente di Servizi di ripristino di Microsoft Azure in ogni computer di cui eseguire il backup. Ogni computer esegue l'agente protezione del server DPM o del server di Backup di Microsoft Azure, mentre l'agente di Servizi di ripristino di Microsoft Azure viene eseguito solo nei server DPM o server di Backup di Microsoft Azure.
- Si ottiene una maggiore flessibilità e opzioni di pianificazione granulari per l'esecuzione dei backup.
- È possibile gestire i backup per più computer raccolti in gruppi di protezione in una singola console. Questa opzione è particolarmente utile se si vuole eseguire contemporaneamente il backup di app divise in livelli tra più computer.

Altre informazioni sul [funzionamento del backup](backup-architecture.md#architecture-back-up-to-dpmmabs) quando si usa un server di backup e i [requisiti di supporto](backup-support-matrix-mabs-dpm.md) per i server di backup.

## <a name="what-can-i-back-up"></a>Backup consentiti

**Computer** | **Metodo di backup** | **Eseguire il backup**
--- | --- | ---
**Macchine virtuali Windows locali** | Esecuzione dell'agente di Servizi di ripristino di Microsoft Azure | Backup di file, cartelle, stato del sistema.<br/><br/> I computer Linux non sono supportati.
**Computer locali** | backup in DPM/MABS | Backup di qualsiasi elemento protetto da [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) o [server di Backup di Microsoft Azure](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) come file, cartelle, condivisioni, volumi e dati specifici delle app.
**Macchine virtuali di Azure** | Esecuzione dell'estensione di backup dell'agente di macchine Virtuali di Azure | Backup dell'intera macchina virtuale
**Macchine virtuali di Azure** | Esecuzione dell'agente di Servizi di ripristino di Microsoft Azure | Backup di file, cartelle, stato del sistema.<br/><br/> I computer Linux non sono supportati.
**Macchine virtuali di Azure** | Backup in server DPM o server di Backup di Microsoft Azure in esecuzione in Azure | Backup di qualsiasi elemento protetto da [server di Backup di Microsoft Azure](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) o [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) come file, cartelle, condivisioni, volumi e dati specifici delle app.

## <a name="what-backup-agents-do-i-need"></a>Quali agenti di backup sono necessari?

**Scenario** | **Agent**
--- | ---
**Eseguire un backup delle VM di Azure** | Non è necessario alcun agente. L'estensione delle macchine virtuali di Azure per il backup viene installata nella macchina virtuale di Azure quando si esegue il primo backup della macchina virtuale di Azure.<br/><br/> Sono supportati Windows e Linux.
**Backup di computer Windows locali** | Scaricare, installare ed eseguire l'agente di Servizi di ripristino di Microsoft Azure direttamente nel computer.
**Backup delle macchine virtuali di Azure con l'agente di Servizi di ripristino di Microsoft Azure** | Scaricare, installare ed eseguire l'agente di Servizi di ripristino di Microsoft Azure direttamente nel computer. L'agente di Servizi di ripristino di Microsoft Azure può essere eseguito insieme all'estensione di backup.
**Backup di computer locali e macchine virtuali di Azure in DPM o server di Backup di Microsoft Azure** | L'agente di protezione di DPM o del server di Backup di Microsoft Azure viene eseguito nei computer da proteggere. L'agente di Servizi di ripristino di Microsoft Azure viene eseguito in server DPM o server di Backup di Microsoft Azure per eseguire il backup in Azure.

## <a name="which-backup-agent-should-i-use"></a>Quale agente di backup è necessario usare?

**Backup** | **Soluzione** | **Limitazione**
--- | --- | ---
**Si vuole eseguire il backup di un'intera macchina virtuale di Azure** | Abilitare il backup per la macchina virtuale. L'estensione di backup verrà configurata automaticamente nella VM di Azure Windows o Linux. | Viene eseguito il backup dell'intera macchina virtuale <br/><br/> Per le VM Windows, il backup è coerente con le app. Per Linux, il backup è coerente con i file. Se serve il riconoscimento delle app per le macchine virtuali Linux, è necessario configurarlo con script personalizzati.
**Si vuole eseguire il backup di file e cartelle specifiche nella macchina virtuale di Azure** | Distribuire l'agente di Servizi di ripristino di Microsoft Azure nella macchina virtuale.
**Si vuole eseguire direttamente il backup di computer Windows locali** | Installare l'agente MARS nel computer. | È possibile eseguire il backup di file, cartelle e stato del sistema in Azure. I backup non sono con riconoscimento delle app.
**Si vuole eseguire direttamente il backup di computer Linux locali** | È necessario distribuire DPM o MABS per eseguire il backup in Azure. | Il backup dell'host Linux non è supportato; è possibile eseguire il backup solo della macchina guest Linux ospitata in Hyper-V o VMWare.
**Si vuole eseguire il backup di app in esecuzione in locale** | Per i backup con riconoscimento delle app, i computer devono essere protetti da DPM o dal server di Backup di Microsoft Azure.
**Si vogliono impostazioni di backup e ripristino granulari e flessibile per le macchine virtuali di Azure** | Proteggere le macchine virtuali di Azure con DPM o il server di Backup di Microsoft Azure in esecuzione in Azure per una maggiore flessibilità per la pianificazione dei backup e per la massima flessibilità per la protezione e il ripristino di file, cartelle, volumi, app e stato del sistema.

## <a name="backup-and-retention"></a>Backup e conservazione

Backup di Azure ha un limite di 9999 punti di ripristino, noti anche come copie di backup o snapshot, per ogni *istanza protetta*.

- Un'istanza protetta è un computer, un server (fisico o virtuale) o un carico di lavoro configurato per eseguire il backup dei dati in Azure. Un'istanza è protetta dopo il salvataggio di una copia di backup dei dati.
- La protezione è data dalla copia di backup dei dati. Se i dati di origine sono andati persi o danneggiati, la copia di backup può ripristinare i dati di origine.

La tabella seguente illustra la frequenza massima di backup per ogni componente. La configurazione dei criteri di backup determina la rapidità con cui si utilizzano i punti di ripristino. Ad esempio, se si crea un punto di ripristino ogni giorno, è possibile mantenere i punti di ripristino per 27 anni prima di eseguirli. Se si gestisce un punto di ripristino mensile, è possibile mantenere i punti di ripristino per 833 anni prima di eseguirli. Il servizio Backup non imposta un limite di tempo di scadenza su un punto di ripristino.

|  | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di VM IaaS di Azure |
| --- | --- | --- | --- | --- |
| Frequenza di backup<br/> (nell'insieme di credenziali di Servizi di ripristino) |3 backup al giorno |2 backup al giorno |2 backup al giorno |1 backup al giorno |
| Frequenza di backup<br/> (nel disco) |Non applicabile |Ogni 15 minuti per SQL Server<br/><br/> Ogni ora per altri carichi di lavoro |Ogni 15 minuti per SQL Server<br/><br/> Ogni ora per altri carichi di lavoro |Non applicabile |
| Opzioni di conservazione |Giornaliera, settimanale, mensile, annuale |Giornaliera, settimanale, mensile, annuale |Giornaliera, settimanale, mensile, annuale |Giornaliera, settimanale, mensile, annuale |
| Punti di ripristino massimo per ogni istanza protetta |9999|9999|9999|9999|
| Periodo massimo di conservazione |Dipende dalla frequenza dei backup |Dipende dalla frequenza dei backup |Dipende dalla frequenza dei backup |Dipende dalla frequenza dei backup |
| Punti di ripristino nel disco locale |Non applicabile | 64 per i file server<br/><br/> 448 per i server applicazioni | 64 per i file server<br/><br/> 448 per i server applicazioni |Non applicabile |
| Punti di ripristino su nastro |Non applicabile |Illimitato |Non applicabile |Non applicabile |

## <a name="how-does-azure-backup-work-with-encryption"></a>Come funziona Backup di Azure con la crittografia?

**Crittografia** | **Backup in locale** | **Eseguire un backup delle VM di Azure** | **Backup di SQL nelle VM di Azure**
--- | --- | --- | ---
Crittografia di dati inattivi<br/> (Crittografia dei dati nella posizione in cui sono archiviati/salvati in modo permanente) | Per crittografare i dati viene usata una passphrase specificata dal cliente | Per crittografare i dati archiviati nell'insieme di credenziali viene usata la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) di Azure.<br/><br/> Il servizio Backup crittografa automaticamente i dati prima di archiviarli. Il servizio Archiviazione di Azure decrittografa i dati prima di recuperarli. L'uso di chiavi gestite dal cliente per la crittografia del servizio di archiviazione non è attualmente supportato.<br/><br/> È possibile eseguire il backup di macchine virtuali che usano [Crittografia dischi di Azure (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) per crittografare i dischi del sistema operativo e i dischi dati. Backup di Azure supporta le macchine virtuali solo con crittografia BEK e con crittografia BEK e [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/). Vedere le [limitazioni](backup-azure-vms-encryption.md#encryption-support). | Backup di Azure supporta il backup di database di SQL Server o server con crittografia [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) abilitata. È supportata la crittografia TDE con chiavi gestite da Azure o con chiavi gestite dal cliente (BYOK).<br/><br/> Backup di Azure non esegue alcun tipo di crittografia SQL come parte del processo di backup.
Crittografia in transito<br/> (Crittografia dei dati durante lo spostamento da una posizione a un'altra) | I dati vengono crittografati mediante AES256 e inviati all'insieme di credenziali in Azure tramite HTTPS | All'interno di Azure, i dati in transito tra Archiviazione di Azure e l'insieme di credenziali sono protetti tramite HTTPS. Questi dati rimangono all'interno della rete backbone di Azure.<br/><br/> Per il recupero di file, iSCSI protegge i dati trasmessi tra l'insieme di credenziali e la macchina virtuale di Azure. Il tunneling protetto protegge il canale iSCSI. | All'interno di Azure, i dati in transito tra Archiviazione di Azure e l'insieme di credenziali sono protetti tramite HTTPS.<br/><br/> Il recupero di file non è pertinente per SQL.

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare](backup-architecture.md) l'architettura e i componenti per diversi scenari di backup.
- [Verificare](backup-support-matrix.md) i requisiti di supporto e le limitazioni per il backup e per il [backup di macchine Virtuali di Azure](backup-support-matrix-iaas.md).

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
