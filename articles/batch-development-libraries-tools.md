<properties 
	pageTitle="Strumenti e librerie di sviluppo di Azure Batch" 
	description="Ottenere le librerie e strumenti che necessari per sviluppare applicazioni Azure Batch e applicazioni di Batch" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Strumenti e librerie di sviluppo di Azure Batch 
<p> Ottenere le librerie e strumenti per lo sviluppo di applicazioni Azure Batch e applicazioni di Batch.

## Batch

+ [Batch nella libreria Client per .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet): sviluppo di applicazioni client per eseguire i processi con il servizio di Batch
+ [Libreria per la gestione batch](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – gli account del servizio di gestione di Batch
+ [Explorer batch](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN): esempio e applicazione GUI di esplorare, accedere e aggiornare gli elementi principali all'interno di un account di Batch, inclusi i processi e attività, macchine virtuali di attività (TVMs) e pool e i file a una TVM.

> [AZURE.TIP]Explorer batch è un ottimo strumento se si ha familiarità con il Batch o da monitorare o risolvere i problemi di attività Batch. Poiché è un esempio di codice, il codice sorgente viene illustrato come le funzionalità sono implementati utilizzando l'API .NET di Batch. Vedere il [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Batch Apps

+ [Batch applicazioni Cloud SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) – consentono alle applicazioni di ripartire i processi per il servizio di Batch
+ [Batch applicazioni Visual Studio Extension](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio Gallery) – enable Cloud applicazioni in Visual Studio tramite il SDK di Batch applicazioni Cloud
+ [Batch App Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – interagire con applicazioni abilitate per ripartire i processi per il servizio di Batch
+ [Batch App Python Client](https://github.com/Azure/azure-batch-apps-python) (GitHub) - modulo client Python per interagire con le applicazioni configurate in un servizio App Batch
+ [Esempio Blender Apps batch](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Addon per Blender software open source per il rendering che utilizza il client SDK applicazioni Batch e Python per impostare una piattaforma per il rendering basato su cloud


## Risorse aggiuntive

+ [Esempi di codice](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Forum di Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Iniziare con la libreria di Batch di Azure per .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=GIT-SubDir-->