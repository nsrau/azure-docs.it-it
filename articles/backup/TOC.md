
# Panoramica
## [Informazioni su Backup di Azure](backup-introduction-to-azure-backup.md)

# Introduzione
## [Eseguire un backup delle VM di Azure](backup-azure-vms-first-look-arm.md)
## [Eseguire un backup di Windows Server o di computer Windows](backup-try-azure-backup-in-10-mins.md)
## [Eseguire il backup dei server VMware](backup-azure-backup-server-vmware.md)

# Procedure

## Server di backup di Azure
### [Matrice di protezione del server di Backup di Azure](backup-mabs-protection-matrix.md)
### Installare o aggiornare
#### [Preparare i carichi di lavoro del server di Backup di Azure nel portale di Azure](backup-azure-microsoft-azure-backup.md)
#### [Preparare i carichi di lavoro del server di Backup di Azure nel portale classico](backup-azure-microsoft-azure-backup-classic.md)
#### [Aggiungere risorse di archiviazione al server di Backup di Azure](backup-mabs-add-storage.md)
#### [Aggiornare il server di Backup di Azure alla versione 2](backup-mabs-upgrade-to-v2.md)
#### [Installazione automatica del server di Backup di Azure](backup-mabs-unattended-install.md)
### Proteggere i carichi di lavoro
#### [Usare il server di Backup di Azure per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md)
#### [Usare il server di Backup di Azure per eseguire il backup di Exchange](backup-azure-exchange-mabs.md)
#### [Usare il server di Backup di Azure per eseguire il backup di una farm di SharePoint](backup-azure-backup-sharepoint-mabs.md)
#### [Usare il server di Backup di Azure per eseguire il backup di SQL](backup-azure-sql-mabs.md)
#### [Proteggere lo stato del sistema e il ripristino bare metal](backup-mabs-system-state-and-bmr.md)
### [Ripristinare i dati da un server di Backup di Azure](backup-azure-alternate-dpm-server.md)

## Macchine virtuali di Azure
### Preparare la macchina virtuale
#### [Preparare le macchine virtuali distribuite da Resource Manager](backup-azure-arm-vms-prepare.md)
#### [Backup coerenti con l'applicazione di VM Linux](backup-azure-linux-app-consistent.md)
#### [Preparare le macchine virtuali di Azure](backup-azure-vms-prepare.md)
### Pianificare l'ambiente
#### [Pianificare l'infrastruttura di backup delle macchine virtuali](backup-azure-vms-introduction.md)
### Eseguire il backup delle VM
#### [Backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino](backup-azure-arm-vms.md)
#### [Backup delle macchine virtuali crittografate](backup-azure-vms-encryption.md)
#### [Eseguire il backup di macchine virtuali di Azure](backup-azure-vms.md)
### Gestire e monitorare le VM
#### [Gestire i backup delle macchine virtuali di Azure nel portale di Azure](backup-azure-manage-vms.md)
#### [Monitorare gli avvisi per i backup delle macchine virtuali di Azure nel portale di Azure](backup-azure-monitor-vms.md)
#### [Gestire e monitorare i backup delle macchine virtuali di Azure nel portale classico](backup-azure-manage-vms-classic.md)
### Ripristinare i dati dalle VM
#### [Ripristinare i file da backup di macchine virtuali di Azure](backup-azure-restore-files-from-vm.md)
#### [Ripristinare macchine virtuali distribuite con Resource Manager nel portale di Azure](backup-azure-arm-restore-vms.md)
#### [Ripristinare le macchine virtuali crittografate](backup-azure-vms-encryption.md)
#### [Ripristinare macchine virtuali in Azure](backup-azure-restore-vms.md)
#### [Ripristinare la chiave e il segreto di Key Vault per le macchine virtuali crittografate](backup-azure-restore-key-secret.md)

## Configurare report di Backup di Azure
### [Configurare i report di Backup di Azure](backup-azure-configure-reports.md)
### [Modello di dati per i report di Backup di Azure](backup-azure-reports-data-model.md)
### [Modello di dati per di Log Analytics per Backup di Azure](backup-azure-log-analytics-data-model.md)

## Data Protection Manager
### [Preparare carichi di lavoro DPM nel portale di Azure](backup-azure-dpm-introduction.md)
### [Preparare carichi di lavoro DPM nel portale classico](backup-azure-dpm-introduction-classic.md)
### [Usare System Center DPM per eseguire il backup del server di Exchange](backup-azure-backup-exchange-server.md)
### [Ripristinare i dati in un server DPM alternativo](backup-azure-alternate-dpm-server.md)
### [Usare DPM per eseguire il backup di carichi di lavoro di SQL Server](backup-azure-backup-sql.md)
### [Usare DPM per eseguire il backup di una farm di SharePoint](backup-azure-backup-sharepoint.md)

## Usare PowerShell
### [Macchine virtuali di Azure nel portale di Azure](backup-azure-vms-automation.md)
### [Macchine virtuali di Azure nel portale classico](backup-azure-vms-classic-automation.md)
### [DPM nel portale di Azure](backup-dpm-automation.md)
### [DPM nel portale classico](backup-dpm-automation-classic.md)
### [Windows Server nel portale di Azure](backup-client-automation.md)
### [Windows Server nel portale classico](backup-client-automation-classic.md)

## Database SQL di Azure
### [Configurare la conservazione del backup a lungo termine](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Visualizzare i backup in un insieme di credenziali dei Servizi di ripristino](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Ripristinare dalla conservazione del backup a lungo termine](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Eliminare i backup di Azure SQL a lungo termine](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Windows Server
### [Eseguire un backup dei file e delle cartelle di Windows Server](backup-configure-vault.md)
### [Eseguire un backup dello stato del sistema Windows Server](backup-azure-system-state.md)
### [Recuperare i file da Azure a Windows Server](backup-azure-restore-windows-server.md)
### [Ripristinare lo stato del sistema Windows Server](backup-azure-restore-system-state.md)
### [Monitorare e gestire insiemi di credenziali di Servizi di ripristino](backup-azure-manage-windows-server.md)
### Eseguire un backup e ripristino con il portale classico
#### [Windows Server con il modello di distribuzione classica](backup-configure-vault-classic.md)
#### [Gestire insiemi di credenziali di Backup con il modello di distribuzione classica](backup-azure-manage-windows-server-classic.md)
#### [Ripristinare file in un server Windows usando il modello di distribuzione classica](backup-azure-restore-windows-server-classic.md)

## Insieme di credenziali dei servizi di ripristino
### [Panoramica degli insiemi di credenziali dei servizi di ripristino](backup-azure-recovery-services-vault-overview.md)
### [Aggiornamento di un insieme di credenziali di backup a insieme di credenziali dei servizi di ripristino](backup-azure-upgrade-backup-to-recovery-services.md)
### [Eliminare un insieme di credenziali di Servizi di ripristino](backup-azure-delete-vault.md)

## Risoluzione dei problemi
### [Problemi relativi al backup delle macchine virtuali di Azure nel portale di Azure](backup-azure-vms-troubleshoot.md)
### [Problemi relativi al backup delle macchine virtuali di Azure nel portale classico](backup-azure-vms-troubleshoot-classic.md)
### [Errore di backup delle VM di Azure: non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot a causa del timeout della sottoattività di creazione snapshot della macchina virtuale](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Rallentamento delle prestazioni di backup di file e cartelle in Backup di Azure](backup-azure-troubleshoot-slow-backup-performance-issue.md)
### [Risolvere i problemi del server di Backup di Azure](backup-azure-mabs-troubleshoot.md)

# Concetti

## domande frequenti
### [Domande frequenti sull'insieme di credenziali dei Servizi di ripristino](backup-azure-backup-faq.md)
### [Domande frequenti sul backup delle macchine virtuali di Azure](backup-azure-vm-backup-faq.md)
### [Domande frequenti sul backup di file-cartelle tramite l'agente di Backup di Azure](backup-azure-file-folder-backup-faq.md)

## [Controllo degli accessi in base al ruolo](backup-rbac-rs-vault.md)
## [Sicurezza per i backup ibridi](backup-azure-security-feature.md)
## [Configurare il backup offline](backup-azure-backup-import-export.md)
## [Sostituire la libreria di nastri](backup-azure-backup-cloud-as-tape.md)


# Riferimento
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Prezzi](https://azure.microsoft.com/pricing/details/backup/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=backup)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=backup)
