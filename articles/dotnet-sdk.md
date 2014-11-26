<properties pageTitle="What is the Azure .NET SDK" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure .NET SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# Informazioni su Azure SDK per .NET

Azure SDK per .NET è un set di strumenti di Visual Studio, strumenti da riga di comando, file binari di runtime e librerie client che consentono di sviluppare, testare e distribuire app eseguite in Azure. Questo articolo illustra le funzionalità ottenute quando si installa l'SDK. È possibile scaricare l'SDK dalla [pagina dei download di Azure][pagina dei download di Azure].

Azure SDK per .NET include inoltre librerie client per l'uso di servizi di Azure Tali librerie vanno installate separatamente mediante [NuGet][NuGet].

## Sommario

-   [Cosa è incluso nell'installazione di Azure SDK per .NET][Cosa è incluso nell'installazione di Azure SDK per .NET]
-   [Cosa non è incluso nell'installazione di Azure SDK per .NET][Cosa non è incluso nell'installazione di Azure SDK per .NET]
-   [Domande frequenti][Domande frequenti]
-   [Risorse][Risorse]

## <span id="included"></span></a>Cosa è incluso nell'installazione di Azure SDK per .NET

Azure SDK per .NET installa i prodotti seguenti:

-   [Visual Studio Express per il Web][Visual Studio Express per il Web]
-   [Microsoft ASP.NET e strumenti Web per Visual Studio][Microsoft ASP.NET e strumenti Web per Visual Studio]
-   [Strumenti di Microsoft Azure per Microsoft Visual Studio][Strumenti di Microsoft Azure per Microsoft Visual Studio]
-   [Strumenti di creazione di Microsoft Azure][Strumenti di creazione di Microsoft Azure]
-   [Emulatore di Microsoft Azure][Emulatore di Microsoft Azure]
-   [Emulatore di archiviazione di Microsoft Azure][Emulatore di archiviazione di Microsoft Azure]
-   [Strumenti di archiviazione di Microsoft Azure][Strumenti di archiviazione di Microsoft Azure]
-   [Librerie di Microsoft Azure per .NET][Librerie di Microsoft Azure per .NET]
-   [Componente aggiuntivo di pubblicazione Azure LightSwitch per Visual Studio][Componente aggiuntivo di pubblicazione Azure LightSwitch per Visual Studio]

### <span id="vwd"></span></a>Visual Studio Express per il Web

Se nel computer non è installato Visual Studio, l'SDK installerà [Visual Studio Express per il Web][1].

### <span id="wte"></span></a>Microsoft ASP.NET e strumenti Web per Visual Studio

Consente di eseguire le attività seguenti con Siti Web di Azure:

-   [Pubblicare progetti Web in Siti Web di Azure][Pubblicare progetti Web in Siti Web di Azure].
-   [Pubblicare progetti di applicazioni console in Azure WebJobs][Pubblicare progetti di applicazioni console in Azure WebJobs].
-   [Creare risorse di Siti Web di Azure e di database SQL durante la creazione o la pubblicazione di un nuovo progetto Web][Creare risorse di Siti Web di Azure e di database SQL durante la creazione o la pubblicazione di un nuovo progetto Web].
-   [Creare script di distribuzione di PowerShell durante la creazione di nuovi siti Web][Creare script di distribuzione di PowerShell durante la creazione di nuovi siti Web].
-   [Gestire e risolvere i problemi di Siti Web di Azure in Esplora server][Gestire e risolvere i problemi di Siti Web di Azure in Esplora server].
-   [Condurre esecuzioni remote in modalità debug per Siti Web e WebJobs][Condurre esecuzioni remote in modalità debug per Siti Web e WebJobs].

> [WACOM.NOTE] Non è necessario installare Azure SDK per .NET per usare queste funzionalità, in quanto incluse negli aggiornamenti di Visual Studio.

### <span id="tools"></span></a>Strumenti di Microsoft Azure per Microsoft Visual Studio

Consentono di lavorare con Servizi cloud di Azure e Macchine virtuali:

-   [Creare, aprire e pubblicare progetti di servizi cloud][Creare, aprire e pubblicare progetti di servizi cloud].
-   [Creare pacchetti di distribuzione per progetti di servizi cloud][Creare pacchetti di distribuzione per progetti di servizi cloud].
-   [Creare macchine virtuali di Azure durante la creazione di nuovi progetti Web][Creare macchine virtuali di Azure durante la creazione di nuovi progetti Web].
-   [Creare script di PowerShell durante la creazione di nuove macchine virtuali][Creare script di distribuzione di PowerShell durante la creazione di nuovi siti Web].
-   [Visualizzare e gestire impostazioni di progetti di servizi cloud nelle finestre delle proprietà dei progetti di Visual Studio][Visualizzare e gestire impostazioni di progetti di servizi cloud nelle finestre delle proprietà dei progetti di Visual Studio].
-   Visualizzare e gestire [servizi cloud][servizi cloud], [macchine virtuali][macchine virtuali] e [bus di servizio][bus di servizio] in Esplora server.
-   [Condurre esecuzioni remote in modalità debug per servizi cloud e macchine virtuali][Condurre esecuzioni remote in modalità debug per servizi cloud e macchine virtuali].

### <span id="auth"></span></a>Strumenti di creazione di Microsoft Azure

Sono inclusi i componenti seguenti:

-   Strumento da riga di comando CSPack[per la creazione di pacchetti di distribuzione.][per la creazione di pacchetti di distribuzione.]
-   Strumento da riga di comando CSEncrypt[per la crittografia di password usate per accedere a istanze del ruolo di servizi cloud tramite una connessione Desktop remoto.][per la crittografia di password usate per accedere a istanze del ruolo di servizi cloud tramite una connessione Desktop remoto.]
-   File binari di runtime richiesti dai progetti di servizi cloud per comunicare con i relativi ambienti di runtime e per la diagnostica. Questi file binari non sono disponibili in pacchetti NuGet.

### <span id="emulator"></span></a>Emulatore di Microsoft Azure

L'[Emulatore di Azure][Emulatore di Azure] simula l'ambiente del servizio cloud per consentire il test dei progetti di servizi cloud nel computer locale prima di distribuirli in Azure.

### <span id="stgemulator"></span></a>Emulatore di archiviazione di Microsoft Azure

L'[Emulatore di archiviazione di Azure][Emulatore di archiviazione di Azure] usa un'istanza di SQL Server e il file system locale per simulare l'archiviazione di Azure (code, tabelle, BLOB) per consentirne il test in locale.

### <span id="stgtools"></span></a>Strumenti di archiviazione di Microsoft Azure

Viene installato [AzCopy][AzCopy], uno strumento da riga di comando che può essere usato per trasferire dati da e verso un account di archiviazione di Azure.

### <span id="libraries"></span></a>Librerie di Microsoft Azure per .NET

Sono inclusi i componenti seguenti:

-   Pacchetti NuGet per archiviazione di Azure, bus di servizio e cache che vengono archiviati nel computer affinché Visual Studio possa creare nuovi progetti di servizi cloud mentre si è offline.
-   Un plug-in di Visual Studio che consente l'esecuzione locale in Visual Studio dei progetti [Cache nel ruolo][Cache nel ruolo].

### <span id="ls"></span></a>Componente aggiuntivo di pubblicazione Azure LightSwitch per Visual Studio

Consente di [pubblicare progetti LightSwitch in Siti Web di Azure][pubblicare progetti LightSwitch in Siti Web di Azure]. Il componente aggiuntivo LightSwitch è incluso negli aggiornamenti di Visual Studio insieme ad Azure SDK per .NET. L'installazione dell'SDK garantisce che si stia usando la versione più recente del componente aggiuntivo.

## <span id="notincluded"></span></a>Cosa non è incluso nell'installazione di Azure SDK per .NET

Vi sono alcuni componenti che potrebbero essere necessari per lo sviluppo in Azure ma che non sono inclusi nell'installazione dell'SDK. I più importanti sono i seguenti:

-   [Librerie client][NuGet].

    Azure SDK include librerie client per l'uso di servizi di Azure, ma non tutte vengono installate quando si installa l'SDK. Se l'applicazione necessita di una libreria client non installata dall'SDK, è possibile ottenerla da [NuGet.org][NuGet]. Se l'applicazione usa una libreria client che non viene installata dall'SDK, è consigliabile eseguirne l'aggiornamento alla versione corrente dal sito NuGet.org.

    **Copie locali delle librerie client.** Azure SDK per .NET copia nel computer i pacchetti NuGet per alcune librerie client di Azure, come quelle relative ad archiviazione, bus di servizio e cache. Queste librerie client vengono automaticamente incluse nei nuovi progetti di servizi cloud, in modo che i pacchetti NuGet locali abilitino Visual Studio alla creazione di progetti anche qualora non si sia connessi a Internet. Le librerie client vengono generalmente aggiornate con maggiore frequenza rispetto a quella dei rilasci di nuove versioni dell'SDK, pertanto le librerie client in NuGet.org sono spesso più aggiornate di quelle presenti nell'SDK più recente.

    **Modelli di progetto che includono librerie client.** Solo i modelli di progetto per [Servizi cloud di Azure][Creare, aprire e pubblicare progetti di servizi cloud] e [Servizi mobili di Azure][Servizi mobili di Azure] includono automaticamente alcune librerie client. Per altre librerie o altri modelli, installare i [pacchetti di librerie client NuGet][NuGet] necessari.

-   [Azure PowerShell][Azure PowerShell].

    Azure PowerShell consente di [automatizzare la creazione e la distribuzione dell'ambiente Azure][automatizzare la creazione e la distribuzione dell'ambiente Azure].

-   [Modelli di progetto di Servizi mobili di Azure][Servizi mobili di Azure].

    I modelli per servizi mobili sono disponibili solo in Visual Studio 2013 Update 2 e versioni successive. Non sono disponibili in Visual Studio 2012 o versioni precedenti né in Visual Studio 2013 Update 1 o precedenti, anche se si installa Azure SDK per .NET.

## <span id="faq"></span></a>Domande frequenti

-   [Molte funzionalità di Azure sono già presenti in Visual Studio. È necessario installare Azure SDK per .NET?][Molte funzionalità di Azure sono già presenti in Visual Studio. È necessario installare Azure SDK per .NET?]
-   [Occorre una libreria client. È necessario installare Azure SDK per .NET per ottenerla?][Occorre una libreria client. È necessario installare Azure SDK per .NET per ottenerla?]
-   [Dove è possibile trovare versioni precedenti di Azure SDK per .NET?][Dove è possibile trovare versioni precedenti di Azure SDK per .NET?]
-   [Qual è il criterio relativo al ciclo di vita di Azure SDK per .NET?][Qual è il criterio relativo al ciclo di vita di Azure SDK per .NET?]
-   [Con quali versioni di sistema operativo guest è compatibile Azure SDK per .NET?][Con quali versioni di sistema operativo guest è compatibile Azure SDK per .NET?]

### <span id="azinvs"></span></a>Molte funzionalità di Azure sono già presenti in Visual Studio. È necessario installare Azure SDK per .NET?

È buona norma installare l'SDK se si vuole sviluppare per Azure usando gli strumenti più aggiornati. Se si preferisce non installare l'SDK, è possibile evitarne l'installazione solo in presenza delle condizioni seguenti:

-   È stato installato l'[aggiornamento di Visual Studio][aggiornamento di Visual Studio] più recente.
-   Si sta sviluppando solo per Siti Web di Azure o Servizi mobili di Azure, non per Servizi cloud di Azure o Macchine virtuali di Azure.
-   L'applicazione non usa archiviazione, oppure, se la usa, non sono necessari l'emulatore di archiviazione né lo strumento AzCopy.

### <span id="clientlib"></span></a>Occorre una libreria client. È necessario installare Azure SDK per .NET per ottenerla?

L'SDK installa le librerie client solo per consentire la creazione di progetti di servizi cloud anche nei casi in cui non si sia connessi a Internet. Le librerie attuali sono disponibili in pacchetti NuGet presso [NuGet.org][NuGet]. Per altre informazioni, vedere [Cosa non è incluso nell'installazione di Azure SDK per .NET][Cosa non è incluso nell'installazione di Azure SDK per .NET] più indietro in questo documento.

### <span id="olderversions"></span></a>Dove è possibile trovare versioni precedenti di Azure SDK per .NET?

Per versioni precedenti, vedere la pagina di download di [Azure SDK per .NET][Azure SDK per .NET].

### <span id="lifecycle"></span></a>Qual è il criterio relativo al ciclo di vita di Azure SDK per .NET?

Vedere [Ciclo di vita del supporto per i servizi cloud di Microsoft Azure][Ciclo di vita del supporto per i servizi cloud di Microsoft Azure].

### <span id="guestos"></span></a>Con quali versioni di sistema operativo guest è compatibile Azure SDK per .NET?

Vedere [Rilasci del sistema operativo guest Azure e matrice di compatibilità SDK][Rilasci del sistema operativo guest Azure e matrice di compatibilità SDK].

## <span id="resources"></span></a>Risorse

Per scaricare Azure SDK per .NET o una libreria client, vedere la [pagina di download di Azure][pagina dei download di Azure].

Per il codice sorgente di Azure SDK per .NET, comprese le librerie client, vedere [GitHub.com/Azure][GitHub.com/Azure].

Per la documentazione di riferimento sulle librerie client di Azure, vedere [Informazioni di riferimento su .NET per Azure][Informazioni di riferimento su .NET per Azure].

  [pagina dei download di Azure]: /it-it/downloads/
  [NuGet]: http://go.microsoft.com/fwlink/?LinkId=510472
  [Cosa è incluso nell'installazione di Azure SDK per .NET]: #included
  [Cosa non è incluso nell'installazione di Azure SDK per .NET]: #notincluded
  [Domande frequenti]: #faq
  [Risorse]: #resources
  [Visual Studio Express per il Web]: #vwd
  [Microsoft ASP.NET e strumenti Web per Visual Studio]: #wte
  [Strumenti di Microsoft Azure per Microsoft Visual Studio]: #tools
  [Strumenti di creazione di Microsoft Azure]: #auth
  [Emulatore di Microsoft Azure]: #emulator
  [Emulatore di archiviazione di Microsoft Azure]: #stgemulator
  [Strumenti di archiviazione di Microsoft Azure]: #stgtools
  [Librerie di Microsoft Azure per .NET]: #libraries
  [Componente aggiuntivo di pubblicazione Azure LightSwitch per Visual Studio]: #ls
  [1]: http://www.visualstudio.com/it-it/products/visual-studio-express-vs.aspx
  [Pubblicare progetti Web in Siti Web di Azure]: ../web-sites-dotnet-get-started/
  [Pubblicare progetti di applicazioni console in Azure WebJobs]: ../websites-dotnet-deploy-webjobs/
  [Creare risorse di Siti Web di Azure e di database SQL durante la creazione o la pubblicazione di un nuovo progetto Web]: ../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Creare script di distribuzione di PowerShell durante la creazione di nuovi siti Web]: http://msdn.microsoft.com/it-it/library/dn642480.aspx
  [Gestire e risolvere i problemi di Siti Web di Azure in Esplora server]: ../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement
  [Condurre esecuzioni remote in modalità debug per Siti Web e WebJobs]: ../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [Creare, aprire e pubblicare progetti di servizi cloud]: ../cloud-services-dotnet-get-started/
  [Creare pacchetti di distribuzione per progetti di servizi cloud]: http://msdn.microsoft.com/it-it/library/ff683672.aspx
  [Creare macchine virtuali di Azure durante la creazione di nuovi progetti Web]: ../virtual-machines-dotnet-create-visual-studio-powershell/
  [Visualizzare e gestire impostazioni di progetti di servizi cloud nelle finestre delle proprietà dei progetti di Visual Studio]: http://msdn.microsoft.com/it-it/library/ee405486.aspx
  [servizi cloud]: http://msdn.microsoft.com/it-it/library/ff683675.aspx
  [macchine virtuali]: http://msdn.microsoft.com/it-it/library/jj131259.aspx
  [bus di servizio]: http://msdn.microsoft.com/it-it/library/jj149828.aspx
  [Condurre esecuzioni remote in modalità debug per servizi cloud e macchine virtuali]: http://msdn.microsoft.com/it-it/library/ff683670.aspx
  [per la creazione di pacchetti di distribuzione.]: http://msdn.microsoft.com/it-it/library/gg432988.aspx
  [per la crittografia di password usate per accedere a istanze del ruolo di servizi cloud tramite una connessione Desktop remoto.]: http://msdn.microsoft.com/it-it/library/hh404001.aspx
  [Emulatore di Azure]: http://msdn.microsoft.com/it-it/library/dn339018.aspx
  [Emulatore di archiviazione di Azure]: http://msdn.microsoft.com/it-it/library/hh403989.aspx
  [AzCopy]: http://aka.ms/AzCopy
  [Cache nel ruolo]: http://msdn.microsoft.com/it-it/library/dn386103.aspx
  [pubblicare progetti LightSwitch in Siti Web di Azure]: http://msdn.microsoft.com/it-it/library/jj131261.aspx
  [Servizi mobili di Azure]: ../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/
  [Azure PowerShell]: ../install-configure-powershell/
  [automatizzare la creazione e la distribuzione dell'ambiente Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Molte funzionalità di Azure sono già presenti in Visual Studio. È necessario installare Azure SDK per .NET?]: #azinvs
  [Occorre una libreria client. È necessario installare Azure SDK per .NET per ottenerla?]: #clientlib
  [Dove è possibile trovare versioni precedenti di Azure SDK per .NET?]: #olderversions
  [Qual è il criterio relativo al ciclo di vita di Azure SDK per .NET?]: #lifecycle
  [Con quali versioni di sistema operativo guest è compatibile Azure SDK per .NET?]: #guestos
  [aggiornamento di Visual Studio]: http://www.visualstudio.com/it-it/downloads/download-visual-studio-vs#DownloadFamilies_5
  [Azure SDK per .NET]: /it-it/downloads/archive-net-downloads/
  [Ciclo di vita del supporto per i servizi cloud di Microsoft Azure]: http://support.microsoft.com/gp/azure-cloud-lifecycle-faq
  [Rilasci del sistema operativo guest Azure e matrice di compatibilità SDK]: http://msdn.microsoft.com/it-it/library/ee924680.aspx
  [GitHub.com/Azure]: https://github.com/azure/
  [Informazioni di riferimento su .NET per Azure]: /it-it/develop/net/reference/
