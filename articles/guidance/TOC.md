# Architettura

## Concetti fondamentali sul cloud

### [Progettazione di applicazioni resilienti per Azure](guidance-resiliency-overview.md)
#### [Elenco di controllo per la resilienza](guidance-resiliency-checklist.md)
#### [Analisi della modalità di errore](guidance-resiliency-failure-mode-analysis.md)

#### [Ripristino di emergenza per le applicazioni basate su Microsoft Azure](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Disponibilità elevata per le applicazioni basate su Microsoft Azure](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Elenco di controllo per la disponibilità elevata](..\resiliency\resiliency-high-availability-checklist.md)
#### [Indicazioni sulla resilienza per servizi specifici di Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Ripristino dal danneggiamento o dall'eliminazione accidentale dei dati](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Ripristino da errori locali in Azure](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Ripristino dopo un'interruzione di servizio a livello di area](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Ripristino dall'ambiente locale ad Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Indicazioni tecniche sulla resilienza di Azure](..\resiliency\resiliency-technical-guidance.md)


## Architetture di riferimento

### Architettura di riferimento per il calcolo
#### [Esecuzione di una VM Linux in Azure](guidance-compute-single-vm-linux.md)
#### [Esecuzione di una VM Windows in Azure](guidance-compute-single-vm.md)
#### [Esecuzione di più macchine virtuali in Azure per la scalabilità e la disponibilità](guidance-compute-multi-vm.md)
#### [Esecuzione di macchine virtuali per un'architettura a più livelli in Azure](guidance-compute-n-tier-vm-linux.md)
#### [Esecuzione di macchine virtuali Windows per un'architettura a più livelli in Azure](guidance-compute-n-tier-vm.md)
#### [Esecuzione di macchine virtuali Linux in più aree per una disponibilità elevata](guidance-compute-multiple-datacenters-linux.md)
#### [Esecuzione di macchine virtuali Windows in più aree per una disponibilità elevata](guidance-compute-multiple-datacenters.md)

### [Connessione della rete locale ad Azure](guidance-connecting-your-on-premises-network-to-azure.md)
#### [Implementazione di un'architettura di rete ibrida con Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Implementazione di un'architettura di rete ibrida con Azure e la rete VPN locale](guidance-hybrid-network-vpn.md)
#### [Implementazione di un'architettura di rete ibrida a disponibilità elevata](guidance-hybrid-network-expressroute-vpn-failover.md)

### Protezione del limite cloud in Azure
#### [Implementazione di un'architettura di rete ibrida sicura in Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Implementazione di una rete perimetrale tra Azure e Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Gestione delle identità in Azure](guidance-ra-identity.md)
#### [Implementazione di Azure Active Directory](guidance-identity-aad.md)
#### [Estensione di Active Directory Directory Services (ADDS) in Azure](guidance-identity-adds-extend-domain.md)
#### [Creazione di una foresta di risorse di Active Directory Directory Services (ADDS) in Azure](guidance-identity-adds-resource-forest.md)
#### [Implementazione di Active Directory Federation Services (AD FS) in Azure](guidance-identity-adfs.md)

### Architettura di riferimento per le applicazioni Web PaaS
#### [Architettura di riferimento per Azure: applicazione Web di base](guidance-web-apps-basic.md)
#### [Architettura di riferimento per Azure: applicazione Web con disponibilità elevata](guidance-web-apps-multi-region.md)
#### [Miglioramento della scalabilità in un'applicazione Web](guidance-web-apps-scalability.md)


## Modelli di progettazione cloud

## Procedure consigliate per le applicazioni cloud

### [Linee guida per la progettazione di un'API](..\best-practices-api-design.md)
### [Guida all'implementazione di API](..\best-practices-api-implementation.md)
### [Indicazioni sulla scalabilità automatica](..\best-practices-auto-scaling.md)
### [Elenco di controllo della disponibilità](..\best-practices-availability-checklist.md)
### [Linee guida per i processi in background](..\best-practices-background-jobs.md)
### [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](..\best-practices-availability-paired-regions.md)
### [Informazioni aggiuntive sulla memorizzazione nella cache](..\best-practices-caching.md)
### [Indicazioni sulla rete per la distribuzione di contenuti (CDN)](..\best-practices-cdn.md)
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
#### [Esecuzione di Elasticsearch in Azure](guidance-elasticsearch-running-on-azure.md)
#### [Ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query con Elasticsearch in Azure](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configurazione delle opzioni di resilienza e ripristino di Elasticsearch in Azure](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Creazione di un ambiente di test delle prestazioni per Elasticsearch in Azure](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementazione di un piano di test di JMeter per Elasticsearch](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Esecuzione di test automatizzati della resilienza in Elasticsearch](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Esecuzione dei test automatizzati delle prestazioni di Elasticsearch](guidance-elasticsearch-running-automated-performance-tests.md)

### [Gestione delle identità per le applicazioni multi-tenant in Microsoft Azure](guidance-multitenant-identity.md)
#### [Introduzione alla gestione delle identità per applicazioni multi-tenant in Microsoft Azure](guidance-multitenant-identity-intro.md)
#### [Informazioni sull'applicazione Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Autenticazione nelle app multi-tenant con Azure AD e OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Utilizzo delle identità basate sulle attestazioni nelle applicazioni multi-tenant](guidance-multitenant-identity-claims.md)
#### [Iscrizione e onboarding del tenant in un'applicazione multi-tenant](guidance-multitenant-identity-signup.md)
#### [Ruoli dell'applicazione nelle applicazioni multi-tenant](guidance-multitenant-identity-app-roles.md)
#### [Autorizzazione basata sui ruoli e sulle risorse nelle applicazioni multi-tenant](guidance-multitenant-identity-authorize.md)
#### [Protezione di un'API Web back-end in un'applicazione multi-tenant](guidance-multitenant-identity-web-api.md)
#### [Memorizzazione nella cache dei token di accesso in un'applicazione multi-tenant](guidance-multitenant-identity-token-cache.md)
#### [Federazione con il servizio AD FS di un cliente per app multi-tenant in Azure](guidance-multitenant-identity-adfs.md)
#### [Uso di asserzioni client per ottenere token di accesso da Azure AD](guidance-multitenant-identity-client-assertion.md)
#### [Uso dell'insieme di credenziali delle chiavi di Azure per proteggere i segreti dell'applicazione](guidance-multitenant-identity-keyvault.md)

#### [Distribuzione di dispositivi virtuali con disponibilità elevata](guidance-nva-ha.md)


<!--HONumber=Nov16_HO2-->


