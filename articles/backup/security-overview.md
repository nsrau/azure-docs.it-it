---
title: Panoramica delle funzionalità di sicurezza
description: Informazioni sulle funzionalità di sicurezza di backup di Azure che consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585826"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Panoramica delle funzionalità di sicurezza di backup di Azure

Una delle operazioni più importanti che è possibile eseguire per proteggere i dati consiste nel disporre di un'infrastruttura di backup affidabile. Tuttavia è altrettanto importante assicurarsi che i dati vengano sottoposti a backup in modo sicuro e che i backup siano sempre protetti. Backup di Azure fornisce sicurezza per l'ambiente di backup, sia quando i dati sono in transito che inattivi. Questo articolo elenca le funzionalità di sicurezza di backup di Azure che consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.

## <a name="management-and-control-of-identity-and-user-access"></a>Gestione e controllo dell'identità e dell'accesso utente

Backup di Azure consente di gestire l'accesso con granularità fine tramite il [controllo degli accessi in base al ruolo (RBAC) di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Il controllo degli accessi in base al ruolo consente di separare i compiti all'interno del team e concedere solo la quantità di accesso agli utenti necessari per svolgere i propri processi.

Backup di Azure offre tre ruoli predefiniti per controllare le operazioni di gestione dei backup:

* Collaboratore di backup: per creare e gestire i backup, eccetto l'eliminazione dell'insieme di credenziali di servizi di ripristino e l'accesso ad altri utenti
* Operatore di backup: tutti gli elementi di un collaboratore tranne la rimozione del backup e la gestione dei criteri
* Lettore di backup: autorizzazioni per visualizzare tutte le operazioni di gestione di backup

Altre informazioni sul [controllo degli accessi in base al ruolo per gestire backup di Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

Backup di Azure include diversi controlli di sicurezza incorporati nel servizio per prevenire, rilevare e rispondere alle vulnerabilità di sicurezza. Altre informazioni sui [controlli di sicurezza per backup di Azure](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Separazione tra archiviazione Guest e archiviazione di Azure

Con backup di Azure, che include il backup della macchina virtuale e SQL e SAP HANA nel backup delle VM, i dati di backup vengono archiviati in archiviazione di Azure e il Guest non ha accesso diretto all'archiviazione di backup o al relativo contenuto.  Con il backup delle macchine virtuali, la creazione e l'archiviazione dello snapshot di backup vengono eseguite dall'infrastruttura di Azure, in cui il cliente non ha alcun coinvolgimento oltre a disattivazione il carico di lavoro per i backup coerenti con l'applicazione.  Con SQL e SAP HANA, l'estensione di backup ottiene l'accesso temporaneo per la scrittura in BLOB specifici.  In questo modo, anche in un ambiente compromesso, i backup esistenti non possono essere alterati o eliminati dal Guest.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Connettività Internet non necessaria per il backup di macchine virtuali di Azure

Il backup di macchine virtuali di Azure richiede lo spostamento dei dati dal disco della macchina virtuale all'insieme di credenziali dei servizi di ripristino. Tuttavia, tutte le comunicazioni e il trasferimento dei dati necessari avvengono solo nella rete backbone di Azure senza dover accedere alla rete virtuale. Per questo motivo, il backup delle macchine virtuali di Azure inserite all'interno di reti protette non richiede l'accesso a qualsiasi IP o FQDN.

## <a name="private-endpoints-for-azure-backup"></a>Endpoint privati per backup di Azure

È ora possibile usare [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per eseguire il backup dei dati in modo sicuro dai server all'interno di una rete virtuale all'insieme di credenziali dei servizi di ripristino. L'endpoint privato usa un IP dallo spazio di indirizzi della VNET per l'insieme di credenziali, pertanto non è necessario esporre le reti virtuali a tutti gli indirizzi IP pubblici. Gli endpoint privati possono essere usati per eseguire il backup e il ripristino dei database SQL e SAP HANA eseguiti nelle macchine virtuali di Azure. Può essere usato anche per i server locali con l'agente MARS.

>[!NOTE]
> Questa funzionalità è attualmente in disponibilità limitata. Compilare [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e inviare un [messaggio di posta elettronica](mailto:azbackupnetsec@microsoft.com) se si è interessati all'uso di endpoint privati per backup di Azure. La possibilità di usare questa funzionalità è soggetta all'approvazione del servizio backup di Azure.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Crittografia dei dati in transito e inattivi

La crittografia protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione. In Azure, i dati in transito tra archiviazione di Azure e l'insieme di credenziali sono protetti da HTTPS. Questi dati rimangono all'interno della rete backbone di Azure.

* I dati di backup vengono crittografati automaticamente usando chiavi gestite da Microsoft. È anche possibile crittografare le macchine virtuali con dischi gestiti di cui è stato eseguito il backup nell'insieme di credenziali di servizi di ripristino usando [chiavi gestite dal cliente](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) archiviate nel Azure Key Vault. Non è necessario eseguire alcuna azione esplicita per abilitare la crittografia. Si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di servizi di ripristino.

* Backup di Azure supporta il backup e il ripristino di macchine virtuali di Azure con i dischi del sistema operativo/dati crittografati con crittografia dischi di Azure (ADE). [Altre informazioni sulle macchine virtuali di Azure crittografate e backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Protezione dei dati di backup da eliminazioni accidentali

Backup di Azure offre funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione. Con l'eliminazione temporanea, se l'utente elimina il backup di una macchina virtuale, i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino di tale elemento di backup senza perdita di dati. Gli altri 14 giorni di conservazione dei dati di backup nello stato "eliminazione temporanea" non comportano alcun costo per il cliente. [Altre informazioni sull'eliminazione](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)temporanea.

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoraggio e avvisi di attività sospette

Backup di Azure offre [funzionalità di monitoraggio e avviso predefinite](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) per visualizzare e configurare le azioni per gli eventi correlati a backup di Azure. I [report di backup](https://docs.microsoft.com/azure/backup/configure-reports) vengono usati come destinazione unica per tenere traccia dell'utilizzo, il controllo di backup e ripristini e l'identificazione delle tendenze principali a diversi livelli di granularità. L'uso degli strumenti di monitoraggio e creazione di report di backup di Azure consente di avvisare l'utente in caso di attività non autorizzate, sospette o dannose non appena si verificano.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funzionalità di sicurezza per proteggere i backup ibridi

Il servizio backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup e il ripristino di file, cartelle e lo stato del volume o del sistema da un computer locale ad Azure. MARS offre ora funzionalità di sicurezza che consentono di proteggere i backup ibridi. Queste funzionalità includono:

* Ogni volta che viene eseguita un'operazione critica, come la modifica di una passphrase, viene aggiunto un ulteriore livello di autenticazione. Questa convalida serve a garantire che tali operazioni possano essere eseguite solo dagli utenti che hanno credenziali di Azure valide. [Altre informazioni sulle funzionalità che impediscono gli attacchi](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* I dati di backup eliminati vengono conservati per altri 14 giorni dalla data di eliminazione. In questo modo si garantisce la recuperabilità dei dati in un determinato periodo di tempo, quindi non si verificherà alcuna perdita di dati anche se si verifica un attacco. Inoltre, viene conservato un maggior numero di punti di recupero per prevenire il rischio di dati danneggiati. [Altre informazioni sul ripristino dei dati di backup eliminati](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* Per i dati sottoposti a backup con l'agente di Servizi di ripristino di Microsoft Azure (MARS), viene usata una passphrase per assicurarsi che i dati vengano crittografati prima del caricamento in backup di Azure e decrittografati solo dopo il download da backup di Azure. I dettagli della passphrase sono disponibili solo per l'utente che ha creato la passphrase e l'agente configurato. Nessun elemento viene trasmesso o condiviso con il servizio. In questo modo si garantisce la sicurezza completa dei dati come tutti i dati esposti inavvertitamente, ad esempio un attacco man-in-the-Middle sulla rete, non possono essere usati senza la passphrase e la passphrase non viene inviata in rete.

## <a name="compliance-with-standardized-security-requirements"></a>Conformità ai requisiti di protezione standardizzati

Per aiutare le organizzazioni a soddisfare i requisiti nazionali, regionali e specifici del settore che regolano la raccolta e l'uso dei dati degli utenti, Microsoft Azure & backup di Azure offre un set completo di certificazioni e attestazioni. [Vedere l'elenco delle certificazioni di conformità](compliance-offerings.md)

## <a name="next-steps"></a>Passaggi successivi

* [Funzionalità di sicurezza che consentono di proteggere i carichi di lavoro cloud che usano backup di Azure](backup-azure-security-feature-cloud.md)
* [Funzionalità di sicurezza per la protezione dei backup ibridi che usano Backup di Azure](backup-azure-security-feature.md)
