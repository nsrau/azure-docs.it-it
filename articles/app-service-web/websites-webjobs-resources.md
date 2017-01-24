---
title: Risorse di documentazione di Processi Web di Azure
description: Risorse consigliate per informazioni sull&quot;uso di Processi Web di Azure e Azure WebJobs SDK.
services: app-service
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: jimbe
ms.assetid: ed005e56-4334-4641-a5e5-15435c2be36b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 64e2ed7a625aec59f68411d93a5942a546919abb


---
# <a name="azure-webjobs-documentation-resources"></a>Risorse di documentazione di Processi Web di Azure
## <a name="overview"></a>Panoramica
Questo argomento include i collegamenti a risorse della documentazione relative all'uso di Processi Web di Azure e di Azure WebJobs SDK. Processi Web di Azure costituisce una soluzione semplice per eseguire script o programmi come processi in background nel contesto di un'[app Web del servizio app, un'app per le API o un'app per dispositivi mobili](../app-service/app-service-value-prop-what-is.md). È infatti possibile caricare ed eseguire un file eseguibile in formato cmd, bat, exe (.NET), ps1, sh, php, py, js e jar, che verrà eseguito come processo Web in base a una pianificazione (cron) o senza interruzioni.

Lo scopo di [WebJobs SDK](websites-webjobs-resources.md) è semplificare il codice scritto per le attività comuni che possono essere eseguite da un processo Web, ad esempio l'elaborazione di immagini, l'elaborazione della coda, l'aggregazione RSS, la manutenzione di file e l'invio di messaggi di posta elettronica. WebJobs SDK offre funzionalità incorporate per l'uso dell'Archiviazione di Azure e del bus di servizio, per la pianificazione di attività, per la gestione degli errori e per molti altri scenari comuni. È progettato per l'estensibilità e ha a disposizione un [repository open source di estensioni](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Funzioni di Azure](../azure-functions/functions-overview.md) (attualmente in anteprima) si basa su una versione di WebJobs SDK che usa script C#, Node.js e in altri linguaggi. 

Grazie agli strumenti integrati in Visual Studio, è inoltre semplice creare, distribuire e gestire processi Web. È possibile creare processi Web da modelli, nonché pubblicarli e gestirli (eseguirli/arrestarli/monitorarli/eseguirne il debug). 

Il dashboard Processi Web nel portale di Azure include efficaci funzionalità di gestione per controllare completamente l'esecuzione di processi Web, tra cui la possibilità di richiamare singole funzioni nei processi Web. Il dashboard visualizza inoltre i runtime delle funzioni e l'output delle registrazioni. 

## <a name="a-namegetstartedagetting-started-with-webjobs-and-the-webjobs-sdk"></a><a name="getstarted"></a>Introduzione a Processi Web e WebJobs SDK
* [Introduzione a Processi Web di Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Post di blog sui vantaggi offerti da Processi Web di Azure](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html)  (di Troy Hunt)
* [Funzionalità di Processi Web di Azure](https://azure.microsoft.com/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Informazioni su WebJobs SDK](websites-dotnet-webjobs-sdk.md)
* [Indicazioni di processi di background Microsoft Patterns and Practices](/documentation/articles/best-practices-background-jobs/)
* [Annuncio della versione 1.1.0 RTM di Microsoft Azure WebJobs SDK](https://azure.microsoft.com/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Introduzione a Azure WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md)
* [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Come usare il servizio di archiviazione tabelle di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Come usare il bus di servizio di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-service-bus.md)
* [Come usare WebHooks con WebJobs SDK, con esempi per GitHub, IFTTT e HTTP](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Riferimento rapido per Azure WebJobs SDK (download PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Documentazione sulle impostazioni di WebJobs in GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Video
  * [Processi Web e WebJobs SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
  * [Serie di video su Processi Web su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
  * [Introduzione agli strumenti di Processi Web per Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [Strumenti di Processi Web e debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
  * [Aggiornamento di Processi Web di Azure con Pranav Rastogi: estendibilità nella versione 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Vedere anche le sezioni seguenti su [Distribuzione di Processi Web](#deploy) e di [Verifica e debug di Processi Web](#debug).

## <a name="a-namedeployadeploying-webjobs"></a><a name="deploy"></a>Distribuzione di Processi Web
* [Come distribuire Processi Web di Azure mediante Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Come distribuire Processi Web mediante il portale di Azure](web-sites-create-web-jobs.md)
* [Abilitazione della distribuzione da riga di comando o continua di processi Web di Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Distribuzione tramite Git di un'app per console .NET in Azure mediante Processi Web](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Distribuzione di un processo Web F# in Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Distribuzione di servizi personalizzati come Azure Webjobs](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Video
  * [Introduzione agli strumenti di Processi Web per Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [Strumenti di Processi Web e debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="a-namescheduleascheduling-webjobs"></a><a name="schedule"></a>Pianificazione di Processi Web
* [Finestra di dialogo Aggiungi processo Web Azure](websites-dotnet-deploy-webjobs.md#configure)
* [Creare un processo Web pianificato nel portale di Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Associazione di un processo Web a un processo di pianificazione](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Pianificazione dei processi Web di Azure con espressioni cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Pianificazione delle singole funzioni di Processi Web tramite WebJobs SDK TimerTrigger](websites-dotnet-webjobs-sdk.md#schedule)

## <a name="a-namedebugatesting-and-debugging-webjobs"></a><a name="debug"></a>Verifica e debug di Processi Web
* [Nuove funzionalità per sviluppatori e debug di Processi Web di Azure in Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Visualizzare il dashboard di WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Come scrivere i log mediante WebJobs SDK e visualizzarli nel dashboard](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Debug remoto di processi Web](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Come ottenere informazioni sull'autore del BLOB](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hosting di codice interattivo nel cloud](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Aggiunta di traccia a Azure WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Video
  * [Strumenti di Processi Web e debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="a-namescaleascaling-webjobs"></a><a name="scale"></a>Ridimensionamento di Processi Web
* [Ridimensionamento dell'applicazione Web con Siti Web di Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Progettazione di app Web pronte per l'uso in ambito aziendale su vasta scala](https://channel9.msdn.com/Events/Build/2014/3-626). Tratta il ridimensionamento di siti Web di Web con Processi Web e include informazioni anche su WebJobs SDK.
* Video
  * [Scalabilità orizzontale di Processi Web](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

## <a name="a-nameadditionalaadditional-webjobs-resources"></a><a name="additional"></a>Risorse aggiuntive su Processi Web
* [Post di blog di Magnus Mårtensson sulla versione GA di Processi Web di Azure](http://magnusmartensson.com/azure-webjobs-ga)
* [Esecuzione di processi Web di Powershell in siti Web di Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Ricevere notifiche quando i processi Web di Azure attivati vengono completati](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Criteri semplici di conservazione dei backup di siti Web con Processi Web](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Lentezza dei siti Web e dei servizi cloud di Microsoft Azure quando vengono richiesti per la prima volta](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Spiega come usare Processi Web per simulare la funzionalità AlwaysOn che è disponibile solo con il livello standard di Siti Web.
* [Arresto normale di Processi Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Per l'arresto normale di WebJobs SDK, vedere [Arresto normale](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).
* [Automatizzazione di backup con Processi Web di Azure e AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Video
  * [Video di Magnus Mårtensson su Processi Web di Azure](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
  * [Serie di video su Processi Web su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="a-nameadditionalsdkaadditional-webjobs-sdk-resources"></a><a name="additionalsdk"></a>Risorse aggiuntive su WebJobs SDK
* [Note sulla versione di WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [Codice sorgente WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk)
* [Codice sorgente per estensioni WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions), con una [guida dettagliata al modello di estendibilità](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Codice sorgente di script WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-script/) (usato per [Funzioni di Azure](../azure-functions/functions-overview.md))
* [Processo Web per caricare file FREB nell'archiviazione di Azure con WebJobs SDK](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hosting di processi Web di Azure all'esterno di Azure, usufruendo dei vantaggi della registrazione offerti da un processo Web ospitato di Azure](http://bypassion.dk/?p=510)
* [Creazione di uno strumento di importazione dati con Processi Web di Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Panoramica di Funzioni di Azure](../azure-functions/functions-overview.md)
* Video
  * [Serie di video su Processi Web su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="a-namesamplesasample-webjob-applications"></a><a name="samples"></a>Applicazioni Processi Web di esempio
* [Applicazioni di esempio fornite dal team di Processi Web su GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Sito Web di Azure di esempio con back-end di Processi Web mediante WebJobs SDK](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Illustra l'uso di processi Web pianificati o guidati dagli eventi. Vedere il post di blog sulla [ricostruzione di SiteMonitR con Azure WebJobs SDK](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

## <a name="a-nameblogsablogs"></a><a name="blogs"></a>Blog
* [Blog di Azure](/blog)
* [Blog di Amit Apple](http://blog.amitapple.com/). Incentrato sui Processi Web (non contiene informazioni sull'SDK).
* [Blog di Magnus Mårtensson](http://magnusmartensson.com/)

## <a name="a-namegethelpagetting-help-with-webjobs"></a><a name="gethelp"></a>Guida per i Processi Web
* [StackOverflow per Processi Web](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow per WebJobs SDK](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow per Funzioni di Azure](http://stackoverflow.com/questions/tagged/azure-functions)
* [Forum su Azure e ASP.NET](http://forums.asp.net/1247.aspx)
* [Forum sulle app Web del servizio app di Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Sito di Azure Web App utente vocale](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Usare l'hashtag #AzureWebJobs.
* [Segnalare un bug o un problema di Processi Web](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)




<!--HONumber=Dec16_HO2-->


