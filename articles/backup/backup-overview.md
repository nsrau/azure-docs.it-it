---
title: Informazioni su Backup di Azure
description: Panoramica del servizio Backup di Azure, con informazioni su come distribuirlo nel contesto della strategia di continuità aziendale e ripristino di emergenza.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7156042243b1ba28cea712dc3722600b9fc46c42
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360641"
---
# <a name="what-is-azure-backup"></a>Informazioni su Backup di Azure

Il servizio Backup di Azure consente di eseguire il backup dei dati nel cloud di Microsoft Azure. È possibile eseguire il backup di computer e carichi di lavoro locali, nonché di macchine virtuali di Azure (VM).


## <a name="why-use-azure-backup"></a>Perché usare Backup di Azure

Backup di Azure offre i vantaggi principali seguenti:

- **Offload del backup locale**: Backup di Azure offre una soluzione semplice per eseguire il backup delle risorse locali nel cloud. Consente di ottenere backup a breve e a lungo termine senza la necessità di distribuire complesse soluzioni locali. Elimina la necessità dei backup su nastro o fuori sede.
- **Backup delle VM IaaS di Azure**: Backup di Azure fornisce backup indipendenti e isolati per salvaguardare dalla distruzione accidentale dei dati originali. I backup vengono archiviati in un insieme di credenziali di Servizi di ripristino con la gestione predefinita dei punti di ripristino. La configurazione e la scalabilità sono semplici, i backup sono ottimizzati ed è possibile eseguire il ripristino con facilità secondo necessità.
- **Scalabilità semplificata**: Backup di Azure sfrutta le potenzialità e la scalabilità illimitata del cloud di Azure per offrire disponibilità elevata, senza costi generali di manutenzione o monitoraggio. È possibile impostare avvisi per fornire informazioni sugli eventi, ma non è necessario preoccuparsi della disponibilità elevata dei dati nel cloud.
- **Trasferimento dati senza limiti**: Backup di Azure non prevede limiti per la quantità di dati trasferiti in ingresso o in uscita. Backup di Azure non addebita costi per i dati trasferiti. Se tuttavia si usa il servizio Importazione/esportazione di Azure per importare grandi quantità di dati, viene applicato un costo per i dati in ingresso. Per altre informazioni su questi costi, vedere [Flusso di lavoro del backup offline in Backup di Azure](backup-azure-backup-import-export.md). I dati in uscita sono i dati trasferiti da un insieme di credenziali di Servizi di ripristino durante un'operazione di ripristino.
- **Sicurezza dei dati**: grazie alla crittografia dei dati, la trasmissione e l'archiviazione dei dati sono sicure nel cloud pubblico. La passphrase di crittografia viene archiviata in locale e non viene mai trasmessa o archiviata in Azure. Se è necessario ripristinare i dati, solo il cliente è in possesso della passphrase o della chiave di crittografia.
- **Backup coerenti con le app**: i backup coerenti con le applicazioni implicano che un punto di ripristino ha tutti i dati necessari per ripristinare la copia di backup. Backup di Azure offre backup coerenti con l'applicazione, che eliminano la necessità di correzioni aggiuntive per ripristinare i dati. Il ripristino di dati coerenti con l'applicazione riduce il tempo di ripristino e consente quindi di tornare rapidamente allo stato operativo.
- **Conservazione a breve e a lungo termine**: **Conservazione a lungo termine**: è possibile usare gli insiemi di credenziali dei Servizi di ripristino per la conservazione dei dati a breve termine e a lungo termine. Azure non limita la durata della conservazione dei dati in un insieme di credenziali di dei Servizi di ripristino. È possibile conservare i dati in un insieme di credenziali per il tempo desiderato. Backup di Azure ha un limite di 9999 punti di ripristino per ogni istanza protetta. Vedere la sezione [Backup e conservazione](backup-introduction-to-azure-backup.md#backup-and-retention) in questo articolo per una spiegazione sull'effetto di questo limite sulle esigenze di backup.
- **Gestione automatica dell'archiviazione**. Gli ambienti ibridi richiedono spesso un'archiviazione eterogenea, in parte in locale e in parte nel cloud. Con Backup di Azure non sono previsti costi per l'uso di dispositivi di archiviazione locale. Backup di Azure alloca e gestisce automaticamente le risorse di archiviazione di backup e usa un modello di pagamento in base al consumo. Con il pagamento in base al consumo si pagano solo le risorse di archiviazione effettivamente usate. Per altre informazioni, vedere la pagina relativa ai [prezzi di Azure](https://azure.microsoft.com/pricing/details/backup).
- **Più opzioni di archiviazione**. Un aspetto della disponibilità elevata è la replica di archiviazione. Backup di Azure offre due tipi di replica: [archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md) e [archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md). Scegliere l'opzione di archiviazione di backup in base alle esigenze:
    - L'archiviazione con ridondanza locale replica i dati tre volte (crea tre copie dei dati) in un'unità di scala di archiviazione in un data center. Tutte le copie dei dati si trovano nella stessa area geografica. L'archiviazione con ridondanza locale è un'opzione a costo contenuto per la protezione dei dati da errori hardware locali.
    - L'archiviazione con ridondanza geografica è l'opzione di replica predefinita e consigliata. L'archiviazione con ridondanza geografica replica i dati in un'area secondaria a centinaia di chilometri di distanza dalla posizione primaria dei dati di origine. L'archiviazione con ridondanza geografica è più costosa dell'archiviazione con ridondanza locale, ma offre un livello più elevato di durabilità per i dati, anche in presenza di un'interruzione di area.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Qual è la differenza tra Backup di Azure e Azure Site Recovery?

I servizi Backup di Azure e Azure Site Recovery contribuiscono entrambi al perseguimento di una strategia di continuità aziendale e ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) nell'organizzazione. La strategia BCDR si propone due obiettivi di ampio respiro:

- Mantenere i dati aziendali al sicuro e recuperabili in caso di interruzioni del servizio.
- Mantenere la piena operatività di app e carichi di lavoro durante i tempi di inattività pianificati e non pianificati.

I due servizi offrono funzionalità complementari ma differenti.

- **Azure Site Recovery**: Site Recovery fornisce una soluzione di ripristino di emergenza per i computer locali e per le VM di Azure. I computer vengono replicati da una posizione primaria a una secondaria. In caso di emergenza, viene eseguito il failover dei computer nella seconda posizione, da cui risultano accessibili. Quando viene ripristinata la normale operatività, viene eseguito il failback dei computer nel sito primario per il recupero.
- **Backup di Azure**: il servizio Backup di Azure esegue i backup dei dati da computer locali e da VM di Azure. È possibile eseguire il backup e il ripristino dei dati a livello granulare, includendo file, cartelle, stato del sistema e dati con riconoscimento delle app. Backup di Azure gestisce i dati a livello più granulare rispetto a Site Recovery. Se ad esempio una presentazione in un computer portatile risulta danneggiata, è possibile usare Backup di Azure per ripristinarla. Se si vogliono mantenere i dati e le configurazioni delle VM al sicuro e accessibili, è possibile usare Site Recovery.  

Consultare la tabella seguente per identificare le proprie esigenze ai fini della strategia BCDR. 

**Obiettivo** | **Dettagli** | **Confronto**
--- | --- | --- | --- |
**Backup/conservazione dei dati** | I dati di backup possono essere conservati e archiviati per diversi giorni, mesi o anche anni se necessario ai fini della conformità. | Le soluzioni come Backup di Azure consentono di selezionare con precisione i dati di cui eseguire il backup, nonché di ottimizzare i criteri di backup e conservazione.<br/><br/> Site Recovery non offre lo stesso livello di ottimizzazione.
**Obiettivo del punto di ripristino (RPO)** | Quantità di perdita di dati accettabile se si rende necessario un ripristino. | I backup hanno RPO più variabili.<br/><br/> I backup delle VM hanno in genere un RPO di un giorno, mentre i backup dei database hanno RPO fino a un minimo di 15 minuti.<br/><br/> Site Recovery fornisce un RPO basso, perché la replica è continua o frequente, quindi il delta tra l'origine e la copia di replica è piccolo.
**Obiettivo del tempo di ripristino (RTO)** |Quantità di tempo necessario per completare un ripristino o un recupero. | Dato l'RPO più alto, la quantità di dati che deve essere elaborata dalla soluzione di backup è in genere molto più grande e questo comporta un RTO maggiore. Ad esempio, il ripristino dei dati dai nastri può richiedere giorni, a seconda del tempo richiesto per il trasporto del nastro da una posizione esterna. | Le soluzioni di ripristino di emergenza come Site Recovery hanno un RPO basso, perché la replica continua e/o frequente implica in genere che la destinazione è maggiormente sincronizzata con l'origine. |

## <a name="what-backup-scenarios-are-supported"></a>Quali scenari di backup sono supportati?

Backup di Azure consente di eseguire il backup sia di computer locali che di VM di Azure.

**Computer** | **Scenario di backup**
--- | ---
**Computer locali (fisici/virtuali)** |  È possibile eseguire il backup di singoli computer locali.<br/><br/>È possibile eseguire il backup di computer locali protetti da System Center Data Protection Manager (DPM)<br/><br/> È possibile eseguire il backup di computer locali protetti da server di Backup di Microsoft Azure (MABS).
**VM di Azure** | È possibile eseguire il backup di singole VM di Azure.<br/><br/> È possibile eseguire il backup di VM di Azure protette da DPM o MABS.

### <a name="back-up-servers"></a>Backup dei server

È possibile scegliere di eseguire il backup di server e carichi di lavoro locali, oppure di VM di Azure e relativi carichi di lavoro, prima in un server di backup e poi in un insieme di credenziali di Servizi di ripristino. 

**Server di backup** | **Dettagli**
--- | ---
**System Center Data Protection Manager (DPM)** | È possibile usare Backup di Azure per eseguire il backup di dati protetti da DPM:<br/><br/> - DPM può essere in esecuzione in locale (sistema fisico o virtuale) o in Azure.<br/><br/> - È possibile proteggere diversi tipi di dati in esecuzione nei computer locali e nelle VM di Azure eseguendone il backup nel server DPM.<br/><br/> Il server DPM può essere a sua volta sottoposto a backup in un insieme di credenziali di Servizi di ripristino usando il servizio Backup di Azure.<br/><br/> Il server DPM e i computer che protegge devono essere connessi alla stessa rete. I computer locali possono essere protetti solo da un server DPM locale. Analogamente, le VM di Azure possono essere protette solo da DPM in esecuzione in Azure.
**Server di Backup di Microsoft Azure (MABS)** | È possibile usare Backup di Azure per eseguire il backup di dati protetti da MABS:<br/><br/> - MABS può essere in esecuzione in locale (sistema fisico o virtuale) o in Azure.<br/><br/> - È possibile proteggere diversi tipi di dati in esecuzione nei computer locali e nelle VM di Azure eseguendone il backup in MABS.<br/><br/> - MABS può essere a sua volta sottoposto a backup in un insieme di credenziali di Servizi di ripristino usando il servizio Backup di Azure.<br/><br/> - MABS offre funzionalità simili a DPM, con la differenza che non consente di eseguire il backup su nastro. MABS non richiede una licenza di System Center.<br/><br/> Come nel caso di DPM, i computer locali possono essere protetti solo da un server MABS locale. Le VM di Azure possono essere protette solo da un server MABS in Azure.

L'esecuzione di un backup prima in DPM/MABS e poi in un insieme di credenziali offre i vantaggi seguenti:

- Con DPM/MABS si ottengono backup con riconoscimento delle app ottimizzati per app comuni come SQL Server, Exchange e SharePoint, oltre a backup di file, cartelle, volumi e dello stato del computer (bare metal, stato del sistema).
- Non è necessario installare l'agente di Backup di Azure in ogni computer di cui eseguire il backup. Ogni computer esegue l'agente di protezione di DPM/MABS, mentre l'agente di Servizi di ripristino di Microsoft Azure di Backup di Azure viene eseguito solo nel server DPM/MABS.
- Si ottiene una maggiore flessibilità e opzioni di pianificazione granulari per l'esecuzione dei backup.
- È possibile gestire i backup per più computer raccolti in gruppi di protezione in una singola console. Questa opzione è particolarmente utile se si vuole eseguire contemporaneamente il backup di app divise in livelli tra più computer.

## <a name="what-can-be-backed-up"></a>Cosa può essere sottoposto a backup?

**Computer** | **Server di backup** | **Backup**
--- | --- | ---
VM Windows locali | Backup non eseguito in DPM o MABS | Backup di file, cartelle, stato del sistema.
VM di Azure (Windows e Linux) | Backup non eseguito in DPM o MABS | Backup di file, cartelle, stato del sistema.<br/><br/> I backup sono con riconoscimento delle app per i computer Windows e con riconoscimento dei file per i computer Linux.
VM locali/VM di Azure | Con protezione di DPM | Backup di qualsiasi elemento protetto da DPM, come file, cartelle, condivisioni, volumi e dati specifici delle app. [Informazioni](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) relative agli elementi di cui DPM può eseguire il backup.
VM locali/VM di Azure | Con protezione di MABS | Backup di qualsiasi elemento protetto da MABS, come file, cartelle, condivisioni, volumi e dati specifici delle app. [Informazioni](backup-mabs-protection-matrix.md) relative agli elementi di cui MABS può eseguire il backup.

## <a name="what-backup-agents-do-i-need"></a>Quali agenti di backup sono necessari?
**Scenario** | **Agent** | **Dettagli**
--- | --- | ---
Computer locali (nessun server di backup) | Agente di Servizi di ripristino di Microsoft Azure (MARS) in esecuzione nel computer Windows. | L'agente MARS viene scaricato e installato durante la distribuzione di Backup di Azure.<br/><br/> Sono supportati solo i computer Windows.
VM di Azure (nessun server di backup) | Nessun agente esplicito necessario. L'estensione macchina virtuale di Azure per il backup viene eseguita nella VM di Azure. | L'estensione viene installata quando si esegue il primo backup della VM di Azure.<br/><br/> Sono supportati Windows e Linux.
Computer locali/VM di Azure protetti da DPM. | L'agente MARS viene eseguito nel server DPM. | Non è necessario eseguire l'agente MARS nei singoli computer.<br/><br/> Quando si distribuisce DPM, l'agente di protezione di DPM viene installato in ogni computer da proteggere. 
Backup di computer locali e VM di Azure protetti da MABS | L'agente MARS viene eseguito nel server MABS. | Non è necessario eseguire l'agente MARS nei singoli computer.<br/><br/> Quando si distribuisce MABS, l'agente di protezione di MABS viene installato in ogni computer da proteggere. 


## <a name="which-backup-agent-should-i-use"></a>Quale agente di backup è necessario usare?

**Backup** | **Soluzione** | **Limitazione**
--- | --- | ---
Si vuole eseguire il backup di computer Windows locali. I computer non sono protetti da DPM o MABS | Installare l'agente MARS nel computer. | È possibile eseguire il backup di file, cartelle e stato del sistema in Azure. I backup non sono con riconoscimento delle app.
Si vuole eseguire il backup di computer Linux locali. I computer non sono protetti da DPM o MABS. | È necessario distribuire DPM o MABS per eseguire il backup in Azure.
Si vuole eseguire il backup di app in esecuzione in computer Windows locali | Per i backup con riconoscimento delle app, i computer Windows devono essere protetti da DPM o MABS.
Si vuole eseguire il backup di VM di Azure | Eseguire un backup con Backup di Azure. L'estensione di backup verrà configurata automaticamente nella VM di Azure Windows o Linux. | I dischi della VM vengono sottoposti a backup.<br/><br/> Per le VM Windows, il backup è coerente con le app. Per Linux, il backup è coerente con i file. Se serve il riconoscimento delle app, è necessario configurarlo con script personalizzati.
Si vuole eseguire il backup di VM di Azure con flessibilità granulare delle impostazioni di backup e ripristino | Proteggere le VM di Azure con DPM o MABS in esecuzione in Azure per una maggiore flessibilità per la pianificazione dei backup e per la massima flessibilità per la protezione e il ripristino di file, cartelle, volumi, app e stato del sistema.


## <a name="next-steps"></a>Passaggi successivi

- [Esaminare](backup-architecture.md) l'architettura e i componenti per diversi scenari di backup.
- [Verificare](backup-support-matrix.md) le funzionalità e le impostazioni supportate per il backup.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

