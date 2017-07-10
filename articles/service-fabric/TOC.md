
# Panoramica
## [Che cos'è Service Fabric?](service-fabric-overview.md)
## [Informazioni sui microservizi](service-fabric-overview-microservices.md)
## [Quadro generale](service-fabric-content-roadmap.md)
## [Scenari applicativi](service-fabric-application-scenarios.md)
## [Modelli e scenari](service-fabric-patterns-and-scenarios.md)
## [Architettura](service-fabric-architecture.md)
## [Terminologia](service-fabric-technical-overview.md)

# Introduzione
## Configurazione dell'ambiente di sviluppo
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Creare la prima applicazione
### [C# su Windows](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java su Linux](service-fabric-create-your-first-linux-application-with-java.md)
### [C# su Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Distribuire app in un cluster locale](service-fabric-get-started-with-a-local-cluster.md)
## [Distribuire app .NET in un contenitore](service-fabric-host-app-in-a-container.md)
## [Creare il primo cluster in Azure](service-fabric-get-started-azure-cluster.md)
## [Creare il primo cluster autonomo](service-fabric-get-started-standalone-cluster.md)
## [Creare la prima app contenitore](service-fabric-get-started-containers.md)

# Procedure
## Creare un'applicazione
  
### Concetti
#### [Modelli di programmazione supportati](service-fabric-choose-framework.md)
#### [Modello di applicazione](service-fabric-application-model.md)
#### [Modello di hosting](service-fabric-hosting-model.md)
#### [Risorse del manifesto del servizio](service-fabric-service-manifest-resources.md)
#### [Stato del servizio](service-fabric-concepts-state.md)
#### [Partizionamento del servizio](service-fabric-concepts-partitioning.md)
#### [Disponibilità dei servizi](service-fabric-availability-services.md)
#### [Scalabilità delle applicazioni](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Pianificare la capacità dell'app](service-fabric-capacity-planning.md)

### Creare un servizio eseguibile guest
#### [Distribuire un eseguibile guest](service-fabric-deploy-existing-app.md)
#### [Distribuire più eseguibili guest](service-fabric-deploy-multiple-apps.md)

### Creare un servizio contenitore
#### [Panoramica](service-fabric-containers-overview.md)
#### [Distribuire un contenitore Windows](service-fabric-deploy-container.md)
#### [Distribuire un contenitore Linux](service-fabric-deploy-container-linux.md)
#### [Docker Compose (anteprima)](service-fabric-docker-compose.md)
#### [Governance delle risorse per contenitori e servizi](service-fabric-resource-governance.md)
#### [Driver di volume e registrazione](service-fabric-containers-volume-logging-drivers.md)

### Creare un servizio di Reliable Services
#### [Panoramica](service-fabric-reliable-services-introduction.md)
#### Concetti
##### [Ciclo di vita di Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)
##### [Ciclo di vita di Reliable Services: Java](service-fabric-reliable-services-lifecycle-java.md)

#### Reliable Collections
##### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
##### [Linee guida e indicazioni per Reliable Collections](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Lavorare con le raccolte Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transazioni e blocchi](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Coda simultanea affidabile](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Serializzazione di raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Elementi interni di Reliable Collections e Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md)

#### Introduzione
##### [C# su Windows](service-fabric-reliable-services-quick-start.md)
##### [Java su Linux](service-fabric-reliable-services-quick-start-java.md)

#### [Configurare](service-fabric-reliable-services-configuration.md)
#### [Inviare notifiche](service-fabric-reliable-services-notifications.md)
#### [Backup e ripristino](service-fabric-reliable-services-backup-restore.md)

#### Comunicare con i servizi
##### [Comunicare con Reliable Services](service-fabric-reliable-services-communication.md)
##### [Servizio remoto: C#](service-fabric-reliable-services-communication-remoting.md)
##### [Servizio remoto: Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Comunicazioni sicure - C#](service-fabric-reliable-services-secure-communication.md)
##### [Comunicazioni sicure - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Uso avanzato](service-fabric-reliable-services-advanced-usage.md)

### Creare un servizio di Reliable Actors
#### [Panoramica](service-fabric-reliable-actors-introduction.md)
#### Concetti
##### [Architettura](service-fabric-reliable-actors-platform.md)
##### [Ciclo di vita e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
##### [Gestione dello stato](service-fabric-reliable-actors-state-management.md)
##### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
##### [Rientranza](service-fabric-reliable-actors-reentrancy.md)
##### [Serializzazione dei tipi](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### Introduzione
##### [C# su Windows](service-fabric-reliable-actors-get-started.md)
##### [Java su Linux](service-fabric-reliable-actors-get-started-java.md)

#### [Inviare notifiche](service-fabric-reliable-actors-events.md) 
#### [Configurare timer e promemoria](service-fabric-reliable-actors-timers-reminders.md)
#### [Configurare KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Configurare le impostazioni delle comunicazioni](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [Configurare ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### Comunicare con i servizi
#### [Modello di comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md)
#### [Servizio DNS](service-fabric-dnsservice.md)
#### [Proxy inverso](service-fabric-reverseproxy.md)
#### [Configurare il proxy inverso per le comunicazioni sicure](service-fabric-reverseproxy-configure-secure-communication.md)

### [Aggiungere un front-end Web](service-fabric-add-a-web-frontend.md)

### Lavorare in un IDE
#### [Introduzione al plug-in Eclipse per lo sviluppo Java](service-fabric-get-started-eclipse.md)
#### [Gestire app in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Configurare connessioni sicure in Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### [Configurare l'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md)

### Configurare la sicurezza
#### [Gestire i segreti dell'applicazione](service-fabric-application-secret-management.md)  
#### [Configurare i criteri di sicurezza per l'applicazione](service-fabric-application-runas-security.md)

### Debug
#### [Eseguire il debug di un servizio C# in VS](service-fabric-debugging-your-application.md)
#### [Eseguire il debug di un servizio Java in Eclipse](service-fabric-debugging-your-application-java.md)
#### [Errori ed eccezioni comuni](service-fabric-errors-and-exceptions.md)

### Monitorare e diagnosticare in locale
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### Eseguire l'integrazione con Gestione API
#### [Panoramica](service-fabric-api-management-overview.md)
#### [Avvio rapido](service-fabric-api-management-quick-start.md)

### Eseguire la migrazione da Servizi cloud
#### [Confrontare Servizi cloud e Service Fabric](service-fabric-cloud-services-migration-differences.md)
#### [Eseguire la migrazione a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [Procedure consigliate](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Gestire il ciclo di vita delle applicazioni
### [Panoramica](service-fabric-application-lifecycle.md)
### [Inserire un'applicazione in un pacchetto](service-fabric-package-apps.md)
### [Informazioni sull'impostazione ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Distribuire o rimuovere applicazioni
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Interfaccia della riga di comando di Azure 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [API client Fabric](service-fabric-deploy-remove-applications-fabricclient.md)
### Aggiornare un'applicazione
#### [Panoramica](service-fabric-application-upgrade.md)
#### [Configurare l'aggiornamento di un'applicazione](service-fabric-visualstudio-configure-upgrade.md)
#### [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md)
#### Aggiornamento
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Risolvere i problemi relativi agli aggiornamenti delle applicazioni](service-fabric-application-upgrade-troubleshooting.md)
#### [Serializzazione dei dati negli aggiornamenti delle applicazioni](service-fabric-application-upgrade-data-serialization.md)
#### [Argomenti avanzati sugli aggiornamenti delle applicazioni](service-fabric-application-upgrade-advanced.md)

### Testare applicazioni e servizi
#### [Panoramica dell'analisi degli errori](service-fabric-testability-overview.md)
#### [Testare la comunicazione tra servizi](service-fabric-testability-scenarios-service-communication.md)
#### Simulare gli errori
##### [Uso di Chaos controllato](service-fabric-controlled-chaos.md)
##### [Uso di azioni di test](service-fabric-testability-actions.md)
##### [Durante i carichi di lavoro](service-fabric-testability-workload-tests.md)
##### [Uso di scenari di test](service-fabric-testability-scenarios.md)
##### [Uso delle API di transizione del nodo](service-fabric-node-transition-apis.md)
#### [Eseguire test di carico dell'applicazione](service-fabric-vso-load-test.md)

### Configurare l'integrazione continua
#### [Configurare l'integrazione continua con VSTS](service-fabric-set-up-continuous-integration.md)
#### [Distribuire app Java Linux con Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## Creare e gestire i cluster

### [Panoramica](service-fabric-deploy-anywhere.md)
### Concetti
#### [Descrivere un cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Sicurezza del cluster](service-fabric-cluster-security.md)
#### [Differenze in termini di funzionalità tra Linux e Windows](service-fabric-linux-windows-differences.md)

### Pianificazione e preparazione
#### [Pianificazione della capacità](service-fabric-cluster-capacity.md)
#### [Ripristino di emergenza](service-fabric-disaster-recovery.md)

### Cluster in Azure
#### Concetti
##### [Tipi di nodo e set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md)
##### [Modelli di rete di cluster](service-fabric-patterns-networking.md)
#### Create 
##### [Portale di Azure](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio e Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### Scalabilità 
##### [Manualmente](service-fabric-cluster-scale-up-down.md)
##### [A livello di codice](service-fabric-cluster-programmatic-scaling.md)
#### [Aggiornamento](service-fabric-cluster-upgrade.md)
#### [Configurare il controllo di accesso](service-fabric-cluster-security-roles.md)
#### [Configurare](service-fabric-cluster-fabric-settings.md)
#### [Gestire i certificati cluster](service-fabric-cluster-security-update-certs-azure.md) 
#### [Eliminazione](service-fabric-cluster-delete.md)

### Cluster autonomi
#### [Pianificare e preparare la distribuzione](service-fabric-cluster-standalone-deployment-preparation.md)
#### Create
##### [Creare in locale](service-fabric-cluster-creation-for-windows-server.md)
##### [Creare nelle macchine virtuali di Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [Proteggere tramite i certificati](service-fabric-windows-cluster-x509-security.md)  
##### [Proteggere con la protezione di Windows](service-fabric-windows-cluster-windows-security.md)
##### [Contenuti del pacchetto autonomo](service-fabric-cluster-standalone-package-contents.md)
#### [Ridimensionare](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Configurare il controllo di accesso](service-fabric-cluster-security-roles.md)
#### [Configurare](service-fabric-cluster-manifest.md)
#### [Aggiornamento](service-fabric-cluster-upgrade-windows-server.md) 

### [Visualizzare un cluster](service-fabric-visualizing-your-cluster.md)
### [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md)

### [Gestire un cluster con l'interfaccia della riga di comando di XPlat](service-fabric-azure-cli.md)
### [Gestire un cluster con i comandi dell'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)
### [Applicare patch ai nodi del cluster](service-fabric-patch-orchestration-application.md)

### Gestire e orchestrare le risorse cluster
#### [Panoramica di Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
#### [Architettura di Cluster Resource Manager](service-fabric-cluster-resource-manager-architecture.md)
#### [Descrivere un cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Panoramica dei gruppi di applicazioni](service-fabric-cluster-resource-manager-application-groups.md)
#### [Configurare le impostazioni di Cluster Resource Manager](service-fabric-cluster-resource-manager-configure-services.md)
#### [Metriche di consumo delle risorse](service-fabric-cluster-resource-manager-metrics.md)
#### [Usare l'affinità dei servizi](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Criteri di posizionamento dei servizi](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Gestire un cluster](service-fabric-cluster-resource-manager-management-integration.md)
#### [Deframmentazione dei cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Bilanciare un cluster](service-fabric-cluster-resource-manager-balancing.md)
#### [Limitazione](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Spostamento dei servizi](service-fabric-cluster-resource-manager-movement-cost.md)

## Monitorare e diagnosticare
### [Monitorare e diagnosticare applicazioni](service-fabric-diagnostics-overview.md)
### Generare eventi
#### [Generare eventi a livello di infrastruttura](service-fabric-diagnostics-event-generation-infra.md)
##### [Eventi di Reliable Services](service-fabric-reliable-services-diagnostics.md)
##### [Eventi di Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
#### [Generare eventi a livello di applicazione](service-fabric-diagnostics-event-generation-app.md)
### Ispezionare l'integrità di applicazioni e cluster
#### [Monitorare l'integrità di Service Fabric](service-fabric-health-introduction.md)
#### [Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [Aggiungere report sull'integrità personalizzati](service-fabric-report-health.md)
#### [Risolvere i problemi relativi ai report sull'integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [Visualizzare i report sull'integrità](service-fabric-view-entities-aggregated-health.md)
### Aggregare eventi
#### [Aggregare eventi con EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Aggregare eventi con Diagnostica di Azure
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### Analizzare gli eventi
#### [Analizzare gli eventi con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [Analizzare gli eventi con OMS](service-fabric-diagnostics-event-analysis-oms.md)
### [Risolvere i problemi del cluster locale](service-fabric-troubleshoot-local-cluster-setup.md)

# Riferimento
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Interfaccia della riga di comando di Azure](/cli/azure/sf)
## [API Java](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/servicefabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/)
## [Domande frequenti su Service Fabric](service-fabric-common-questions.md)
## [Percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Prezzi](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Codice di esempio](http://aka.ms/servicefabricsamples)
## [Opzioni di supporto di Service Fabric](service-fabric-support.md)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=service-fabric)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
