<properties pageTitle="Azure Spring 2014 release highlights - .NET Dev Center" metaKeywords="azure .net sdk 2.3" description="Learn about the new tools and features available for Azure .NET developers." documentationCenter=".NET" title="Azure Spring 2014 release highlights" authors="mollybos" solutions="" manager="carolz" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mollybos"></tags>

# Novità di rilievo della versione di Azure della primavera 2014

In questo articolo vengono riepilogati i nuovi strumenti, le nuove funzionalità e i nuovi temi presentati agli sviluppatori di Azure .NET nel corso della conferenza Build 2014 e disponibili in Azure SDK per .NET 2.3, Visual Studio 2013 aggiornamento 2 e altre versioni primaverili.

**Strumenti:**

-   [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC]
-   [Azure SDK 2.3][Azure SDK 2.3]
-   [Azure PowerShell][Azure PowerShell]
-   [Interfaccia della riga di comando multipiattaforma di Azure][Interfaccia della riga di comando multipiattaforma di Azure]

Per informazioni dettagliate sugli strumenti rilasciati nella primavera 2014, vedere [Note sulla versione di Azure SDK per .NET 2.3][Note sulla versione di Azure SDK per .NET 2.3] e la pagina degli [aggiornamenti del prodotto di Visual Studio 2013][aggiornamenti del prodotto di Visual Studio 2013].

Sono anche disponibili in streaming su richiesta i [video Build][video Build].

## Sommario

-   [Sviluppo e pubblicazione su Web][Sviluppo e pubblicazione su Web]
-   [Diagnostica e debug][Diagnostica e debug]
-   [Gestione dei servizi di Azure in Visual Studio][Gestione dei servizi di Azure in Visual Studio]
-   [Automazione con PowerShell][Automazione con PowerShell]
-   [Sviluppo per dispositivi mobili con .NET][Sviluppo per dispositivi mobili con .NET]
-   [Libreria del client di archiviazione 3.0 ed emulatore di archiviazione][Libreria del client di archiviazione 3.0 ed emulatore di archiviazione]
-   [Gestione risorse][Gestione risorse]

## <span id="webdeploy"></span></a>Sviluppo e pubblicazione su Web

Azure SDK 2.3 e Visual Studio 2013 Update 2 RC includono diversi aggiornamenti che consentono di semplificare lo sviluppo e la pubblicazione su Web con Azure. È ora possibile creare un sito Web o una macchina virtuale di Azure durante la creazione di una nuova app Web. Quando il sito Web è pronto per la pubblicazione, è possibile usare la finestra di pubblicazione Web aggiornata o gli script di PowerShell aggiunti alla soluzione per distribuire il sito direttamente in un sito Web o in una macchina virtuale di Azure. Consultare le risorse seguenti per ulteriori dettagli ed esercitazioni su come sfruttare le nuove funzionalità:

-   [Introduzione ad Azure e ASP.NET][Introduzione ad Azure e ASP.NET]
-   [Guida introduttiva agli strumenti di Azure per Visual Studio][Guida introduttiva agli strumenti di Azure per Visual Studio]
-   [Creazione di progetti Web ASP.NET in Visual Studio 2013][Creazione di progetti Web ASP.NET in Visual Studio 2013]
-   [Build 2014: Novità di ASP.NET e per il Web in Visual Studio 2013 Update 2 e altri strumenti (video)][Build 2014: Novità di ASP.NET e per il Web in Visual Studio 2013 Update 2 e altri strumenti (video)]

## <span id="diagnostics"></span></a>Diagnostica e debug

Le nuova funzionalità di debug remoto per Macchine virtuali e di debug di codice nativo consentono di diagnosticare in remoto i problemi delle applicazioni:

-   [Debug di un servizio cloud o di una macchina virtuale in Visual Studio][Debug di un servizio cloud o di una macchina virtuale in Visual Studio]

Emulator Express è il nuovo emulatore locale leggero per Servizi cloud. Per altre informazioni su come usarlo per testare Servizi cloud nel computer locale, vedere:

-   [Utilizzo di Emulator Express per l'esecuzione e il debug in locale di un servizio cloud][Utilizzo di Emulator Express per l'esecuzione e il debug in locale di un servizio cloud]

Se Azure SDK 2.3 è installato nel computer, è ora possibile visualizzare e modificare in remoto i file del sito Web da Esplora server e visualizzare i file di log dei propri siti. Quando si salva un file modificato, questo viene salvato nel sito senza che sia necessario pubblicarlo. Per informazioni dettagliate, vedere:

-   [Risoluzione dei problemi di Siti Web di Azure in Visual Studio][Risoluzione dei problemi di Siti Web di Azure in Visual Studio]

## <span id="service-management"></span></a>Gestione dei servizi di Azure in Visual Studio

È possibile sfruttare le funzionalità avanzate di Visual Studio per la gestione delle macchine virtuali, con la possibilità di creare VM dall'ambiente IDE:

-   [Creazione di macchine virtuali di Azure in Visual Studio][Creazione di macchine virtuali di Azure in Visual Studio]
-   [Accesso alle macchine virtuali di Azure da Esplora server][Accesso alle macchine virtuali di Azure da Esplora server]

Sono inoltre disponibili diversi miglioramenti per consentire una gestione più efficace di altri servizi di Azure da Esplora server. Per informazioni dettagliate, vedere:

-   [Esplorazione delle risorse del bus di sistema con Esplora server di Visual Studio][Esplorazione delle risorse del bus di sistema con Esplora server di Visual Studio]
-   [Esplorazione delle risorse di archiviazione con Esplora server][Esplorazione delle risorse di archiviazione con Esplora server]

## <span id="automation"></span></a>Automazione con PowerShell e le API

Installare Azure Powershell per sfruttare i nuovi cmdlet per siti Web, processi Web e altro ancora. Scrivere altro codice di automazione personalizzato tramite l'API di gestione del servizio per .NET. Per informazioni dettagliate, vedere:

-   [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell]
-   [Documentazione di Azure PowerShell][Documentazione di Azure PowerShell]
-   [Build 2014: Automazione ovunque con i nuovi SDK, strumenti e servizi di Azure (video)][Build 2014: Automazione ovunque con i nuovi SDK, strumenti e servizi di Azure (video)]

Creare script di PowerShell direttamente in Visual Studio e usarli per automatizzare la creazione dell'ambiente:

-   [Uso degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test][Uso degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test]

## <span id="mobile"></span></a>Sviluppo per dispositivi mobili con .NET

Servizi mobili di Azure offre ora la possibilità di usare back-end basati su .NET per le app destinate a piattaforme mobili, tra cui Windows Store, Windows Phone, iOS e Android. Per altre informazioni, vedere:

-   [Cloud Cover: Back-end .NET di Servizi mobili di Azure (video)][Cloud Cover: Back-end .NET di Servizi mobili di Azure (video)]
-   [Dev Center di Servizi mobili di Azure][Dev Center di Servizi mobili di Azure]

L'aggiornamento 2 di Visual Studio 2013 include anche il nuovo supporto per lo sviluppo di servizi mobili, inclusi il supporto per il debug remoto e l'integrazione di Hub di notifica in Esplora server. Per informazioni dettagliate, vedere:

-   [Quickstart: Aggiungere un servizio mobile][Quickstart: Aggiungere un servizio mobile]
-   [Come inviare notifiche push a un'app in esecuzione con Visual Studio][Come inviare notifiche push a un'app in esecuzione con Visual Studio]
-   [Come creare API personalizzate e processi pianificati in un servizio mobile][Come creare API personalizzate e processi pianificati in un servizio mobile]

## <span id="storage"></span></a>Libreria del client di archiviazione 3.0

Azure SDK 2.3 include un emulatore di archiviazione aggiornato e la libreria del client di archiviazione 3.0 è integrata nei modelli di progetto disponibili nell'SDK.

Per informazioni dettagliate, vedere:

-   [Libreria del client di archiviazione di Azure 3.0][Libreria del client di archiviazione di Azure 3.0]
-   [Introduzione ad Archiviazione di Azure][Introduzione ad Archiviazione di Azure]
-   [Build 2014: Archiviazione di Microsoft Azure - Novità, procedure consigliate e modelli (video)][Build 2014: Archiviazione di Microsoft Azure - Novità, procedure consigliate e modelli (video)]
-   [Archiviazione di Microsoft Azure in BUILD 2014][Archiviazione di Microsoft Azure in BUILD 2014]

## <span id="arm"></span></a>Gestione risorse

Gestione risorse è un nuovo framework per la distribuzione e la gestione di applicazioni tra più risorse. Sperimentare con Gestione risorse usando il nuovo editor JSON, i cmdlet di PowerShell e il supporto dell'interfaccia della riga di comando. Per altre informazioni, vedere:

-   [Utilizzo di Azure PowerShell con Gestione risorse][Utilizzo di Azure PowerShell con Gestione risorse]
-   [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse][Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse]
-   [Build 2014: Modello del gruppo di risorse di Azure: gestione moderna per il cloud moderno (video)][Build 2014: Modello del gruppo di risorse di Azure: gestione moderna per il cloud moderno (video)]

  [Visual Studio 2013 Update 2 RC]: http://aka.ms/vs2013update2rc
  [Azure SDK 2.3]: http://www.windowsazure.com/it-it/downloads/
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9811175
  [Interfaccia della riga di comando multipiattaforma di Azure]: http://go.microsoft.com/?linkid=9828653
  [Note sulla versione di Azure SDK per .NET 2.3]: http://go.microsoft.com/fwlink/p/?LinkId=393548
  [aggiornamenti del prodotto di Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=272487
  [video Build]: http://go.microsoft.com/fwlink/?LinkId=394377&clcid=0x409
  [Sviluppo e pubblicazione su Web]: #webdeploy
  [Diagnostica e debug]: #diagnostics
  [Gestione dei servizi di Azure in Visual Studio]: #service-management
  [Automazione con PowerShell]: #automation
  [Sviluppo per dispositivi mobili con .NET]: #mobile
  [Libreria del client di archiviazione 3.0 ed emulatore di archiviazione]: #storage
  [Gestione risorse]: #arm
  [Introduzione ad Azure e ASP.NET]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-dotnet-get-started/
  [Guida introduttiva agli strumenti di Azure per Visual Studio]: http://msdn.microsoft.com/it-it/library/azure/ff687127.aspx
  [Creazione di progetti Web ASP.NET in Visual Studio 2013]: http://asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio
  [Build 2014: Novità di ASP.NET e per il Web in Visual Studio 2013 Update 2 e altri strumenti (video)]: http://channel9.msdn.com/Events/Build/2014/3-602
  [Debug di un servizio cloud o di una macchina virtuale in Visual Studio]: http://msdn.microsoft.com/it-it/library/azure/ff683670.aspx
  [Utilizzo di Emulator Express per l'esecuzione e il debug in locale di un servizio cloud]: http://msdn.microsoft.com/it-it/library/windowsazure/dn339018.aspx
  [Risoluzione dei problemi di Siti Web di Azure in Visual Studio]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio
  [Creazione di macchine virtuali di Azure in Visual Studio]: http://msdn.microsoft.com/it-it/library/windowsazure/dn569263.aspx
  [Accesso alle macchine virtuali di Azure da Esplora server]: http://msdn.microsoft.com/it-it/library/windowsazure/jj131259.aspx
  [Esplorazione delle risorse del bus di sistema con Esplora server di Visual Studio]: http://msdn.microsoft.com/it-it/library/windowsazure/jj149828.aspx
  [Esplorazione delle risorse di archiviazione con Esplora server]: http://msdn.microsoft.com/it-it/library/windowsazure/ff683677.aspx
  [Come installare e configurare Azure PowerShell]: http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell/
  [Documentazione di Azure PowerShell]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156055.aspx
  [Build 2014: Automazione ovunque con i nuovi SDK, strumenti e servizi di Azure (video)]: http://channel9.msdn.com/Events/Build/2014/3-621
  [Uso degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test]: http://msdn.microsoft.com/it-it/library/windowsazure/dn642480.aspx
  [Cloud Cover: Back-end .NET di Servizi mobili di Azure (video)]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-137-The-Azure-Mobile-Services-NET-Backend-with-Yavor-Georgiev
  [Dev Center di Servizi mobili di Azure]: /it-it/develop/mobile/
  [Quickstart: Aggiungere un servizio mobile]: http://msdn.microsoft.com/it-it/library/windows/apps/xaml/dn629482.aspx
  [Come inviare notifiche push a un'app in esecuzione con Visual Studio]: http://msdn.microsoft.com/it-it/library/windows/apps/xaml/dn614131.aspx
  [Come creare API personalizzate e processi pianificati in un servizio mobile]: http://msdn.microsoft.com/it-it/library/windows/apps/xaml/dn614130.aspx
  [Libreria del client di archiviazione di Azure 3.0]: http://go.microsoft.com/fwlink/?LinkId=394927
  [Introduzione ad Archiviazione di Azure]: /it-it/documentation/articles/storage-introduction/
  [Build 2014: Archiviazione di Microsoft Azure - Novità, procedure consigliate e modelli (video)]: http://channel9.msdn.com/Events/Build/2014/3-628
  [Archiviazione di Microsoft Azure in BUILD 2014]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/08/microsoft-azure-storage-build-2014.aspx
  [Utilizzo di Azure PowerShell con Gestione risorse]: http://go.microsoft.com/fwlink/?LinkID=394767
  [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse]: /it-it/documentation/articles/xplat-cli-azure-resource-manager/
  [Build 2014: Modello del gruppo di risorse di Azure: gestione moderna per il cloud moderno (video)]: http://channel9.msdn.com/Events/Build/2014/2-607
