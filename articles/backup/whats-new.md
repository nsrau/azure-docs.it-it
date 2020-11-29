---
title: Novità di Backup di Azure
description: Informazioni sulle nuove funzionalità di backup di Azure.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ba29ddea5d5f096640f2bfc012c44ab06bb3e131
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309665"
---
# <a name="whats-new-in-azure-backup"></a>Novità di Backup di Azure

Backup di Azure migliora costantemente e rilascia nuove funzionalità che migliorano la protezione dei dati in Azure. Queste nuove funzionalità espandono la protezione dei dati in nuovi tipi di carico di lavoro, migliorano la sicurezza e migliorano la disponibilità dei dati di backup. Aggiungono inoltre nuove funzionalità di gestione, monitoraggio e automazione.

Per ulteriori informazioni sulle nuove versioni, è possibile aggiungere un segnalibro a questa pagina o [sottoscrivere gli aggiornamenti qui](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Riepilogo aggiornamenti

- Novembre 2020
  - [Modello di Azure Resource Manager per il backup di una condivisione file di Azure](#azure-resource-manager-template-for-afs-backup)
  - [Backup incrementali per database SAP HANA in macchine virtuali di Azure](#incremental-backups-for-sap-hana-databases)
- Settembre 2020
  - [Centro backup](#backup-center)
  - [Eseguire il backup di Database di Azure per PostgreSQL](#backup-azure-database-for-postgresql)
  - [Backup e ripristino di dischi selettivi](#selective-disk-backup-and-restore)
  - [Ripristino tra aree per database SQL Server e SAP HANA in macchine virtuali di Azure](#cross-region-restore-for-sql-server-and-sap-hana)
  - [Supporto per il backup di macchine virtuali con un massimo di 32 dischi](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Esperienza di configurazione di backup semplificata per SQL in macchine virtuali di Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Backup SAP HANA in macchine virtuali di Azure RHEL](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [Archiviazione con ridondanza della zona (ZRS) per i dati di backup](#zone-redundant-storage-zrs-for-backup-data)
  - [Eliminazione temporanea per carichi di lavoro di SQL Server e SAP HANA in macchine virtuali di Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-resource-manager-template-for-afs-backup"></a>Modello di Azure Resource Manager per il backup AFS

Backup di Azure supporta ora la configurazione del backup per le condivisioni file di Azure esistenti usando un modello di Azure Resource Manager (ARM). Il modello configura la protezione per una condivisione file di Azure esistente specificando i dettagli appropriati per l'insieme di credenziali dei servizi di ripristino e i criteri di backup. Crea facoltativamente un nuovo insieme di credenziali dei servizi di ripristino e un criterio di backup e registra l'account di archiviazione che contiene la condivisione file nell'insieme di credenziali di servizi di ripristino.

Per altre informazioni, vedere [modelli di Azure Resource Manager per backup di Azure](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases"></a>Backup incrementali per database SAP HANA

Backup di Azure supporta ora backup incrementali per i database SAP HANA ospitati nelle VM di Azure. In questo modo è possibile eseguire backup più veloci e più convenienti dei dati SAP HANA.

Per ulteriori informazioni, vedere [diverse opzioni disponibili durante la creazione di un criterio di backup](sap-hana-faq-backup-azure-vm.md#policy) e [come creare criteri di backup per SAP Hana database](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center"></a>Centro backup

Backup di Azure ha abilitato una nuova funzionalità di gestione nativa per gestire l'intero patrimonio di backup da una console centrale. Il centro di backup ti offre la possibilità di monitorare, operare, governare e ottimizzare la protezione dei dati su larga scala in modo unificato, coerente con le esperienze di gestione native di Azure.

Con backup Center puoi ottenere una visualizzazione aggregata dell'inventario in sottoscrizioni, località, gruppi di risorse, insiemi di credenziali e persino tenant con Azure Lighthouse. Il centro di backup è anche un centro operativo dal quale è possibile attivare le attività correlate al backup, ad esempio la configurazione di backup, ripristino, creazione di criteri o insiemi di credenziali, da un'unica posizione. Inoltre, grazie all'integrazione semplice con i criteri di Azure, è ora possibile governare l'ambiente e tenere traccia della conformità dal punto di vista del backup. I criteri di Azure predefiniti specifici di backup di Azure consentono anche di configurare backup su larga scala.

Per ulteriori informazioni, vedere [Panoramica di Centro backup](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql"></a>Eseguire il backup di Database di Azure per PostgreSQL

Backup di Azure e i servizi di database di Azure sono disponibili per creare una soluzione di backup di livello aziendale per Azure PostgreSQL (ora disponibile in anteprima). È ora possibile soddisfare le esigenze di protezione e conformità dei dati con criteri di backup controllati dal cliente che consentono la conservazione dei backup per un massimo di 10 anni. In questo modo si dispone di un controllo granulare per gestire le operazioni di backup e ripristino a livello di singolo database. Analogamente, è possibile eseguire il ripristino in più versioni di PostgreSQL o nell'archiviazione BLOB con facilità.

Per altre informazioni, vedere [backup di database di Azure per PostgreSQL](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Backup e ripristino di dischi selettivi

Backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale insieme usando la soluzione di backup della macchina virtuale. A questo punto, usando la funzionalità di backup e ripristino dei dischi selettivi, è possibile eseguire il backup di un subset di dischi dati in una macchina virtuale. Questa è una soluzione efficiente ed economica per soddisfare le esigenze di backup e ripristino. Ogni punto di ripristino contiene solo i dischi inclusi nell'operazione di backup.

Per altre informazioni, vedere [backup e ripristino di dischi selettivi per macchine virtuali di Azure](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>Ripristino tra aree per SQL Server e SAP HANA

Con l'introduzione del ripristino tra più aree, è ora possibile avviare i ripristini in un'area secondaria in modo da mitigare i problemi di tempo di inattività reali in un'area primaria per l'ambiente. In questo modo, l'area secondaria esegue il ripristino completamente controllato dal cliente. Backup di Azure usa i dati di cui è stato eseguito il backup replicati nell'area secondaria per tali ripristini.

Ora, oltre al supporto per il ripristino tra aree per macchine virtuali di Azure, la funzionalità è stata estesa al ripristino di database SQL e SAP HANA anche in macchine virtuali di Azure.

Per ulteriori informazioni, vedere [ripristino tra aree per database SQL](restore-sql-database-azure-vm.md#cross-region-restore) e [ripristino tra aree per database SAP Hana](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Supporto per il backup di macchine virtuali con un massimo di 32 dischi

Fino ad ora, backup di Azure ha supportato 16 dischi gestiti per macchina virtuale. Backup di Azure supporta ora il backup di un massimo di 32 dischi gestiti per macchina virtuale.

Per altre informazioni, vedere la [matrice di supporto per l'archiviazione delle macchine virtuali](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Configurazione di backup più semplice per SQL in macchine virtuali di Azure

La configurazione dei backup per la SQL Server in macchine virtuali di Azure è ora ancora più semplice con la configurazione del backup inline integrata nel riquadro VM del portale di Azure. In pochi passaggi è possibile abilitare il backup del SQL Server per proteggere tutti i database esistenti, oltre a quelli che vengono aggiunti in futuro.

Per altre informazioni, vedere [eseguire il backup di un SQL Server dal riquadro VM](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>Backup SAP HANA in macchine virtuali di Azure RHEL

Backup di Azure è la soluzione di backup nativa per Azure e BackInt è certificata da SAP. Backup di Azure ora ha aggiunto il supporto per Red Hat Enterprise Linux (RHEL), uno dei sistemi operativi Linux più diffusi che eseguono SAP HANA.

Per ulteriori informazioni, vedere la [matrice di supporto dello scenario di backup del database SAP Hana](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>Archiviazione con ridondanza della zona (ZRS) per i dati di backup

Archiviazione di Azure offre un ottimo equilibrio tra prestazioni elevate, disponibilità elevata e resilienza elevata dei dati con le varie opzioni di ridondanza. Backup di Azure consente di estendere questi vantaggi anche ai dati di backup, con le opzioni per archiviare i backup nell'archiviazione con ridondanza locale (con ridondanza locale) e nell'archiviazione con ridondanza geografica (GRS). Sono ora disponibili opzioni di durabilità aggiuntive con il supporto aggiunto per l'archiviazione con ridondanza della zona (ZRS).

Per altre informazioni, vedere [impostare la ridondanza di archiviazione per l'insieme di credenziali di servizi di ripristino](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Eliminazione temporanea per carichi di lavoro SQL Server e SAP HANA

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per proteggersi da tali attacchi, backup di Azure fornisce funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione.

Una di queste funzionalità è l'eliminazione temporanea. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina un backup (o elimina accidentalmente i dati di backup), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino di tale elemento di backup senza perdita di dati. Gli altri 14 giorni di conservazione per i dati di backup nello stato "eliminazione temporanea" non comportano alcun costo.

Oltre al supporto per l'eliminazione temporanea per le macchine virtuali di Azure, i carichi di lavoro SQL Server e SAP HANA in macchine virtuali di Azure sono protetti anche con l'eliminazione temporanea.

Per altre informazioni, vedere [eliminazione temporanea per SQL Server in una macchina virtuale di Azure e SAP Hana nei carichi di lavoro delle macchine virtuali di Azure](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Passaggi successivi

- [Indicazioni e procedure consigliate per il backup di Azure](guidance-best-practices.md)
