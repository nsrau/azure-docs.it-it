# Panoramica
## [Che cos'è Service Fabric?](service-fabric-overview.md)
## [Informazioni sui microservizi](service-fabric-overview-microservices.md)
## [Scenari applicativi](service-fabric-application-scenarios.md)
## [Architettura](service-fabric-architecture.md)
## [Terminologia](service-fabric-technical-overview.md)
## [Guida di orientamento al contenuto](service-fabric-content-roadmap.md)

# Attività iniziali
## Configurazione dell'ambiente di sviluppo
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Creare la prima applicazione
### [C# su Windows](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java su Linux](service-fabric-create-your-first-linux-application-with-java.md)
### [C# su Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Distribuire app in un cluster locale](service-fabric-get-started-with-a-local-cluster.md)

# Procedure
## Creare un'applicazione
### [Modelli e scenari](service-fabric-patterns-and-scenarios.md)
### Nozioni di base
#### [Modello di programmazione](service-fabric-choose-framework.md)
#### [Modello di applicazione](service-fabric-application-model.md)
#### [Modello di comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md)
#### [Risorse del manifesto del servizio](service-fabric-service-manifest-resources.md)
#### [Strumenti](service-fabric-manage-application-in-visual-studio.md)
#### Debug
##### [Eseguire il debug di un servizio C# in VS](service-fabric-debugging-your-application.md)
##### [Eseguire il debug di un servizio Java in Eclipse](service-fabric-debugging-your-application-java.md)
#### Monitorare e diagnosticare
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Gestire i segreti dell'applicazione](service-fabric-application-secret-management.md)  
#### [Configurare i criteri di sicurezza per l'applicazione](service-fabric-application-runas-security.md)  
#### [Configurare l'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md)  
#### [Errori ed eccezioni comuni](service-fabric-errors-and-exceptions.md) 

### Applicazione Reliable Services
#### [Panoramica](service-fabric-reliable-services-introduction.md)
#### Attività iniziali
##### [C# su Windows](service-fabric-reliable-services-quick-start.md)
##### [Java su Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Architettura](service-fabric-reliable-services-platform-architecture.md)
#### [Ciclo di vita di Reliable Services](service-fabric-reliable-services-lifecycle.md)
#### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
#### [Usare Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [Configurare](service-fabric-reliable-services-configuration.md)
#### [Notifiche](service-fabric-reliable-services-notifications.md)
#### [Backup e ripristino](service-fabric-reliable-services-backup-restore.md)
#### [Comunicare con Reliable Services](service-fabric-reliable-services-communication.md)
#### [Proteggere le comunicazioni con Reliable Services](service-fabric-reliable-services-secure-communication.md)
##### [ASP.NET](service-fabric-reliable-services-communication-webapi.md)
##### [Servizio remoto](service-fabric-reliable-services-communication-remoting.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Proxy inverso](service-fabric-reverseproxy.md)
#### [Uso avanzato](service-fabric-reliable-services-advanced-usage.md)

### Applicazione Reliable Actors
#### [Panoramica](service-fabric-reliable-actors-introduction.md)
#### Attività iniziali
##### [C# su Windows](service-fabric-reliable-actors-get-started.md)
##### [Java su Linux](service-fabric-reliable-actors-get-started-java.md)
#### [Architettura](service-fabric-reliable-actors-platform.md)
#### [Ciclo di vita e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
#### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
#### [Rientranza](service-fabric-reliable-actors-reentrancy.md)
#### [Timer e promemoria](service-fabric-reliable-actors-timers-reminders.md)
#### [Eventi](service-fabric-reliable-actors-events.md)
#### [Gestione dello stato](service-fabric-reliable-actors-state-management.md)
#### [Configurare il provider di stato](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Serializzazione dei tipi](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
#### [Configurare le impostazioni delle comunicazioni](service-fabric-reliable-actors-fabrictransportsettings.md) 

### Applicazione eseguibile guest
#### [Distribuire un eseguibile guest](service-fabric-deploy-existing-app.md)
#### [Distribuire più eseguibili guest](service-fabric-deploy-multiple-apps.md)

### Applicazione contenitore
#### [Panoramica](service-fabric-containers-overview.md)
#### [Distribuire un contenitore Windows](service-fabric-deploy-container.md)
#### [Distribuire un contenitore Docker](service-fabric-deploy-container-linux.md)

## Eseguire la migrazione da Servizi cloud
### [Confrontare Servizi cloud e Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Eseguire la migrazione a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

## Creare e gestire i cluster

### Nozioni di base
#### [Panoramica](service-fabric-deploy-anywhere.md)
#### [Descrivere un cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Pianificazione della capacità](service-fabric-cluster-capacity.md)
#### [Visualizzare un cluster](service-fabric-visualizing-your-cluster.md)
#### [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md)
#### [Gestire un cluster con l'interfaccia della riga di comando di Azure](service-fabric-azure-cli.md) 
#### [Sicurezza](service-fabric-cluster-security.md)
#### [Ripristino di emergenza](service-fabric-disaster-recovery.md)

### Cluster in Azure
#### Creare un cluster in Azure
##### [Portale di Azure](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### [Tipi di nodo e set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md)
#### [Ridimensionare un cluster](service-fabric-cluster-scale-up-down.md)
#### [Aggiornare un cluster](service-fabric-cluster-upgrade.md)
#### [Eliminare un cluster](service-fabric-cluster-delete.md)
#### [Controllo di accesso](service-fabric-cluster-security-roles.md)
#### [Configurare un cluster](service-fabric-cluster-fabric-settings.md)
#### [Aggiungere o sostituire i certificati del cluster](service-fabric-cluster-security-update-certs-azure.md) 
#### [Provare gratuitamente un party cluster](http://aka.ms/tryservicefabric)

### Cluster autonomi
#### [Creare un cluster autonomo](service-fabric-cluster-creation-for-windows-server.md)
#### [Creare un cluster autonomo in Macchine virtuali di Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
#### [Ridimensionare un cluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Aggiornare un cluster](service-fabric-cluster-upgrade-windows-server.md)
#### [Proteggere un cluster](service-fabric-windows-cluster-x509-security.md)
#### [Controllo di accesso](service-fabric-cluster-security-roles.md)
#### [Configurare un cluster](service-fabric-cluster-manifest.md)
#### [Proteggere un cluster con i certificati](service-fabric-windows-cluster-x509-security.md)  
#### [Proteggere un cluster con la protezione di Windows](service-fabric-windows-cluster-windows-security.md) 

## Gestire e orchestrare le risorse cluster
### [Panoramica di Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
### [Architettura di Cluster Resource Manager](service-fabric-cluster-resource-manager-architecture.md)
### [Descrivere un cluster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Panoramica dei gruppi di applicazioni](service-fabric-cluster-resource-manager-application-groups.md)
### [Configurare le impostazioni di Cluster Resource Manager](service-fabric-cluster-resource-manager-configure-services.md)
### [Metriche di consumo delle risorse](service-fabric-cluster-resource-manager-metrics.md)
### [Usare l'affinità dei servizi](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [Criteri di posizionamento dei servizi](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [Gestire un cluster](service-fabric-cluster-resource-manager-management-integration.md)
### [Deframmentazione dei cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [Bilanciare un cluster](service-fabric-cluster-resource-manager-balancing.md)
### [Limitazione](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [Spostamento dei servizi](service-fabric-cluster-resource-manager-movement-cost.md)

## Gestire il ciclo di vita delle applicazioni
### [Panoramica](service-fabric-application-lifecycle.md)
### [Configurare l'integrazione continua](service-fabric-set-up-continuous-integration.md)
### Distribuire o rimuovere applicazioni
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [Panoramica dell'aggiornamento delle applicazioni](service-fabric-application-upgrade.md)
### [Configurare l'aggiornamento di un'applicazione](service-fabric-visualstudio-configure-upgrade.md)
### [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md)
### Aggiornare un'applicazione
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Risolvere i problemi relativi agli aggiornamenti delle applicazioni](service-fabric-application-upgrade-troubleshooting.md)
### [Serializzazione dei dati negli aggiornamenti delle applicazioni](service-fabric-application-upgrade-data-serialization.md)
### [Argomenti avanzati sugli aggiornamenti delle applicazioni](service-fabric-application-upgrade-advanced.md)

## Ispezionare l'integrità di applicazioni e cluster
### [Monitorare l'integrità di Service Fabric](service-fabric-health-introduction.md)
### [Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Aggiungere report sull'integrità personalizzati](service-fabric-report-health.md)
### [Risolvere i problemi relativi ai report sull'integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Visualizzare i report sull'integrità](service-fabric-view-entities-aggregated-health.md)

## Monitorare e diagnosticare
### Monitorare e diagnosticare servizi in locale
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Log di diagnostica di Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Raccogliere log da un processo di servizio](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Diagnostica in Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Diagnostica in Reliable Services con stato](service-fabric-reliable-services-diagnostics.md)
### [Risolvere i problemi del cluster locale](service-fabric-troubleshoot-local-cluster-setup.md)
### [Risolvere i problemi comuni](service-fabric-diagnostics-troubleshoot-common-scenarios.md)

## Ridimensionare le applicazioni
### [Partizionare Reliable Services](service-fabric-concepts-partitioning.md)
### [Disponibilità dei servizi](service-fabric-availability-services.md)
### [Ridimensionare le applicazioni](service-fabric-concepts-scalability.md)
### [Pianificare la capacità delle applicazioni](service-fabric-capacity-planning.md)

## Testare applicazioni e servizi
### [Panoramica dell'analisi degli errori](service-fabric-testability-overview.md)
### [Testare la comunicazione tra servizi](service-fabric-testability-scenarios-service-communication.md)
### Simulare gli errori
#### [Uso di Chaos controllato](service-fabric-controlled-chaos.md)
#### [Uso di azioni di test](service-fabric-testability-actions.md)
#### [Durante i carichi di lavoro](service-fabric-testability-workload-tests.md)
#### [Richiamando la perdita di dati](service-fabric-use-data-loss-api.md)
#### [Uso di scenari di test](service-fabric-testability-scenarios.md)
#### [Uso delle API di transizione del nodo](service-fabric-node-transition-apis.md)
### [Eseguire test di carico dell'applicazione](service-fabric-vso-load-test.md)

# Riferimento
## [PowerShell](//powershell/servicefabric/vlatest/servicefabric)
## [API Java](/java/api/microsoft.servicefabric.services)
## [.NET](/dotnet/api/microsoft.servicefabric.services)
## [REST](/rest/api/servicefabric)

# Risorse
## [Domande frequenti su Service Fabric](service-fabric-common-questions.md)
## [Opzioni di supporto di Service Fabric](service-fabric-support.md)
## [Codice di esempio](http://aka.ms/servicefabricsamples)
## [Percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Prezzi](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=service-fabric)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)


<!--HONumber=Jan17_HO3-->


