---
title: Panoramica delle funzionalità di sicurezza
description: Informazioni sulle funzionalità di sicurezza di Backup di Azure che consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 178518f9f04a789f3cb634797cab650e24864337
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653795"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Panoramica delle funzionalità di sicurezza in Backup di Azure

Una delle decisioni più importanti che è possibile prendere per proteggere i dati consiste nel dotarsi di un'infrastruttura di backup affidabile. Tuttavia è altrettanto importante assicurarsi che i dati vengano sottoposti a backup in modo sicuro e che i backup siano sempre protetti. Backup di Azure offre sicurezza per l'ambiente di backup, sia quando i dati sono in transito che quando sono inattivi. In questo articolo vengono illustrate informazioni sulle funzionalità di sicurezza di Backup di Azure che consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.

## <a name="management-and-control-of-identity-and-user-access"></a>Gestione e controllo dell'identità e dell'accesso utente

Gli account di archiviazione usati dagli insiemi di credenziali di Servizi di ripristino sono isolati e gli utenti non possono accedervi per eventuali azioni dannose. L'accesso è consentito solo tramite le operazioni di gestione di Backup di Azure, ad esempio il ripristino. Backup di Azure consente di controllare le operazioni gestite tramite un accesso con granularità fine usando il [controllo degli accessi in base al ruolo (RBAC)](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault) di Azure. RBAC consente di separare i compiti all'interno del team e concedere agli utenti l'accesso solo nella misura necessaria per consentire loro di svolgere il lavoro.

Backup di Azure offre tre [ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) per controllare le operazioni di gestione del backup:

* Collaboratore di backup: esegue le operazioni di creazione e gestione del backup, ad eccezione dell'eliminazione dell'insieme di credenziali di Servizi di ripristino e della possibilità di consentire l'accesso ad altri utenti
* Operatore di backup: esegue tutte le operazioni svolte da un collaboratore, tranne la rimozione di backup e la gestione dei criteri di backup
* Lettore di backup: ha le autorizzazioni per visualizzare tutte le operazioni di gestione del backup

Per altre informazioni, vedere [Usare il controllo degli accessi in base al ruolo per gestire Backup di Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

Backup di Azure include nel servizio diversi controlli di sicurezza predefiniti per prevenire, rilevare e rispondere alle vulnerabilità di sicurezza. Per altre informazioni, vedere [Controlli di sicurezza per Backup di Azure](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Separazione tra guest e Archiviazione di Azure

Con Backup di Azure, che include il backup della macchina virtuale e SQL e SAP HANA nel backup delle VM, i dati di backup vengono archiviati in Archiviazione di Azure e il guest non ha accesso diretto all'archiviazione di backup o al contenuto relativo.  Con il backup delle macchine virtuali, la creazione e l'archiviazione dello snapshot di backup vengono eseguite dall'infrastruttura di Azure, in cui il guest non ha alcun coinvolgimento oltre alla disattivazione del carico di lavoro per i backup coerenti con l'applicazione.  Con SQL e SAP HANA, l'estensione del backup ottiene l'accesso temporaneo per la scrittura in BLOB specifici.  In questo modo, anche in un ambiente compromesso, i backup esistenti non possono essere alterati o eliminati dal guest.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Connettività Internet non necessaria per il backup delle VM di Azure

Il backup delle macchine virtuali di Azure richiede lo spostamento dei dati dal disco della macchina virtuale all'insieme di credenziali di Servizi di ripristino. Tuttavia, tutte le comunicazioni e il trasferimento dei dati necessari avvengono solo nella rete backbone di Azure senza dover accedere alla rete virtuale. Per questo motivo, il backup delle VM di Azure inserite all'interno di reti protette non richiede di consentire l'accesso a qualsiasi IP o FQDN.

## <a name="private-endpoints-for-azure-backup"></a>Endpoint privati per Backup di Azure

È ora possibile usare gli [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per eseguire il backup dei dati in modo sicuro dai server all'interno di una rete virtuale nell'insieme di credenziali di Servizi di ripristino. L'endpoint privato usa un IP dallo spazio indirizzi della VNET per l'insieme di credenziali, pertanto non è necessario esporre le reti virtuali a indirizzi IP pubblici. È possibile usare gli endpoint privati per eseguire il backup e il ripristino dei database SQL e SAP HANA in esecuzione nelle macchine virtuali di Azure. Possono anche essere usati per i server locali che usano l'agente MARS.

Altre informazioni sugli endpoint privati per Backup di Azure sono disponibili [qui](https://docs.microsoft.com/azure/backup/private-endpoints).

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Crittografia dei dati in transito e inattivi

La crittografia protegge i dati e consente di soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. All'interno di Azure i dati in transito tra Archiviazione di Azure e l'insieme di credenziali sono protetti con HTTPS. Questi dati rimangono all'interno della rete backbone di Azure.

* I dati di backup vengono crittografati automaticamente usando chiavi gestite da Microsoft. È anche possibile crittografare le macchine virtuali con dischi gestiti di cui è stato eseguito il backup nell'insieme di credenziali di Servizi di ripristino usando [chiavi gestite dal cliente](backup-encryption.md#encryption-of-backup-data-using-customer-managed-keys) archiviate in Azure Key Vault. Non è necessario eseguire alcuna azione esplicita per abilitare la crittografia: si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di Servizi di ripristino.

* Backup di Azure supporta il backup e il ripristino delle VM di Azure i cui dischi del sistema operativo e dei dati sono crittografati con Crittografia dischi di Azure. [Altre informazioni sulle VM di Azure crittografate e su Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Protezione dei dati di backup da eliminazioni accidentali

Backup di Azure offre funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione. Con l'eliminazione temporanea, se l'utente elimina il backup di una VM, i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendone il ripristino senza perdita di dati. I 14 giorni aggiuntivi di conservazione dei dati di backup nello stato "eliminazione temporanea" non comportano alcun costo per il cliente. [Altre informazioni sull'eliminazione temporanea](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoraggio e avvisi di attività sospette

Backup di Azure offre [funzionalità di monitoraggio e avviso predefinite](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) per visualizzare e configurare le azioni per gli eventi correlati a Backup di Azure. I [report di Backup](https://docs.microsoft.com/azure/backup/configure-reports) fungono da destinazione unica per tenere traccia dell'utilizzo, controllare backup e ripristino e rilevare le tendenze principali a diversi livelli di granularità. L'uso degli strumenti di monitoraggio e creazione di report di Backup di Azure consente di avvisare l'utente in caso di attività non autorizzate, sospette o dannose non appena si verificano.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funzionalità di sicurezza per la protezione dei backup ibridi

Il servizio Backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure (MARS, Microsoft Azure Recovery Services) per eseguire il backup e il ripristino di file, cartelle e dello stato del volume o del sistema da un computer locale ad Azure. MARS ora offre funzionalità di sicurezza per la protezione dei backup ibridi. Queste funzionalità includono:

* Ogni volta che viene eseguita un'operazione critica, come la modifica di una passphrase, viene aggiunto un ulteriore livello di autenticazione. Questa convalida serve a garantire che tali operazioni possano essere eseguite solo dagli utenti che hanno credenziali di Azure valide. [Altre informazioni sulle funzionalità che impediscono gli attacchi](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* I dati di backup eliminati vengono conservati per altri 14 giorni dalla data di eliminazione. In questo modo, viene garantita la possibilità di recuperare i dati entro un certo periodo di tempo, così da scongiurare la perdita di dati anche in caso di attacco. Inoltre, viene conservato un maggior numero di punti di recupero per prevenire il rischio di dati danneggiati. [Altre informazioni sul ripristino dei dati di backup eliminati](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* Per i dati sottoposti a backup con l'agente di Servizi di ripristino di Microsoft Azure (MARS), viene usata una passphrase per assicurarsi che i dati vengano crittografati prima del caricamento in Backup di Azure e decrittografati solo dopo il download da Backup di Azure. I dettagli della passphrase sono disponibili solo per l'utente che ha creato la passphrase e l'agente che viene con essa configurato. Non viene trasmesso o condiviso niente con il servizio. In questo modo si garantisce la sicurezza completa dei dati perché tutti i dati esposti inavvertitamente, ad esempio nel caso di un attacco man-in-the-middle sulla rete, non possono essere usati senza la passphrase e la passphrase non viene inviata in rete.

## <a name="compliance-with-standardized-security-requirements"></a>Conformità ai requisiti di sicurezza standardizzati

Microsoft Azure e Backup di Azure offrono una gamma completa di certificazioni e attestazioni che consentono alle organizzazioni di soddisfare i requisiti nazionali, regionali e specifici del settore che regolano la raccolta e l'uso dei dati degli utenti. [Elenco di certificazioni per la conformità](compliance-offerings.md)

## <a name="next-steps"></a>Passaggi successivi

* [Funzionalità di sicurezza per la protezione dei carichi di lavoro cloud che usano Backup di Azure](backup-azure-security-feature-cloud.md)
* [Funzionalità di sicurezza per la protezione dei backup ibridi che usano Backup di Azure](backup-azure-security-feature.md)
