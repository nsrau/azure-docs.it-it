<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Utilizzo dei contatori di prestazioni in Azure

È possibile usare i contatori delle prestazioni in un'applicazione Azure per raccogliere dati che contribuiscono a individuare i colli di bottiglia e a ottimizzare le prestazioni di sistema e dell'applicazione. I contatori delle prestazioni disponibili per Windows Server 2008, Windows Server 2012, IIS e ASP.NET possono essere utilizzati per raccogliere dati e per determinare l'integrità dell'applicazione Azure.

È possibile configurare i contatori delle prestazioni prima della distribuzione o in fase di esecuzione in Visual Studio 2012 o Visual Studio 2013 mediante Azure SDK 2.0 o versioni successive. Per altre informazioni, vedere [Configurazione di Diagnostica Azure][Configurazione di Diagnostica Azure]. Per informazioni sulla configurazione manuale dei contatori delle prestazioni nell'applicazione, vedere [Come configurare i contatori delle prestazioni][Come configurare i contatori delle prestazioni].

Per altre linee guida dettagliate sulla creazione di una strategia di registrazione e traccia e sull'utilizzo della diagnostica e di altre tecniche per risolvere i problemi e ottimizzare le applicazioni Azure, vedere [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure][Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure].

## <a name="nextsteps"> </a>Passaggi successivi

Usare i collegamenti seguenti per informazioni su come implementare scenari più complessi per la risoluzione dei problemi.

-   [Test delle prestazioni di un servizio cloud][Test delle prestazioni di un servizio cloud]
-   [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure][Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure]
-   [Come monitorare i servizi cloud][Come monitorare i servizi cloud]
-   [Come usare il blocco applicazione per la scalabilità automatica][Come usare il blocco applicazione per la scalabilità automatica]
-   [Compilazione di applicazioni cloud elastiche e resilienti][Compilazione di applicazioni cloud elastiche e resilienti]

## <a name="additional"> </a>Risorse aggiuntive

-   [Abilitazione della diagnostica in Azure][Abilitazione della diagnostica in Azure]
-   [Raccogliere dati di registrazione usando Diagnostica Azure][Raccogliere dati di registrazione usando Diagnostica Azure]
-   [Debug di un'applicazione Azure][Debug di un'applicazione Azure]

  [Configurazione di Diagnostica Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/dn186185.aspx
  [Come configurare i contatori delle prestazioni]: http://msdn.microsoft.com/it-it/library/azure/dn535595.aspx
  [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/hh771389.aspx
  [Test delle prestazioni di un servizio cloud]: http://msdn.microsoft.com/it-it/library/azure/hh369930.aspx
  [Come monitorare i servizi cloud]: http://azure.microsoft.com/it-it/documentation/articles/cloud-services-how-to-monitor/
  [Come usare il blocco applicazione per la scalabilità automatica]: http://azure.microsoft.com/it-it/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [Compilazione di applicazioni cloud elastiche e resilienti]: http://msdn.microsoft.com/it-it/library/hh680949(PandP.50).aspx
  [Abilitazione della diagnostica in Azure]: http://azure.microsoft.com/it-it/documentation/articles/cloud-services-dotnet-diagnostics/
  [Raccogliere dati di registrazione usando Diagnostica Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433048.aspx
  [Debug di un'applicazione Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/ee405479.aspx
