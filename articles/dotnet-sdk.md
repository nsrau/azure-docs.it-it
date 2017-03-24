---
title: Informazioni su Azure .NET SDK
description: Informazioni sui contenuti di Azure .NET SDK.
documentationcenter: .net
author: chrissfanos
editor: mollybos
services: 
ms.assetid: f70279ac-7c56-4d6a-9882-352a8b644a4b
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/30/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 5f61a5422fc36b2468585e601823f292680142ec
ms.lasthandoff: 11/17/2016


---
# <a name="what-is-the-azure-sdk-for-net"></a>Informazioni su Azure SDK per .NET
## <a name="overview"></a>Panoramica
Azure SDK per .NET è un set di strumenti di Visual Studio, strumenti da riga di comando, file binari di runtime e librerie client che consente di sviluppare, testare e distribuire app eseguite in Azure. Questo articolo illustra le funzionalità ottenute quando si installa l'SDK. È possibile scaricare l'SDK dalla [pagina dei download di Azure](https://azure.microsoft.com/downloads/).

Azure SDK per .NET include anche [librerie client per l'uso di servizi di Azure](http://go.microsoft.com/fwlink/?LinkId=510472). Tali librerie vanno installate separatamente mediante NuGet.

## <a id="included"></a>Cosa è incluso in Azure SDK per .NET
Azure SDK per .NET installa i prodotti seguenti:

* [Visual Studio Community Edition 2015](#vwd)
* [Emulatore di archiviazione di Microsoft Azure](#stgemulator)
* [Strumenti di archiviazione di Microsoft Azure](#stgtools)
* [Strumenti di creazione di Microsoft Azure](#auth)
* [Emulatore di Microsoft Azure](#emulator)
* [Strumenti HDInsight per Visual Studio e Microsoft Hive ODBC Driver](#hdinsight)
* [Librerie di Microsoft Azure per .NET](#libraries)
* [SDK di App per dispositivi mobili di Microsoft Azure](#mobile)
* [Microsoft Azure PowerShell](#ps)
* [Strumenti di Microsoft Azure per Microsoft Visual Studio](#tools)
* [Microsoft ASP.NET e strumenti Web per Visual Studio](#wte)
* [Strumenti di Azure Data Lake per Visual Studio](#datalake)

### <a id="vwd"></a>Visual Studio Community Edition 2015
Se nel computer non è installato Visual Studio, l'SDK installerà [Visual Studio Community Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a id="stgemulator"></a>Emulatore di archiviazione di Microsoft Azure
L' [Emulatore di archiviazione di Azure](http://msdn.microsoft.com/library/hh403989.aspx) usa un'istanza di SQL Server e il file system locale per simulare l'archiviazione di Azure (code, tabelle, BLOB) per consentirne il test in locale.

### <a id="stgtools"></a>Strumenti di archiviazione di Microsoft Azure
Viene installato [AzCopy](http://aka.ms/AzCopy), uno strumento da riga di comando che può essere usato per trasferire dati da e verso un account di archiviazione di Azure.

### <a id="auth"></a>Strumenti di creazione di Microsoft Azure
Sono inclusi i componenti seguenti:

* Strumento da riga di comando CSPack [per la creazione di pacchetti di distribuzione.](http://msdn.microsoft.com/library/gg432988.aspx)
* Strumento da riga di comando CSEncrypt [per la crittografia di password usate per accedere a istanze del ruolo di servizi cloud tramite una connessione Desktop remoto.](http://msdn.microsoft.com/library/hh404001.aspx)
* File binari di runtime richiesti dai progetti di servizi cloud per comunicare con i relativi ambienti di runtime e per la diagnostica. Questi file binari non sono disponibili in pacchetti NuGet.

### <a id="emulator"></a>Emulatore di Microsoft Azure
L' [Emulatore di Azure](http://msdn.microsoft.com/library/dn339018.aspx) simula l'ambiente del servizio cloud per consentire il test dei progetti di servizi cloud nel computer locale prima di distribuirli in Azure.

### <a id="hdinsight"></a>HDInsight Tools per Visual Studio e Microsoft Hive ODBC Driver
Gli strumenti HDInsight in Esplora Server consentono di esplorare i database Hive e gli account di archiviazione collegati per i cluster HDInsight, di creare tabelle e di creare e inviare query Hive. Per altre informazioni, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

### <a id="libraries"></a>Librerie di Microsoft Azure per .NET
Sono inclusi i componenti seguenti:

* Pacchetti NuGet per archiviazione di Azure, bus di servizio e cache che vengono archiviati nel computer affinché Visual Studio possa creare nuovi progetti di servizi cloud mentre si è offline.
* Un plug-in di Visual Studio che consente l'esecuzione locale in Visual Studio dei progetti [Cache nel ruolo](http://msdn.microsoft.com/library/dn386103.aspx) .

### <a id="mobile"></a>SDK di App per dispositivi mobili di Microsoft Azure
Strumenti per lavorare con le [app per dispositivi mobili in Azure App Service](app-service-mobile/app-service-mobile-value-prop.md).

### <a id="ps"></a>Microsoft Azure PowerShell
Azure PowerShell consente di [automatizzare la creazione e la distribuzione dell'ambiente Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

### <a id="tools"></a>Strumenti di Microsoft Azure per Microsoft Visual Studio
Consentono di lavorare con le risorse di Azure, soprattutto Servizi cloud e Macchine virtuali:

* [Creare, aprire e pubblicare progetti di servizi cloud](cloud-services/cloud-services-dotnet-get-started.md).
* [Creare pacchetti di distribuzione per progetti di servizi cloud](http://msdn.microsoft.com/library/ff683672.aspx).
* [Creare macchine virtuali di Azure durante la creazione di nuovi progetti Web](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* [Creare script di PowerShell durante la creazione di nuove macchine virtuali](http://msdn.microsoft.com/library/dn642480.aspx).
* [Visualizzare e gestire impostazioni di progetti di servizi cloud nelle finestre delle proprietà dei progetti di Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Visualizzare e gestire [servizi cloud](http://msdn.microsoft.com/library/ff683675.aspx), [macchine virtuali](http://msdn.microsoft.com/library/jj131259.aspx) e [bus di servizio](http://msdn.microsoft.com/library/jj149828.aspx) in Esplora server.
* [Condurre esecuzioni remote in modalità debug per servizi cloud e macchine virtuali](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatizzare il provisioning di risorse tramite i progetti di distribuzione dei gruppi di risorse di Azure](https://msdn.microsoft.com/library/dn872471.aspx)

### <a id="wte"></a>Strumenti per il servizio App di Microsoft per Visual Studio
Consente di eseguire le attività seguenti con Siti Web di Azure:

* [Pubblicare progetti Web in Siti Web di Azure](app-service-web/web-sites-dotnet-get-started.md).
* [Pubblicare progetti di applicazioni console in Azure WebJobs](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Creare risorse di Siti Web di Azure e di database SQL durante la creazione o la pubblicazione di un nuovo progetto Web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [Creare script di distribuzione di PowerShell durante la creazione di nuovi siti Web](http://msdn.microsoft.com/library/dn642480.aspx).
* [Gestire e risolvere i problemi di Siti Web di Azure in Esplora server](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Condurre esecuzioni remote in modalità debug per Siti Web e WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug).

> [!NOTE]
> Non è necessario installare Azure SDK per .NET per usare queste funzionalità, in quanto incluse negli aggiornamenti di Visual Studio.
> 
> 

## <a id="datalake"></a>Strumenti di Azure Data Lake per Visual Studio
Per altre informazioni, vedere [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a id="notincluded"></a>Cosa non è incluso nell'installazione di Azure SDK per .NET
Vi sono alcuni componenti che potrebbero essere necessari per lo sviluppo in Azure ma che non sono inclusi nell'installazione dell'SDK. I più importanti sono i seguenti:

* [Librerie client](http://go.microsoft.com/fwlink/?LinkId=510472).
  
    Azure SDK include librerie client per l'uso di servizi di Azure, ma non tutte vengono installate quando si installa l'SDK. Se l'applicazione necessita di una libreria client non installata dall'SDK, è possibile ottenerla da [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Se l'applicazione usa una libreria client che non viene installata dall'SDK, è consigliabile eseguirne l'aggiornamento alla versione corrente dal sito NuGet.org.
  
      **Local copies of client libraries.** The Azure SDK for .NET copies to your computer the NuGet packages for some Azure client libraries, such as Storage, Service Bus, and Caching. These client libraries are automatically included in new cloud service projects, so the local NuGet packages enable Visual Studio to create projects even if you're not connected to the Internet. Client libraries are generally updated more frequently than new SDK versions are released, so the client libraries at NuGet.org are often more current than what you get with the SDK.
  
    **Modelli di progetto che includono librerie client.** Solo i modelli di progetto per [Servizi cloud di Microsoft Azure](cloud-services/cloud-services-dotnet-get-started.md) e [App per dispositivi mobili](app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) del servizio app di Azure includono automaticamente alcune librerie client. Per altre librerie o altri modelli, installare i [pacchetti di librerie client NuGet](http://go.microsoft.com/fwlink/?LinkId=510472) necessari.
* [Modelli di progetto per app per dispositivi mobili](app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
  
    I modelli di app per dispositivi mobili sono disponibili solo in Visual Studio 2013 Update 2 e versioni successive. Non sono disponibili in Visual Studio 2012 o versioni precedenti né in Visual Studio 2013 Update 1 o precedenti, anche se si installa Azure SDK per .NET.

## <a id="faq"></a>Domande frequenti
* [Molte funzionalità di Azure sono già presenti in Visual Studio. È necessario installare Azure SDK per .NET?](#azinvs)
* [Occorre una libreria client. È necessario installare Azure SDK per .NET per ottenerla?](#clientlib)
* [Dove è possibile trovare versioni precedenti di Azure SDK per .NET?](#olderversions)
* [Qual è il criterio relativo al ciclo di vita di Azure SDK per .NET?](#lifecycle)
* [Con quali versioni di sistema operativo guest è compatibile Azure SDK per .NET?](#guestos)
* [Come si disinstalla Azure SDK per .NET?](#uninstall)

### <a id="azinvs"></a>Molte funzionalità di Azure sono già presenti in Visual Studio. È necessario installare Azure SDK per .NET?
È buona norma installare l'SDK se si vuole sviluppare per Azure usando gli strumenti più aggiornati. Se si preferisce non installare l'SDK, è possibile evitarne l'installazione solo in presenza delle condizioni seguenti:

* È stato installato l' [aggiornamento di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5)più recente.
* Si sta sviluppando solo per Siti Web di Azure o Servizi mobili di Azure, non per Servizi cloud di Azure o Macchine virtuali di Azure.
* L'applicazione non usa archiviazione, oppure, se la usa, non sono necessari l'emulatore di archiviazione né lo strumento AzCopy.

### <a id="clientlib"></a>Occorre una libreria client. È necessario installare Azure SDK per .NET per ottenerla?
L'SDK installa le librerie client solo per consentire la creazione di progetti di servizi cloud anche nei casi in cui non si sia connessi a Internet. Le librerie attuali sono disponibili in pacchetti NuGet presso [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Per altre informazioni, vedere [Cosa non è incluso nell'installazione di Azure SDK per .NET](#notincluded) più indietro in questo documento.

### <a id="olderversions"></a>Dove è possibile trovare versioni precedenti di Azure SDK per .NET?
Per versioni precedenti, vedere la pagina di download di [Azure SDK per .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) .

### <a id="lifecycle"></a>Qual è il criterio relativo al ciclo di vita di Azure SDK per .NET?
Vedere [Ciclo di vita del supporto per i servizi cloud di Microsoft Azure](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

### <a id="guestos"></a>Con quali versioni di sistema operativo guest è compatibile Azure SDK per .NET?
Vedere [Rilasci del sistema operativo guest Azure e matrice di compatibilità SDK](http://msdn.microsoft.com/library/ee924680.aspx).

### <a id="uninstall"></a>Come si disinstalla Azure SDK per .NET?
Ogni elemento visualizzato in questo articolo nella sezione [Cosa è incluso in Azure SDK per .NET](#included) è un programma separato presente nell'elenco dei programmi installati in **Programmi e funzionalità**nel Pannello di controllo di Windows.  Non è possibile disinstallare tali elementi come gruppo, è necessario disinstallare singolarmente ciascun programma.

Se Azure SDK per .NET è già installato e si installa una nuova versione, in genere non è necessario disinstallare la versione precedente. Nella maggior parte dei casi, l'installazione di SDK aggiorna un programma esistente anziché aggiungerne uno nuovo lasciando il precedente.

Tuttavia, se si vuole rimuovere residui di una versione precedente non più necessari, disinstallare solo i programmi per cui è specificato il numero di versione precedente e disinstallarli solo se è presente se lo stesso programma con una versione più recente. Ad esempio, dopo l'aggiornamento da 2.5 a 2.6, è possibile che vengano visualizzate le versioni 2.5 e 2.6 di "Strumenti di Microsoft Azure per Microsoft Visual Studio 2013" ed è possibile disinstallare la versione 2.5. Tuttavia, è anche possibile che venga visualizzata solo la versione 2.5 di "Strumenti di creazione di Microsoft Azure" e non sarebbe opportuno disinstallarla.

I numeri di versione nei titoli dei programmi visualizzati in **Programmi e funzionalità** possono essere fuorvianti.  SDK versione 2.6 include, ad esempio, "SDK di App per dispositivi mobili di Microsoft Azure V1.0" se si installa l’SDK per Visual Studio 2013 e "SDK di App per dispositivi mobili di Microsoft Azure V2.0" per Visual Studio 2015. In questo caso la versione non è la versione dell’SDK ma un indicatore della versione di Visual Studio applicata dal programma.

In questo articolo non vengono elencati tutti i programmi inclusi in tutte le versioni precedenti di Azure SDK. Esistono altri programmi che è possibile disinstallare dalle versioni precedenti di SDK, perché le versioni precedenti di SDK talvolta includono programmi che sono stati omessi dalle versioni successive. La versione 2.5, ad esempio, consente di installare "Strumenti di Azure Resource Manager per Visual Studio" ma non si trova nell'elenco perché non viene più visualizzata come programma separato in **Programmi e funzionalità**.  In questo articolo vengono elencati solo i programmi inclusi in Azure SDK per .NET versione 2.6.

> **Nota:** alcuni dei programmi inclusi nell’SDK possono essere installati anche separatamente in altri contesti e potrebbero essere necessari anche se non è necessario l'intero SDK. Lo stesso concetto può essere valido per i programmi che sono stati installati dalle versioni precedenti di SDK ma sono stati omessi dalle versioni successive di SDK. Quando si disinstallano i programmi, evitare di rimuovere un elemento che è ancora necessario nel computer.
> 
> 

## <a id="versions"></a>Versioni
Per verificare la versione corrente o per scaricare le versioni precedenti, vedere la pagina [Cronologia delle versioni di Azure SDK per .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) .

## <a id="resources"></a>Risorse
Per scaricare la versione corrente di Azure SDK per .NET o una libreria client, vedere la pagina di [download di Azure](https://azure.microsoft.com/downloads/).

Per il codice sorgente di Azure SDK per .NET, comprese le librerie client, vedere [GitHub.com/Azure](https://github.com/azure/).

Per la documentazione di riferimento sulle librerie client di Azure, vedere [Informazioni di riferimento su .NET per Azure](https://azure.microsoft.com/documentation/api/).


