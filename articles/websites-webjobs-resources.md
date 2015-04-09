<properties 
	pageTitle="Risorse per Processi Web di Azure" 
	description="Risorse consigliate per informazioni sull'uso di Processi Web di Azure e dell'SDK Processi Wev di Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Risorse per Processi Web di Azure

## Informazioni generali

In questo argomento vengono forniti i collegamenti a risorse della documentazione relative all'uso di Processi Web di Azure e di Azure WebJobs SDK. Processi Web di Azure costituisce una soluzione semplice e rapida per eseguire script e programmi sotto forma di processi in background in [App Web di Servizio app](http://go.microsoft.com/fwlink/?LinkId=529714). È infatti possibile caricare ed eseguire un file eseguibile in formato cmd, bat, exe (.NET), ps1, sh, php, py, js e jar, che verrà eseguito come processo Web in base a una pianificazione (cron) o senza interruzioni.

WebJobs SDK semplifica l'uso dell'archiviazione di Azure. Include infatti un sistema di binding e attivazione che interagisce con BLOB, code e tabelle di archiviazione di Microsoft Azure, oltre che con le code del bus di servizio.

Grazie agli strumenti integrati in Visual Studio, è inoltre semplice creare, distribuire e gestire processi Web. È possibile creare processi Web da modelli, nonché pubblicarli e gestirli (eseguirli/arrestarli/monitorarli/eseguirne il debug). 

Il dashboard Processi Web nel portale di Azure include efficaci funzionalità di gestione per controllare completamente l'esecuzione di processi Web, tra cui la possibilità di richiamare singole funzioni nei processi Web. Il dashboard visualizza inoltre i runtime delle funzioni e l'output delle registrazioni. 

##<a name="getstarted"></a>Introduzione a Processi Web e all'SDK Processi Web

* [Introduzione a Processi Web di Azure](http://www.hanselman.comblog.md/IntroducingWindowsAzureWebJobs.aspx)
* [Funzionalità Processi Web di Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Informazioni sull'SDK Processi Web](websites-dotnet-webjobs-sdk.md)
* [Annuncio relativo alla versione 1.0.0 RTM dell'SDK Processi Web di Microsoft Azure](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Introduzione all'SDK Processi Web di Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Come usare l'archiviazione delle code di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Come usare l'archivio BLOB di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Come usare l'archivio tabelle di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Come usare il bus di servizio di Azure con l'SDK Processi Web](websites-dotnet-webjobs-sdk-service-bus.md)
* [Riferimenti rapidi all'SDK Processi Web (PDF da scaricare)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Video
	* [Processi Web e SDK Processi Web](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Serie di video su Processi Web di Azure su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Introduzione agli strumenti di Processi Web per Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Strumenti di Processi Web e debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

Vedere anche le seguenti sezioni in [Distribuzione di processi Web](#deploy) e [Test e debug di processi Web](#debug).

##<a name="deploy"></a>Distribuzione di Processi Web

* [Come distribuire Processi Web di Azure nei siti Web di Azure](websites-dotnet-deploy-webjobs.md)
* [Come distribuire Processi Web usando il portale di gestione di Azure](web-sites-create-web-jobs.md)
* [Abilitazione della riga di comando o del recapito continuo dei Processi Web di Azure](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Distribuzione Git di un'app di console .NET in Azure usando Processi Web](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* Video
	* [Introduzione agli strumenti di Processi Web per Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Strumenti di Processi Web e debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Pianificazione di Processi Web

* [Finestra di dialogo Aggiungi processo Web Azure](websites-dotnet-deploy-webjobs.md#configure)
* [Creare un processo Web pianificato nel portale di gestione di Azure](web-sites-create-web-jobs.md#CreateScheduled)

##<a name="debug"></a>Verifica e debug di Processi Web

* [Nuove funzionalità per sviluppatori e di debug per Processi Web di Azure in Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Visualizzazione del dashboard di Processi Web](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Come scrivere log usando l'SDK Processi Web e come visualizzarli nel dashboard](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Debug remoto di processi Web](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Chi ha scritto il BLOB?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hosting del codice interattivo nel cloud](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Visualizzazione di un dashboard per lo sviluppo locale tramite l'SDK Processi Web](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Aggiunta di analisi ai siti Web e ai Processi Web di Azure](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Monitoraggio, diagnostica e risoluzione dei problemi relativi ad  Archiviazione di Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting/)
* Video
	* [Strumenti di Processi Web e debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Scalabilità di Processi Web 

* [Ridimensionamento dell'applicazione Web con Siti Web di Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Siti Web di Azure: Progettazione di app Web pronte per l'uso in ambito aziendale su vasta scala](https://channel9.msdn.com/Events/Build/2014/3-626). Tratta il ridimensionamento di siti Web di Web con Processi Web e include informazioni anche su WebJobs SDK.
* Video
	* [Scalabilità orizzontale di Processi Web](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Risorse aggiuntive su Processi Web

* [Post di blog di Magnus Mårtensson sulla versione GA di Processi Web di Azure](http://magnusmartensson.com/azure-webjobs-ga)
* [Documentazione sulle impostazioni di WebJobs in GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Esecuzione di processi Web di Powershell in siti Web di Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Ricevere notifiche quando i processi Web di Azure attivati vengono completati](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Criteri semplici di conservazione dei backup di siti Web con Processi Web](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Lentezza dei siti Web e dei servizi cloud di Azure quando vengono richiesti per la prima volta](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Spiega come usare Processi Web per simulare la funzionalità AlwaysOn che è disponibile solo con il livello standard di Siti Web.
* [Arresto normale di Processi Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Per l'arresto normale di WebJobs SDK, vedere [Arresto normale](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* Video
	* [Video di Magnus Mårtensson su Processi Web di Azure](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Serie di video su Processi Web di Azure su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Risorse aggiuntive su WebJobs SDK

* [Trigger, binding e parametri route in AzureJob](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Binding di archiviazione di Azure
	* [BLOBS](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [Code](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [Tabelle](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [Come funziona [BlobTrigger]?](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Binding avanzati con Azure WebJobs SDK](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Processo Web per caricare file FREB nell'archiviazione di Azure con WebJobs SDK](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hosting di processi Web di Azure all'esterno di Azure, usufruendo dei vantaggi della registrazione offerti da un processo Web ospitato in Azure](http://bypassion.dk/?p=510)
* [Creazione di uno strumento di importazione dati con Processi Web di Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Video
	* [Serie di video su Processi Web di Azure su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Applicazioni Processi Web di esempio

* [Applicazioni di esempio fornite dal team di Processi Web su GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Sito Web di Azure di esempio con back-end di Processi Web mediante WebJobs SDK](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Illustra l'uso di processi Web pianificati o guidati dagli eventi. Vedere il post di blog [Ricompilazione di SiteMonitR usando Azure WebJobs SDK](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blog

* [Blog di Azure](/blog)
* [Blog di Amit Apple](http://blog.amitapple.com/). Incentrato sui Processi Web (non contiene informazioni sull'SDK).
* [Blog di Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Guida per i Processi Web

* [StackOverflow per Processi Web](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow per WebJobs SDK](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Forum su Azure e ASP.NET](http://forums.asp.net/1247.aspx)
* [Forum su Siti Web di Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Sito User Voice su Siti Web di Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Usare l'hashtag #AzureWebJobs.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->