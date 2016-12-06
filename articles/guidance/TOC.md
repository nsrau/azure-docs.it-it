# Architettura

## Concetti fondamentali sul cloud

### [Progettare applicazioni resilienti](guidance-resiliency-overview.md)
#### [Elenco di controllo per la resilienza](guidance-resiliency-checklist.md)
#### [Analisi della modalità di errore](guidance-resiliency-failure-mode-analysis.md)
#### [Ripristino di emergenza](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Ripristino di emergenza e disponibilità elevata](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Disponibilità elevata](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Elenco di controllo per la disponibilità elevata](..\resiliency\resiliency-high-availability-checklist.md)
#### [Indicazioni sulla resilienza per servizi specifici di Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Ripristino dal danneggiamento o dall'eliminazione accidentale dei dati](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Ripristino da errori locali](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Ripristino dopo un'interruzione di servizio a livello di area](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Ripristino dall'ambiente locale ad Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Indicazioni tecniche sulla resilienza di Azure](..\resiliency\resiliency-technical-guidance.md)


## Architetture di riferimento

### [Eseguire carichi di lavoro di VM in Azure](guidance-ra-compute.md)
#### [Eseguire una VM Linux in Azure](guidance-compute-single-vm-linux.md)
#### [Eseguire una VM Windows in Azure](guidance-compute-single-vm.md)
#### [Eseguire più macchine virtuali in Azure per la scalabilità e la disponibilità](guidance-compute-multi-vm.md)
#### [Eseguire macchine virtuali per un'architettura a più livelli](guidance-compute-n-tier-vm-linux.md)
#### [Eseguire macchine virtuali Windows per un'architettura a più livelli](guidance-compute-n-tier-vm.md)
#### [Eseguire macchine virtuali Linux in più aree per una disponibilità elevata](guidance-compute-multiple-datacenters-linux.md)
#### [Eseguire macchine virtuali Windows in più aree per una disponibilità elevata](guidance-compute-multiple-datacenters.md)

### [Connettere la rete locale ad Azure](guidance-ra-hybrid-networking.md)
#### [Architettura di rete ibrida con Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Architettura di rete ibrida con Azure e la rete VPN locale](guidance-hybrid-network-vpn.md)
#### [Architettura di rete ibrida a disponibilità elevata](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Proteggere il limite cloud in Azure](guidance-ra-network-security.md)
#### [Architettura di rete ibrida sicura in Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Rete perimetrale tra Azure e Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Gestire le identità in Azure](guidance-ra-identity.md)
#### [Implementare Azure Active Directory](guidance-identity-aad.md)
#### [Estendere Active Directory Domain Services ad Azure](guidance-identity-adds-extend-domain.md)
#### [Creare una foresta di risorse di Active Directory Domain Services in Azure](guidance-identity-adds-resource-forest.md)
#### [Implementare AD FS in Azure](guidance-identity-adfs.md)

### [Architettura delle applicazioni Web per il servizio app di Azure](guidance-ra-app-service.md)
#### [Applicazione Web di base](guidance-web-apps-basic.md)
#### [Applicazione Web con disponibilità elevata](guidance-web-apps-multi-region.md)
#### [Migliorare la scalabilità in un'applicazione Web](guidance-web-apps-scalability.md)


## Procedure consigliate per le applicazioni cloud

### [Linee guida per la progettazione di un'API](..\best-practices-api-design.md)
### [Guida all'implementazione di API](..\best-practices-api-implementation.md)
### [Indicazioni sulla scalabilità automatica](..\best-practices-auto-scaling.md)
### [Elenco di controllo della disponibilità](..\best-practices-availability-checklist.md)
### [Linee guida per i processi in background](..\best-practices-background-jobs.md)
### [Aree abbinate di Azure](..\best-practices-availability-paired-regions.md)
### [Informazioni aggiuntive sulla memorizzazione nella cache](..\best-practices-caching.md)
### [Indicazioni sulla rete per la distribuzione di contenuti](..\best-practices-cdn.md)
### [Linee guida di partizionamento di dati](..\best-practices-data-partitioning.md)
### [Indicazioni di monitoraggio e diagnostica](..\best-practices-monitoring.md)
### [Servizi cloud Microsoft e sicurezza di rete](..\best-practices-network-security.md)
### [Procedure consigliate per la progettazione di modelli di Azure Resource Manager](..\best-practices-resource-manager-design-templates.md)
### [Convenzioni di denominazione consigliate per le risorse di Azure](guidance-naming-conventions.md)
### [Considerazioni sulla sicurezza per Azure Resource Manager](..\best-practices-resource-manager-security.md)
### [Condivisione dello stato in modelli di Azure Resource Manager](..\best-practices-resource-manager-state.md)
### [Indicazioni generali per la ripetizione di tentativi](..\best-practices-retry-general.md)
### [Indicazioni specifiche del servizio per la ripetizione di tentativi](..\best-practices-retry-service-specific.md)
### [Elenco di controllo per la scalabilità](..\best-practices-scalability-checklist.md)


## Guide relative agli scenari

### [Indicazioni su Elasticsearch in Azure](guidance-elasticsearch.md)
#### [Eseguire Elasticsearch in Azure](guidance-elasticsearch-running-on-azure.md)
#### [Ottimizzare le prestazioni di inserimento dei dati](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Ottimizzare le prestazioni dell'aggregazione dei dati e delle query](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configurare la resilienza e il ripristino](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Creare un ambiente di test delle prestazioni](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementare un piano di test di JMeter](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Distribuire un campionatore JUnit per JMeter](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Eseguire i test di resilienza automatizzati](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Eseguire i test delle prestazioni automatizzati](guidance-elasticsearch-running-automated-performance-tests.md)

### [Gestione delle identità per le applicazioni multi-tenant](guidance-multitenant-identity.md)
#### [Panoramica](guidance-multitenant-identity-intro.md)
#### [Applicazione Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Autenticazione con Azure AD e OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Identità basate sulle attestazioni](guidance-multitenant-identity-claims.md)
#### [Iscrizione e caricamento del tenant](guidance-multitenant-identity-signup.md)
#### [Ruoli applicazione](guidance-multitenant-identity-app-roles.md)
#### [Autorizzazione basata sui ruoli e sulle risorse](guidance-multitenant-identity-authorize.md)
#### [Proteggere un'API Web back-end](guidance-multitenant-identity-web-api.md)
#### [Memorizzare nella cache i token di accesso](guidance-multitenant-identity-token-cache.md)
#### [Federazione con il servizio AD FS del cliente](guidance-multitenant-identity-adfs.md)
#### [Usare le asserzioni client per ottenere token di accesso](guidance-multitenant-identity-client-assertion.md)
#### [Usare l'insieme di credenziali delle chiavi di Azure per proteggere i segreti dell'applicazione](guidance-multitenant-identity-keyvault.md)
#### [Distribuzione di dispositivi virtuali con disponibilità elevata](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


