# Panoramica
## [Che cos'è Site Recovery?](site-recovery-overview.md)
## [Funzionamento di Azure Site Recovery](site-recovery-components.md)
## [Funzionamento della replica Hyper-V in Azure](site-recovery-hyper-v-azure-architecture.md)
## [Quali carichi di lavoro è possibile proteggere?](site-recovery-workload.md)
## [Matrice di supporto di Site Recovery](site-recovery-support-matrix-to-azure.md)
## [DOMANDE FREQUENTI](site-recovery-faq.md)
## [Guarda il video introduttivo](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Attività iniziali
## [Replicare VM VMware in Azure](site-recovery-vmware-to-azure.md)
## [Replicare i server fisici in Azure](site-recovery-physical-servers-to-azure.md)
## [Replicare VM Hyper-V in Azure con VMM](site-recovery-vmm-to-azure.md)
## [Replicare VM Hyper-V in Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replicare le VM Hyper-V in un sito secondario con VMM](site-recovery-vmm-to-vmm.md)
## [Replicare VM VMware e server fisici in un sito secondario](site-recovery-vmware-to-vmware.md)
## [Replicare VM VMware in Azure in una distribuzione multi-tenant (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Procedure
## Pianificare
### [Prerequisiti di distribuzione](site-recovery-prereq.md)
### [Pianificare l'infrastruttura di rete](site-recovery-network-design.md)
### [Pianificare la capacità e ridimensionare la replica VMware in Azure](site-recovery-plan-capacity-vmware.md)
### [Deployment Planner per la replica VMware in Azure](site-recovery-deployment-planner.md)
### [Capacity Planner per la replica Hyper-V](site-recovery-capacity-planner.md)
### [Controllo degli accessi in base al ruolo](site-recovery-role-based-linked-access-control.md)

## Configurare
### [Configurare l'ambiente di origine](site-recovery-set-up-vmware-to-azure.md)
### [Configurare l'ambiente di destinazione](site-recovery-prepare-target-vmware-to-azure.md)
### [Configurare le impostazioni di replica](site-recovery-setup-replication-settings-vmware.md)
### [Distribuire il servizio Mobility per la replica VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Distribuire il servizio Mobility con System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Distribuire il servizio Mobility con Automation DSC per Azure](site-recovery-automate-mobility-service-install.md)
### [Abilitare la replica](site-recovery-replicate-vmware-to-azure.md)
## Effettuare il failover e il failback
### [Effettuare il failover di computer protetti](site-recovery-failover.md)
### [Configurare piani di ripristino](site-recovery-create-recovery-plans.md)
#### [Aggiungere runbook di Azure a piani di ripristino](site-recovery-runbook-automation.md)
### [Eseguire un failover di test](site-recovery-test-failover-to-azure.md)
### [Riproteggere i computer dopo il failover](site-recovery-how-to-reprotect.md)
### [Effettuare il failback da Azure](site-recovery-failback-azure-to-vmware.md)

## Migrazione
### [Eseguire la migrazione ad Azure](site-recovery-migrate-to-azure.md)
### [Eseguire la migrazione tra aree di Azure](site-recovery-migrate-azure-to-azure.md)
### [Eseguire la migrazione di istanze Windows per AWS in Azure](site-recovery-migrate-aws-to-azure.md)
## Carichi di lavoro
### [Active Directory e DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [Servizi Desktop remoto](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Altri carichi di lavoro](site-recovery-workload.md#workload-summary)
## Automatizzare la replica
### [Automatizzare la replica Hyper-V in Azure senza VMM](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatizzare la replica Hyper-V in Azure con VMM](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatizzare la replica Hyper-V in un sito secondario con VMM](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Gestisci
### [Modificare le impostazioni di replica](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy.md)
### [Gestire server di elaborazione in Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Gestire il server di configurazione](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Gestire server di elaborazione con scalabilità orizzontale](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Gestire server vCenter](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Rimuovere server e disabilitare la protezione](site-recovery-manage-registration-and-protection.md)
## [Monitorare e risolvere i problemi](site-recovery-monitoring-and-troubleshooting.md)

# Riferimento
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell - Classica](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Risorse correlate
## [Automazione di Azure](/azure/automation/)

# Risorse
## [Percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Prezzi](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=site-recovery)
