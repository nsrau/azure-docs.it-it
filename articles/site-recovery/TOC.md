# Panoramica
## [Che cos'è Site Recovery?](site-recovery-overview.md)
## Funzionamento di Azure Site Recovery
### [Architetture da Azure ad Azure](site-recovery-azure-to-azure-architecture.md)
### [Architetture da Hyper-V ad Azure](site-recovery-architecture-hyper-v-to-azure.md)
### [Architettura di replica in un sito secondario](site-recovery-architecture-to-secondary-site.md)
## [Quali carichi di lavoro è possibile proteggere?](site-recovery-workload.md)
## Matrice di supporto di Site Recovery
### [Supporto da Azure ad Azure](site-recovery-support-matrix-azure-to-azure.md)
### [Supporto da locale ad Azure](site-recovery-support-matrix-to-azure.md)
### [Supporto da locale a sito secondario](site-recovery-support-matrix-to-sec-site.md)
## [Domande frequenti](site-recovery-faq.md)
## [Guarda il video introduttivo](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Attività iniziali
## [Replicare le VM di Azure (anteprima)](site-recovery-azure-to-azure.md)
## [Replicare VM VMware in Azure](vmware-walkthrough-overview.md)
### [Passaggio 1: Esaminare l'architettura](vmware-walkthrough-architecture.md)
### [Passaggio 2: Esaminare i prerequisiti e le limitazioni](vmware-walkthrough-prerequisites.md)
### [Passaggio 3: Pianificare la capacità](vmware-walkthrough-capacity.md)
### [Passaggio 4: Pianificare la rete](vmware-walkthrough-network.md)
### [Passaggio 5: Preparare Azure](vmware-walkthrough-prepare-azure.md)
### [Passaggio 6: Preparare VMware](vmware-walkthrough-prepare-vmware.md)
### [Passaggio 7: Creare un insieme di credenziali](vmware-walkthrough-create-vault.md)
### [Passaggio 8: Configurare l'origine e la destinazione](vmware-walkthrough-source-target.md)
### [Passaggio 9: Creare criteri di replica](vmware-walkthrough-replication.md)
### [Passaggio 10: Installare il servizio Mobility](vmware-walkthrough-install-mobility.md)
### [Passaggio 11: Abilitare la replica](vmware-walkthrough-enable-replication.md)
### [Passaggio 12: Eseguire un failover di test](vmware-walkthrough-test-failover.md)
## [Replicare VM Hyper-V in Azure](hyper-v-site-walkthrough-overview.md)
### [Passaggio 1: Esaminare l'architettura](hyper-v-site-walkthrough-architecture.md)
### [Passaggio 2: Esaminare i prerequisiti e le limitazioni](hyper-v-site-walkthrough-prerequisites.md)
### [Passaggio 3: Pianificare la capacità](hyper-v-site-walkthrough-capacity.md)
### [Passaggio 4: Pianificare la rete](hyper-v-site-walkthrough-network.md)
### [Passaggio 5: Preparare Azure](hyper-v-site-walkthrough-prepare-azure.md)
### [Passaggio 6: Preparare host Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md)
### [Passaggio 7: Creare un insieme di credenziali](hyper-v-site-walkthrough-create-vault.md)
### [Passaggio 8: Configurare l'origine e la destinazione](hyper-v-site-walkthrough-source-target.md)
### [Passaggio 9: Creare criteri di replica](hyper-v-site-walkthrough-replication.md)
### [Passaggio 10: Abilitare la replica](hyper-v-site-walkthrough-enable-replication.md)
### [Passaggio 11: Eseguire un failover di test](hyper-v-site-walkthrough-test-failover.md)
## [Replicare VM Hyper-V in Azure con VMM](site-recovery-vmm-to-azure.md)
## [Replicare i server fisici in Azure](physical-walkthrough-overview.md)
### [Passaggio 1: Esaminare l'architettura](physical-walkthrough-architecture.md)
### [Passaggio 2: Esaminare i prerequisiti e le limitazioni](physical-walkthrough-prerequisites.md)
### [Passaggio 3: Pianificare la capacità](physical-walkthrough-capacity.md)
### [Passaggio 4: Pianificare la rete](physical-walkthrough-network.md)
### [Passaggio 5: Preparare Azure](physical-walkthrough-prepare-azure.md)
### [Passaggio 6: Creare un insieme di credenziali](physical-walkthrough-create-vault.md)
### [Passaggio 7: Configurare l'origine e la destinazione](physical-walkthrough-source-target.md)
### [Passaggio 8: Creare criteri di replica](physical-walkthrough-replication.md)
### [Passaggio 9: Installare il servizio Mobility](physical-walkthrough-install-mobility.md)
### [Passaggio 10: Abilitare la replica](physical-walkthrough-enable-replication.md)
### [Passaggio 11: Eseguire un failover di test](physical-walkthrough-test-failover.md)
## [Replicare le VM Hyper-V in un sito secondario con VMM](site-recovery-vmm-to-vmm.md)
## [Replicare VM VMware e server fisici in un sito secondario](site-recovery-vmware-to-vmware.md)
## [Replicare VM VMware in Azure in una distribuzione multi-tenant (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Procedure
## Pianificare
### [Prerequisiti per la replica di Azure](site-recovery-azure-to-azure-prereq.md)
### Pianificare la rete
#### [Pianificare la rete per la replica da Azure ad Azure (anteprima)](site-recovery-azure-to-azure-networking-guidance.md)
#### [Pianificare la rete per la replica di un computer locale](site-recovery-network-design.md)
#### [Pianificare il mapping di rete per la replica di una VM di Azure](site-recovery-network-mapping-azure-to-azure.md)
#### [Pianificare il mapping di rete per la replica di una VM Hyper-V](site-recovery-network-mapping.md)
### Pianificare capacità e scalabilità
#### [Pianificare la capacità per la replica VMware in Azure](site-recovery-plan-capacity-vmware.md)
#### [Deployment Planner per la replica VMware in Azure](site-recovery-deployment-planner.md)
#### [Capacity Planner per la replica Hyper-V](site-recovery-capacity-planner.md)
### [Pianificare l'accesso in base al ruolo per la replica delle VM](site-recovery-role-based-linked-access-control.md)
## Configurare
### Configurare l'ambiente di origine
#### [Ambiente di origine per la replica da VMware ad Azure](site-recovery-set-up-vmware-to-azure.md)
#### [Ambiente di origine per la replica da computer fisico ad Azure](site-recovery-set-up-physical-to-azure.md)
### Configurare l'ambiente di destinazione
#### [Ambiente di destinazione per la replica da VMware ad Azure](site-recovery-prepare-target-vmware-to-azure.md)
#### [Ambiente di destinazione per la replica da computer fisico ad Azure](site-recovery-prepare-target-physical-to-azure.md)
### [Configurare le impostazioni di replica](site-recovery-setup-replication-settings-vmware.md)
### [Distribuire il servizio Mobility per la replica VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Distribuire il servizio Mobility con System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Distribuire il servizio Mobility con Automation DSC per Azure](site-recovery-automate-mobility-service-install.md)
### Abilitare la replica
#### [Abilitare la replica da Azure ad Azure](site-recovery-replicate-azure-to-azure.md)
#### [Abilitare la replica da VMware ad Azure](site-recovery-replicate-vmware-to-azure.md)
## Effettuare il failover e il failback
### [Configurare piani di ripristino](site-recovery-create-recovery-plans.md)
#### [Aggiungere runbook di Azure a piani di ripristino](site-recovery-runbook-automation.md)
### Eseguire un failover di test
#### [Eseguire un failover di test in Azure](site-recovery-test-failover-to-azure.md)
#### [Eseguire un failover di test tra cloud VMM](site-recovery-test-failover-vmm-to-vmm.md)
### [Effettuare il failover di computer protetti](site-recovery-failover.md)
### Abilitare la riprotezione dei computer dopo il failover
#### [Abilitare la riprotezione da un'area secondaria di Azure a una primaria](site-recovery-how-to-reprotect-azure-to-azure.md)
#### [Abilitare la riprotezione da Azure a locale](site-recovery-how-to-reprotect.md)
### Effettuare il failback da Azure
#### [Effettuare il failback da Azure a VMware](site-recovery-failback-azure-to-vmware.md)
#### [Effettuare il failback da Azure a Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md)
## Migrazione
### [Eseguire la migrazione ad Azure](site-recovery-migrate-to-azure.md)
### [Eseguire la migrazione tra aree di Azure](site-recovery-migrate-azure-to-azure.md)
### [Eseguire la migrazione di istanze Windows per AWS in Azure](site-recovery-migrate-aws-to-azure.md)
### [Replicare le macchine virtuali sottoposte a migrazione in un'altra area di Azure](site-recovery-azure-to-azure-after-migration.md)
## Carichi di lavoro
### [Active Directory e DNS](site-recovery-active-directory.md)
### [Replicare SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [Servizi Desktop remoto](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Applicazioni Web basate su IIS](site-recovery-iis.md)
### [Citrix XenApp e XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Altri carichi di lavoro](site-recovery-workload.md#workload-summary)
## Automatizzare la replica
### [Automatizzare la replica Hyper-V in Azure senza VMM](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatizzare la replica Hyper-V in Azure con VMM](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatizzare la replica Hyper-V in un sito secondario con VMM](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Gestisci
### [Gestire server di elaborazione in Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Gestire il server di configurazione](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Gestire server di elaborazione con scalabilità orizzontale](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Gestire server vCenter](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Rimuovere server e disabilitare la protezione](site-recovery-manage-registration-and-protection.md)
### [Eliminare un insieme di credenziali dei servizi di ripristino](delete-vault.md)

## Monitorare e risolvere i problemi
### [Problemi di replica da Azure ad Azure](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [Problemi di replica da sito locale ad Azure](site-recovery-vmware-to-azure-protection-troubleshoot.md)
### [Raccogliere i log e risolvere i problemi locali](site-recovery-monitoring-and-troubleshooting.md)

# Riferimento
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell - Classica](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Risorse correlate
## [Automazione di Azure](/azure/automation/)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Prezzi](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=site-recovery)
