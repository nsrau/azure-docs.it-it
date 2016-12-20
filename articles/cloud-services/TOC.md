# Panoramica
## [Informazioni sui servizi cloud](cloud-services-choose-me.md)
## [File di configurazione e pacchetti del servizio cloud](cloud-services-model-and-package.md)

# Attività iniziali
## [Servizio cloud .NET di esempio](cloud-services-dotnet-get-started.md)
## [Servizio cloud Python per Visual Studio di esempio](cloud-services-python-ptvs.md)
## [Configurare un cluster HPC ibrido con Microsoft HPC Pack](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Procedure
## Pianificare
### [Dimensioni delle macchine virtuali](cloud-services-sizes-specs.md)
### [Aggiornamenti](cloud-services-update-azure-service.md)

## Sviluppo
### [Creare ruoli Web e di lavoro PHP](../cloud-services-php-create-web-role.md)
### [Sviluppare e distribuire un'applicazione Node.js](cloud-services-nodejs-develop-deploy-app.md)
### [Sviluppare un'applicazione Web Node.js con Express](cloud-services-nodejs-develop-deploy-express-app.md)
### Archiviazione e Visual Studio
#### [Archivio BLOB e servizi connessi](../storage/vs-storage-cloud-services-getting-started-blobs.md)
#### [Archivio code e servizi connessi](../storage/vs-storage-cloud-services-getting-started-queues.md)
#### [Archivio tabelle e servizi connessi](../storage/vs-storage-cloud-services-getting-started-tables.md)
### Configurare i pacchetti per la compilazione continua e la distribuzione
#### [Visual Studio Team Services e Git](cloud-services-continuous-delivery-use-vso-git.md)
#### [Visual Studio Team Services](cloud-services-continuous-delivery-use-vso.md)
#### [TFS e Team Build](cloud-services-dotnet-continuous-delivery.md)
### [Configurare le regole del traffico per un ruolo](cloud-services-enable-communication-role-instances.md)
### [Gestire eventi del ciclo di vita dei servizi cloud](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Usare Twilio per effettuare una chiamata telefonica (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)
### [New Relic](../store-new-relic-cloud-services-dotnet-application-performance-management.md)

### Configurare le attività di avvio
#### [Creare le attività di avvio](cloud-services-startup-tasks.md)
#### [Attività di avvio comuni](cloud-services-startup-tasks-common.md)
#### [Usare un'attività per installare .NET in un ruolo di un servizio cloud](cloud-services-dotnet-install-dotnet.md)

### Configurare Desktop remoto
#### [Visual Studio](cloud-services-role-enable-remote-desktop.md)
#### [Node.js](cloud-services-nodejs-enable-remote-desktop.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)

## Distribuire
### Creare e distribuire un servizio cloud nel portale
#### [Portale](cloud-services-how-to-create-deploy-portal.md)
#### [Portale classico](cloud-services-how-to-create-deploy.md)
### [Creare un contenitore di servizi cloud vuoto in PowerShell](cloud-services-powershell-create-cloud-container.md)
### Configurare un nome di dominio personalizzato
#### [Portale](cloud-services-custom-domain-name-portal.md)
#### [Portale classico](cloud-services-custom-domain-name.md)
### [Inserire temporaneamente una distribuzione di servizi cloud (Node.js)](cloud-services-nodejs-stage-application.md)
### [Connettersi a un controller di dominio personalizzato](cloud-services-connect-to-custom-domain.md)

## Gestire il servizio
### Attività di gestione comuni
#### [Portale](cloud-services-how-to-manage-portal.md)
#### [Portale classico](cloud-services-how-to-manage.md)
### Configurare il servizio cloud
#### [Portale](cloud-services-how-to-configure-portal.md)
#### [Portale classico](cloud-services-how-to-configure.md)
### [Gestire un servizio cloud con Automazione di Azure](automation-manage-cloud-services.md)
### Configurare la scalabilità automatica
#### [Portale](cloud-services-how-to-scale-portal.md)
#### [Portale classico](cloud-services-how-to-scale.md)
### [Usare Python per gestire le risorse di Azure](cloud-services-python-how-to-use-service-management.md)

### [Patch per il sistema operativo guest](cloud-services-guestos-msrc-releases.md)
### Ritiro del sistema operativo guest
#### [Criteri di ritiro](cloud-services-guestos-retirement-policy.md)
#### [Avviso di ritiro della famiglia 1](cloud-services-guestos-family1-retirement.md)
### [Novità sulle versioni del sistema operativo guest](cloud-services-guestos-update-matrix.md)
### [Foglio informativo su XPath per la configurazione di ruoli di Servizi cloud](cloud-services-role-config-xpath.md)

## Gestire i certificati
### [Servizi cloud e certificati di gestione](cloud-services-certs-create.md)
### Configurare SSL 
#### [Portale](cloud-services-configure-ssl-certificate-portal.md)
#### [Portale classico](cloud-services-configure-ssl-certificate.md)

## Monitorare
### [Monitorare il servizio cloud](cloud-services-how-to-monitor.md)
### [Testare le prestazioni](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Test con Visual Studio Profiler](cloud-services-performance-testing-visual-studio-profiler.md)
### Abilitare la diagnostica
#### [PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Usare contatori delle prestazioni in Diagnostica di Azure](cloud-services-dotnet-diagnostics-performance-counters.md)
### [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](cloud-services-dotnet-diagnostics-storage.md)
### [Tracciare un servizio cloud con Diagnostica](cloud-services-dotnet-diagnostics-trace-flow.md)
### [Inviare dati di diagnostica ad App Insights](cloud-services-dotnet-diagnostics-applicationinsights.md)

## Risolvere problemi
### Debug 
#### [Abilitare il debug remoto con la distribuzione di contenuti](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md)
#### [Opzioni per un servizio cloud](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Servizio cloud locale con Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Servizio cloud pubblicato con Visual Studio](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Errore di allocazione dei servizi cloud](cloud-services-allocation-failures.md)
### [Cause comuni del riciclo di ruoli dei servizi cloud](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [Dimensioni della cartella TEMP predefinita insufficienti per un ruolo](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Problemi di distribuzione comuni](cloud-services-troubleshoot-deployment-problems.md)
### [Errore di avvio di un ruolo](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Informazioni aggiuntive sul ripristino](cloud-services-disaster-recovery-guidance.md)
### [Domande frequenti sui servizi cloud](cloud-services-faq.md)

# Riferimento
## [XML Schema .csdef](https://msdn.microsoft.com/library/azure/ee758711)
## [XMLSchema .cscfg](https://msdn.microsoft.com/library/azure/ee758710)
## [REST](https://msdn.microsoft.com/library/azure/ee460812)

# Risorse
## [Prezzi](https://azure.microsoft.com/pricing/details/cloud-services/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)


<!--HONumber=Dec16_HO1-->


