---
title: Panoramica delle funzionalità di sicurezza
description: Informazioni sulle funzionalità di sicurezza in Backup di Azure che consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 91a0f29862b0c9c35e562c143e28ebbc6c39cf94
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423184"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Panoramica delle funzionalità di sicurezza in Backup di AzureOverview of security features in Azure Backup

Uno dei passaggi più importanti da fare per proteggere i dati è disporre di un'infrastruttura di backup affidabile. Ma è altrettanto importante garantire che il backup dei dati venga eseguito in modo sicuro e che i backup siano protetti in ogni momento. Backup di Azure offre sicurezza all'ambiente di backup, sia quando i dati sono in transito che inattivi. Questo articolo elenca le funzionalità di sicurezza in Backup di Azure che consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.

## <a name="management-and-control-of-identity-and-user-access"></a>Gestione e controllo dell'identità e dell'accesso degli utenti

Backup di Azure consente di gestire l'accesso con granularità fine tramite il controllo degli accessi in base al ruolo di [Azure.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) Il controllo degli accessi in base al ruolo consente di separare i compiti all'interno del team e di concedere solo la quantità di accesso agli utenti necessari per svolgere il proprio lavoro.

Backup di Azure offre tre ruoli predefiniti per controllare le operazioni di gestione dei backup:Azure Backup provides three built-in roles to control backup management operations:

* Collaboratore di backup - per creare e gestire i backup, ad eccezione dell'eliminazione dell'insieme di credenziali dei servizi di ripristino e dell'accesso ad altri utenti
* Operatore di backup: tutto ciò che un collaboratore esegue tranne la rimozione dei criteri di backup e di backup
* Lettore di backup - autorizzazioni per visualizzare tutte le operazioni di gestione dei backup

Altre informazioni sul controllo degli accessi in base al ruolo per gestire Backup di Azure.Learn more about [Role-Based Access control to manage Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

Backup di Azure include diversi controlli di sicurezza integrati nel servizio per prevenire, rilevare e rispondere alle vulnerabilità della sicurezza. Altre informazioni sui controlli di sicurezza per Backup di Azure .Learn more about [security controls for Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Separazione tra guest e archiviazione di AzureSeparation between guest and Azure storage

Con Backup di Azure, che include il backup della macchina virtuale e SQL e SAP HANA nel backup della macchina virtuale, i dati di backup vengono archiviati nell'archiviazione di Azure e il guest non ha accesso diretto all'archiviazione di backup o al relativo contenuto.  Con il backup della macchina virtuale, la creazione e l'archiviazione dello snapshot di backup viene eseguita dall'infrastruttura di Azure in cui l'ospite non ha alcun coinvolgimento se non la disattivazione del carico di lavoro per i backup coerenti con l'applicazione.  Con SQL e SAP HANA, l'estensione di backup ottiene l'accesso temporaneo per scrivere in BLOB specifici.  In questo modo, anche in un ambiente compromesso, i backup esistenti non possono essere manomessi o eliminati dal guest.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Connettività Internet non necessaria per il backup della macchina virtuale di AzureInternet connectivity not required for Azure VM backup

Il backup delle macchine virtuali di Azure richiede lo spostamento dei dati dal disco della macchina virtuale all'insieme di credenziali di Servizi di ripristino. Tuttavia, tutte le comunicazioni e il trasferimento dei dati necessari vengono eseguiti solo nella rete backbone di Azure senza dover accedere alla rete virtuale. Pertanto, il backup delle macchine virtuali di Azure inserite all'interno di reti protette non richiede l'accesso a qualsiasi IP o FQDN.

## <a name="private-endpoints-for-azure-backup"></a>Endpoint privati per il backup di AzurePrivate Endpoints for Azure backup

È ora possibile utilizzare [gli endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per eseguire il backup dei dati in modo sicuro dai server all'interno di una rete virtuale all'insieme di credenziali di Servizi di ripristino. L'endpoint privato utilizza un indirizzo IP dello spazio di indirizzi della rete virtuale per l'insieme di credenziali, pertanto non è necessario esporre le reti virtuali a qualsiasi IP pubblico. Gli endpoint privati possono essere usati per eseguire il backup e il ripristino dei database SQL e SAP HANA eseguiti all'interno delle macchine virtuali di Azure.Private Endpoints can be used for backing up and restoring your SQL and SAP HANA databases that run inside your Azure VMs. Può essere utilizzato anche per i server locali utilizzando l'agente MARS.

>[!NOTE]
> Questa funzionalità è attualmente a disponibilità limitata. Compilare [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e [inviarci un](mailto:azbackupnetsec@microsoft.com) messaggio di posta elettronica se si è interessati all'uso di endpoint privati per Backup di Azure.Please fill out this survey and email us if you are interested in using Private Endpoints for Azure Backup. La possibilità di usare questa funzionalità è soggetta all'approvazione del servizio Backup di Azure.The ability to use this feature is subject to approval from the Azure Backup service.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Crittografia dei dati in transito e inattivi

La crittografia protegge i dati e consente di soddisfare gli impegni di conformità e sicurezza dell'organizzazione. All'interno di Azure, i dati in transito tra l'archiviazione di Azure e l'insieme di credenziali sono protetti da HTTPS. Questi dati rimangono all'interno della rete backbone di Azure.

* I dati di backup vengono crittografati automaticamente utilizzando chiavi gestite da Microsoft.Backup data is automatically encrypted using Microsoft-managed keys. È anche possibile crittografare le macchine virtuali del disco gestito di cui è stato eseguito il backup nell'insieme di credenziali di Servizi di ripristino usando [le chiavi gestite](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) dei clienti archiviate nell'insieme di credenziali delle chiavi di Azure.You can also encrypt your backed up managed disk VMs in the Recovery Services Vault using customer managed keys stored in the Azure Key Vault. Non è necessario eseguire alcuna azione esplicita per abilitare questa crittografia. Si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di Servizi di ripristino.

* Backup di Azure supporta il backup e il ripristino delle macchine virtuali di Azure con i relativi dischi di sistema operativo/dati crittografati con Crittografia disco di Azure.Azure Backup supports backup and restore of Azure VMs that have their OS/data disks encrypted with Azure Disk Encryption (ADE). [Altre informazioni sulle macchine virtuali di Azure crittografate e su Backup di Azure.Learn more about encrypted Azure VMs and Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Protezione dei dati di backup da eliminazioni involontarie

Backup di Azure offre funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione. Con l'eliminazione temporanea, se l'utente elimina il backup di una macchina virtuale, i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino dell'elemento di backup senza perdita di dati. La conservazione dei dati di backup aggiuntivi di 14 giorni nello stato "eliminazione temporanea" non comporta alcun costo per il cliente. [Ulteriori informazioni sull'eliminazione temporanea](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoraggio e avvisi di attività sospette

Backup di Azure offre funzionalità di monitoraggio e avvisi incorporate per visualizzare e configurare azioni per gli eventi correlati a Backup di Azure.Azure Backup provides [built-in monitoring](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) and alerting capabilities to view and configure actions for events related to Azure Backup. [I report](https://docs.microsoft.com/azure/backup/configure-reports) di backup fungono da destinazione unica per tenere traccia dell'utilizzo, il controllo dei backup e dei ripristini e l'identificazione delle tendenze chiave a diversi livelli di granularità. L'uso degli strumenti di monitoraggio e creazione di report di Azure Backup può avvisare l'utente di qualsiasi attività non autorizzata, sospetta o dannosa non appena si verifica.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funzionalità di sicurezza per proteggere i backup ibridi

Azure Backup service uses the Microsoft Azure Recovery Services (MARS) agent to back up and restore files, folders, and the volume or system state from an on-premises computer to Azure. MARS offre ora funzionalità di sicurezza che consentono di proteggere i backup ibridi. Queste funzionalità includono:

* Ogni volta che viene eseguita un'operazione critica, come la modifica di una passphrase, viene aggiunto un ulteriore livello di autenticazione. Questa convalida serve a garantire che tali operazioni possano essere eseguite solo dagli utenti che hanno credenziali di Azure valide. [Ulteriori informazioni sulle funzionalità che impediscono gli attacchi](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* I dati di backup eliminati vengono conservati per altri 14 giorni dalla data di eliminazione. Ciò garantisce la recuperabilità dei dati entro un determinato periodo di tempo, pertanto non si verifica alcuna perdita di dati anche se si verifica un attacco. Inoltre, viene conservato un maggior numero di punti di recupero per prevenire il rischio di dati danneggiati. [Ulteriori informazioni sul ripristino dei dati di backup eliminati](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

## <a name="compliance-with-standardized-security-requirements"></a>Conformità ai requisiti di sicurezza standardizzati

Per consentire alle organizzazioni di rispettare i requisiti nazionali, regionali e specifici del settore che disciplinano la raccolta e l'utilizzo dei dati delle persone, Microsoft Azure & Backup di Azure offre un set completo di certificazioni e inquietazioni. [Vedi l'elenco delle certificazioni di conformità](compliance-offerings.md)

## <a name="next-steps"></a>Passaggi successivi

* [Funzionalità di sicurezza per proteggere i carichi di lavoro cloud che usano Backup di AzureSecurity features to help protect cloud workloads that use Azure Backup](backup-azure-security-feature-cloud.md)
* [Funzionalità di sicurezza per la protezione dei backup ibridi che usano Backup di Azure](backup-azure-security-feature.md)
