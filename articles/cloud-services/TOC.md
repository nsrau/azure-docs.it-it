# [Documentazione di Servizi cloud](index.md)

# Panoramica
## [Informazioni sui servizi cloud](cloud-services-choose-me.md)
## [File di configurazione e pacchetti del servizio cloud](cloud-services-model-and-package.md)

# Attività iniziali
## [Servizio cloud .NET di esempio](cloud-services-dotnet-get-started.md)
## [Servizio cloud Python per Visual Studio di esempio](cloud-services-python-ptvs.md)
## [Configurare un cluster HPC ibrido con Microsoft HPC Pack](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Procedure
## Pianificazione
### [Dimensioni delle macchine virtuali](cloud-services-sizes-specs.md)
### [Aggiornamenti](cloud-services-update-azure-service.md)

## Sviluppo
### [Creare ruoli Web e di lavoro PHP](../cloud-services-php-create-web-role.md)
### [Sviluppare e distribuire un'applicazione Node.js](cloud-services-nodejs-develop-deploy-app.md)
### [Sviluppare un'applicazione Web Node.js con Express](cloud-services-nodejs-develop-deploy-express-app.md)
### Archiviazione e Visual Studio
#### [Archivio BLOB e servizi connessi](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [Archivio code e servizi connessi](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [Archivio tabelle e servizi connessi](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### [Configurare le regole del traffico per un ruolo](cloud-services-enable-communication-role-instances.md)
### [Gestire eventi del ciclo di vita dei servizi cloud](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Usare Twilio per effettuare una chiamata telefonica (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)

### Configurare le attività di avvio
#### [Creare le attività di avvio](cloud-services-startup-tasks.md)
#### [Attività di avvio comuni](cloud-services-startup-tasks-common.md)
#### [Usare un'attività per installare .NET in un ruolo di un servizio cloud](cloud-services-dotnet-install-dotnet.md)

### Configurare Desktop remoto
#### [di Microsoft Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
#### [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

## Distribuire
### [Creare e distribuire un servizio cloud nel portale](cloud-services-how-to-create-deploy-portal.md)
### [Creare un contenitore di servizi cloud vuoto in PowerShell](cloud-services-powershell-create-cloud-container.md)
### [Configurare un nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md)
### [Connettersi a un controller di dominio personalizzato](cloud-services-connect-to-custom-domain.md)

## Gestire il servizio
### [Attività di gestione comuni](cloud-services-how-to-manage-portal.md)
### [Configurare il servizio cloud](cloud-services-how-to-configure-portal.md)
### [Gestire un servizio cloud con Automazione di Azure](automation-manage-cloud-services.md)
### [Configurare il ridimensionamento automatico](cloud-services-how-to-scale-portal.md)
### [Usare Python per gestire le risorse di Azure](cloud-services-python-how-to-use-service-management.md)
### [Attenuazione dell'esecuzione speculativa](mitigate-se.md)

### [Patch per il sistema operativo guest](cloud-services-guestos-msrc-releases.md)
### Ritiro del sistema operativo guest
#### [Criteri di ritiro](cloud-services-guestos-retirement-policy.md)
#### [Avviso di ritiro della famiglia 1](cloud-services-guestos-family1-retirement.md)
### [Novità sulle versioni del sistema operativo guest](cloud-services-guestos-update-matrix.md)
### [Foglio informativo su XPath per la configurazione di ruoli di Servizi cloud](cloud-services-role-config-xpath.md)

## Gestire i certificati
### [Servizi cloud e certificati di gestione](cloud-services-certs-create.md)
### [Configurare SSL](cloud-services-configure-ssl-certificate-portal.md)

## Monitorare
### [Monitorare il servizio cloud](cloud-services-how-to-monitor.md)
### [Usare i contatori delle prestazioni](diagnostics-performance-counters.md)
### [Testare le prestazioni](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Test con Visual Studio Profiler](cloud-services-performance-testing-visual-studio-profiler.md)
### Abilitare la diagnostica
#### [Azure PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](cloud-services-dotnet-diagnostics-storage.md)
### [Tracciare un servizio cloud con Diagnostica](cloud-services-dotnet-diagnostics-trace-flow.md)

## Risolvere problemi
### Debug 
#### [Opzioni per un servizio cloud](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Servizio cloud locale con Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Servizio cloud pubblicato con Visual Studio](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Errore di allocazione dei servizi cloud](cloud-services-allocation-failures.md)
### [Cause comuni del riciclo di ruoli dei servizi cloud](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [Dimensioni della cartella TEMP predefinita insufficienti per un ruolo](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Problemi di distribuzione comuni](cloud-services-troubleshoot-deployment-problems.md)
### [Errore di avvio di un ruolo](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Informazioni aggiuntive sul ripristino](cloud-services-disaster-recovery-guidance.md)
### Domande frequenti sui servizi cloud
#### [Domande frequenti sulla disponibilità dell'applicazione e del servizio](cloud-services-application-and-service-availability-faq.md)
#### [Domande frequenti su configurazione e gestione](cloud-services-configuration-and-management-faq.md)
#### [Domande frequenti su connettività e rete](cloud-services-connectivity-and-networking-faq.md)
#### [Domande frequenti sulla distribuzione](cloud-services-deployment-faq.md)

# riferimento
## [Esempi di codice](https://azure.microsoft.com/en-us/resources/samples/?service=cloud-services)
## [Schema XML .csdef](schema-csdef-file.md)
### [Schema LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
### [Schema WebRole](schema-csdef-webrole.md)
### [Schema WebRole](schema-csdef-workerrole.md)
### [Schema NetworkTrafficRules](schema-csdef-networktrafficrules.md)
## [Schema XML .cscfg](schema-cscfg-file.md)
### [Schema del ruolo](schema-cscfg-role.md)
### [Schema NetworkConfiguration](schema-cscfg-networkconfiguration.md)
## [REST](/rest/api/compute/cloudservices/)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=compute)
## [Percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Prezzi](https://azure.microsoft.com/pricing/details/cloud-services/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
