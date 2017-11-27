# [Documentazione di Service Fabric](/azure/service-fabric)
# Panoramica
## [Che cos'è Service Fabric?](service-fabric-overview.md)

# Guide introduttive
## [Creare un'applicazione .NET](service-fabric-quickstart-dotnet.md)
## [Distribuire un'applicazione contenitore Linux](service-fabric-quickstart-containers-linux.md)
## [Distribuire un'applicazione contenitore Windows](service-fabric-quickstart-containers.md)
## [Distribuire un'applicazione Java](service-fabric-quickstart-java.md)

# Esercitazioni
## Distribuire un'app .NET
### [1- Compilare un'applicazione .NET](service-fabric-tutorial-create-dotnet-app.md)
### [2- Distribuire l'applicazione](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3- Configurare CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4- Monitorare e diagnosticare](service-fabric-tutorial-monitoring-aspnet.md)

## Distribuire un'app .NET esistente in un contenitore
### [1- Distribuire un'app .NET con Docker Compose](service-fabric-host-app-in-a-container.md)
### [2 - Monitorare il contenitore](service-fabric-tutorial-monitoring-wincontainers.md)

## Creare un'app contenitore Linux
### [1- Creare immagini del contenitore](service-fabric-tutorial-create-container-images.md)
### [2 - Pacchetto e contenitori di distribuzione](service-fabric-tutorial-package-containers.md)
### [3 - Failover e scalabilità](service-fabric-tutorial-containers-failover.md)

## Creare e gestire un cluster
### 1- Creare un cluster in Azure
#### [1a- Creare un cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1b- Creare un cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2- Ridimensionare il cluster](service-fabric-tutorial-scale-cluster.md)
### [3- Distribuire Gestione API e Service Fabric](service-fabric-tutorial-deploy-api-management.md)


# Esempi
## [Esempi di codice](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [Interfaccia della riga di comando di Service Fabric](samples-cli.md)

# Concetti
## [Informazioni sui microservizi](service-fabric-overview-microservices.md)
## [Quadro generale](service-fabric-content-roadmap.md)
## [Scenari applicativi](service-fabric-application-scenarios.md)
## [Modelli e scenari](service-fabric-patterns-and-scenarios.md)
## [Architettura](service-fabric-architecture.md)
## [Terminologia](service-fabric-technical-overview.md)

## Creare applicazioni e servizi
### Modelli di programmazione supportati
#### [Panoramica](service-fabric-choose-framework.md)
#### Contenitori
##### [Panoramica](service-fabric-containers-overview.md)
##### [Docker Compose (anteprima)](service-fabric-docker-compose.md)
##### [Governance delle risorse](service-fabric-resource-governance.md)
#### Reliable Services
##### [Panoramica](service-fabric-reliable-services-introduction.md)
##### [Ciclo di vita di Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)
##### [Ciclo di vita di Reliable Services: Java](service-fabric-reliable-services-lifecycle-java.md)
##### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
##### [Linee guida e indicazioni per Reliable Collections](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Lavorare con le raccolte Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transazioni e blocchi](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Coda simultanea affidabile](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Serializzazione di raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Elementi interni di Reliable Collections e Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md)
##### [Uso avanzato](service-fabric-reliable-services-advanced-usage.md)

#### Reliable Actors
##### [Panoramica](service-fabric-reliable-actors-introduction.md)
##### [Architettura](service-fabric-reliable-actors-platform.md)
##### [Ciclo di vita e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
##### [Gestione dello stato](service-fabric-reliable-actors-state-management.md)
##### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
##### [Rientranza](service-fabric-reliable-actors-reentrancy.md)
##### [Serializzazione dei tipi](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### [Modello di applicazione](service-fabric-application-model.md)
### [Modello di hosting](service-fabric-hosting-model.md)

### Services
#### [Risorse del servizio](service-fabric-service-manifest-resources.md)
#### [Stato del servizio](service-fabric-concepts-state.md)
#### [Partizionamento del servizio](service-fabric-concepts-partitioning.md)
#### [Disponibilità dei servizi](service-fabric-availability-services.md)
#### [Istanze e repliche](service-fabric-concepts-replica-lifecycle.md)
#### [Riconfigurazione](service-fabric-concepts-reconfiguration.md)
#### Comunicazione con i servizi
##### [Panoramica](service-fabric-connect-and-communicate-with-services.md)
##### [Servizio DNS](service-fabric-dnsservice.md)
##### [Proxy inverso](service-fabric-reverseproxy.md)
##### [Configurare il proxy inverso per le comunicazioni sicure](service-fabric-reverseproxy-configure-secure-communication.md)
##### [Diagnostica del proxy inverso](service-fabric-reverse-proxy-diagnostics.md)
### [Scalabilità delle applicazioni](service-fabric-concepts-scalability.md)
### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Pianificare la capacità dell'applicazione](service-fabric-capacity-planning.md)

## Gestire le applicazioni
### [Panoramica](service-fabric-application-lifecycle.md)
### [Impostazione ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Aggiornamento dell'applicazione
#### [Panoramica](service-fabric-application-upgrade.md)
#### [Configurazione](service-fabric-visualstudio-configure-upgrade.md)
#### [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md)
#### [Serializzazione dei dati negli aggiornamenti delle applicazioni](service-fabric-application-upgrade-data-serialization.md)
#### [Argomenti avanzati sugli aggiornamenti delle applicazioni](service-fabric-application-upgrade-advanced.md)
### [Panoramica dell'analisi degli errori](service-fabric-testability-overview.md)

## Creare e gestire i cluster
### [Panoramica](service-fabric-deploy-anywhere.md)
### Pianificazione e preparazione
#### [Pianificazione della capacità](service-fabric-cluster-capacity.md)
#### [Ripristino di emergenza](service-fabric-disaster-recovery.md)
### [Descrizione di un cluster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Sicurezza del cluster](service-fabric-cluster-security.md)
### [Differenze in termini di funzionalità tra Linux e Windows](service-fabric-linux-windows-differences.md)
### Cluster in Azure
#### [Tipi di nodo e set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md)
#### [Modelli di rete di cluster](service-fabric-patterns-networking.md)
### Gestione risorse cluster
#### [Panoramica](service-fabric-cluster-resource-manager-introduction.md)
#### [Architettura](service-fabric-cluster-resource-manager-architecture.md)
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

## [Eseguire l'integrazione con Gestione API](service-fabric-api-management-overview.md)

## Monitorare e diagnosticare
### [Monitorare e diagnosticare applicazioni](service-fabric-diagnostics-overview.md)
### Generare eventi
#### [Generare eventi a livello di piattaforma](service-fabric-diagnostics-event-generation-infra.md)
##### [Canale operativo](service-fabric-diagnostics-event-generation-operational.md)
##### [Eventi di Reliable Services](service-fabric-reliable-services-diagnostics.md)
##### [Eventi di Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
##### [Metriche delle prestazioni](service-fabric-diagnostics-event-generation-perf.md)
##### [Monitoraggio della comunicazione remota con i servizi](service-fabric-reliable-serviceremoting-diagnostics.md)
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

# Guide alle procedure
## Configurazione dell'ambiente di sviluppo
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)

## [Configurare l'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)

## Creare un'applicazione
### [Creare la prima app C# in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
### Creare un servizio eseguibile guest
#### [Ospitare un'applicazione Node.js in Windows](quickstart-guest-app.md)
#### [Distribuire un eseguibile guest](service-fabric-deploy-existing-app.md)
#### [Distribuire più eseguibili guest](service-fabric-deploy-multiple-apps.md)
### Creare un servizio contenitore
#### [Creare un'applicazione contenitore Windows](service-fabric-get-started-containers.md)
#### [Creare un'applicazione contenitore Linux](service-fabric-get-started-containers-linux.md)
#### [Sicurezza del contenitore](service-fabric-securing-containers.md)
#### [Docker Compose (anteprima)](service-fabric-docker-compose.md)
#### [Governance delle risorse per contenitori e servizi](service-fabric-resource-governance.md)
#### [Driver di volume e registrazione](service-fabric-containers-volume-logging-drivers.md)
#### [Servizi nei contenitori](service-fabric-services-inside-containers.md)
#### [Modalità di rete del contenitore](service-fabric-networking-modes.md)

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
##### [Creare un'applicazione C# in Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
#### Comunicare con i servizi
##### [Comunicare con Reliable Services](service-fabric-reliable-services-communication.md)

##### [Servizio remoto: C#](service-fabric-reliable-services-communication-remoting.md)
##### [Servizio remoto: Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Comunicazioni sicure - C#](service-fabric-reliable-services-secure-communication.md)
##### [Comunicazioni sicure - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Configurare](service-fabric-reliable-services-configuration.md)
#### [Inviare notifiche](service-fabric-reliable-services-notifications.md)
#### [Backup e ripristino](service-fabric-reliable-services-backup-restore.md)

### Creare un servizio di Reliable Actors
#### Attività iniziali
##### [C# su Windows](service-fabric-reliable-actors-get-started.md)
##### [Attore Java in Linux](service-fabric-create-your-first-linux-application-with-java.md)
#### [Inviare notifiche](service-fabric-reliable-actors-events.md)
#### [Configurare timer e promemoria](service-fabric-reliable-actors-timers-reminders.md)
#### [Configurare KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Configurare le impostazioni delle comunicazioni](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [Configurare ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Eseguire la migrazione di applicazioni Java precedenti per il supporto di Maven](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [Configurare il proxy inverso per le comunicazioni sicure](service-fabric-reverseproxy-configure-secure-communication.md)

### [Creare un servizio ASP.NET Core](service-fabric-add-a-web-frontend.md)

### Configurare la sicurezza
#### [Gestire i segreti dell'applicazione](service-fabric-application-secret-management.md)  
#### [Configurare i criteri di sicurezza per l'applicazione](service-fabric-application-runas-security.md)

## Usare un ambiente di sviluppo Windows
### [Gestire le applicazioni in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
### [Configurare connessioni sicure in Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
### [Configurare l'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md)
### [Eseguire il debug di un servizio .NET in Visual Studio](service-fabric-debugging-your-application.md)
### [Errori ed eccezioni comuni](service-fabric-errors-and-exceptions.md)
### [Monitorare e diagnosticare in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
### [Configurare un cluster Linux in Windows](service-fabric-local-linux-cluster-windows.md)

## Usare un ambiente di sviluppo Linux
### [Introduzione al plug-in Eclipse per lo sviluppo Java](service-fabric-get-started-eclipse.md)
### [Eseguire il debug di un servizio Java in Eclipse](service-fabric-debugging-your-application-java.md)
### [Monitorare e diagnosticare in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## Eseguire la migrazione da Servizi cloud
### [Confrontare Servizi cloud e Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Eseguire la migrazione a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Procedure consigliate](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Gestire il ciclo di vita delle applicazioni
### [Inserire un'applicazione in un pacchetto](service-fabric-package-apps.md)

### Distribuire o rimuovere applicazioni
#### [Distribuire applicazioni in un cluster locale](service-fabric-get-started-with-a-local-cluster.md)
#### [Gestione risorse di Azure](service-fabric-application-arm-resource.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [Interfaccia della riga di comando di Service Fabric](service-fabric-application-lifecycle-sfctl.md)
#### [API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

### Aggiornare le applicazioni
#### [Aggiornamento con Azure PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Eseguire l'aggiornamento usando Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Risolvere i problemi relativi agli aggiornamenti delle applicazioni](service-fabric-application-upgrade-troubleshooting.md)

### Testare applicazioni e servizi
#### [Testare la comunicazione tra servizi](service-fabric-testability-scenarios-service-communication.md)
#### Simulare gli errori
##### [Uso di Chaos controllato](service-fabric-controlled-chaos.md)
##### [Uso di azioni di test](service-fabric-testability-actions.md)
##### [Durante i carichi di lavoro](service-fabric-testability-workload-tests.md)
##### [Uso di scenari di test](service-fabric-testability-scenarios.md)
##### [Uso delle API di transizione del nodo](service-fabric-node-transition-apis.md)

### Configurare l'integrazione continua
#### [Configurare l'integrazione continua con VSTS](service-fabric-set-up-continuous-integration.md)
#### [Distribuire applicazioni Linux usando Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## Creare e gestire i cluster
### Cluster in Azure
#### Create
##### [Portale di Azure](service-fabric-cluster-creation-via-portal.md)
##### [Gestione risorse di Azure](service-fabric-cluster-creation-via-arm.md)
#### Scalabilità
##### [Manualmente](service-fabric-cluster-scale-up-down.md)
##### [A livello di codice](service-fabric-cluster-programmatic-scaling.md)
#### [Aggiornamento](service-fabric-cluster-upgrade.md)
#### [Configurare il controllo di accesso](service-fabric-cluster-security-roles.md)
#### [Configurare](service-fabric-cluster-fabric-settings.md)
#### [Aprire una porta nel servizio di bilanciamento del carico](create-load-balancer-rule.md)
#### [Gestire i certificati cluster](service-fabric-cluster-security-update-certs-azure.md)
#### [Eliminazione](service-fabric-cluster-delete.md)

### Cluster autonomi
#### [Pianificare e preparare la distribuzione](service-fabric-cluster-standalone-deployment-preparation.md)
#### Create
##### [Creare il primo cluster autonomo](service-fabric-get-started-standalone-cluster.md)
##### [Creare in locale](service-fabric-cluster-creation-for-windows-server.md)
##### [Proteggere tramite i certificati](service-fabric-windows-cluster-x509-security.md)  
##### [Proteggere con la protezione di Windows](service-fabric-windows-cluster-windows-security.md)
##### [Contenuti del pacchetto autonomo](service-fabric-cluster-standalone-package-contents.md)
#### [Ridimensionare](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Configurare il controllo di accesso](service-fabric-cluster-security-roles.md)
#### [Configurare](service-fabric-cluster-manifest.md)
#### [Aggiornamento](service-fabric-cluster-upgrade-windows-server.md)

### [Visualizzare un cluster](service-fabric-visualizing-your-cluster.md)
### [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md)
### [Applicare patch ai nodi del cluster](service-fabric-patch-orchestration-application.md)

## Monitorare e diagnosticare
### OMS
#### [Configurare OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)
#### [Aggiungere l'agente OMS](service-fabric-diagnostics-oms-agent.md)
#### [Monitorare i contenitori](service-fabric-diagnostics-oms-containers.md)
### Monitoraggio delle prestazioni
#### [Monitoraggio delle prestazioni con Diagnostica di Microsoft Azure](service-fabric-diagnostics-perf-wad.md)

# riferimento
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Interfaccia della riga di comando di Azure (az sf)](/cli/azure/sf)
## [Interfaccia della riga di comando di Service Fabric (sfctl)](service-fabric-sfctl.md)
### [sfctl application](service-fabric-sfctl-application.md)
### [sfctl chaos](service-fabric-sfctl-chaos.md)
### [sfctl cluster](service-fabric-sfctl-cluster.md)
### [sfctl compose](service-fabric-sfctl-compose.md)
### [sfctl is](service-fabric-sfctl-is.md)
### [sfctl node](service-fabric-sfctl-node.md)
### [sfctl partition](service-fabric-sfctl-partition.md)
### [sfctl replica](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl service](service-fabric-sfctl-service.md)
### [sfctl store](service-fabric-sfctl-store.md)
## [API Java](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)
## [Schema XML del modello del servizio](service-fabric-service-model-schema.md)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/)
## [Domande frequenti](service-fabric-common-questions.md)
## [Percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Prezzi](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Codice di esempio](http://aka.ms/servicefabricsamples)
## [Opzioni di supporto](service-fabric-support.md)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=service-fabric)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)