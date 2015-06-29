<properties 
	pageTitle="Strumenti e librerie di sviluppo di Azure Batch" 
	description="Con questi strumenti e librerie è possibile sviluppare applicazioni di Azure Batch e Batch Apps." 
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
<p> Con i seguenti strumenti e librerie è possibile sviluppare applicazioni di Azure Batch e Batch Apps.

## Batch

+ [Libreria client di Azure Batch per .NET](http://www.nuget.org/packages/Azure.Batch/) \(NuGet\): sviluppare applicazioni client per eseguire processi con il servizio Batch.
+ [Libreria di gestione Batch](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) \(NuGet\): gestire gli account del servizio Batch.
+ [Batch Explorer](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) \(MSDN\): applicazione GUI ed esempio per sfogliare, accedere e aggiornare gli elementi principali in un account Batch, inclusi processi e attività, macchine virtuali delle attività \(TVM, Task Virtual Machine\) e pool e file in una TVM.

> [AZURE.TIP]Batch Explorer è uno strumento eccezionale se non si ha familiarità con Batch o si vuole monitorare o risolvere i problemi delle attività di Batch. Poiché si tratta di un esempio di codice, il codice sorgente mostra come in che modo vengono implementate le funzionalità mediante l'API .NET di Batch. Vedere il [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Batch Apps

+ [Batch Apps Cloud SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) \(NuGet\): abilitare le applicazioni per l'offload dei processi al servizio Batch.
+ [Estensione di Visual Studio per Batch Apps](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) \(Visual Studio Gallery\): abilitare per il cloud le applicazioni in Visual Studio tramite Batch Apps Cloud SDK.
+ [Batch Apps Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) \(NuGet\): interagire con applicazioni abilitate per l'offload di processi nel servizio Batch.
+ [Client Python per Batch Apps](https://github.com/Azure/azure-batch-apps-python) \(GitHub\): modulo del client Python per l'interazione con applicazioni configurate in un servizio Batch Apps.
+ [Esempio Blender per Batch Apps](https://github.com/Azure/azure-batch-apps-blender) \(GitHub\): componente aggiuntivo del software open source per il rendering Blender che usa Batch Apps SDK e il client Python per configurare una piattaforma di rendering basata sul cloud.


## Risorse aggiuntive

+ [Esempi di codice](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) \(MSDN\)
+ [Forum di Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=58_postMigration-->