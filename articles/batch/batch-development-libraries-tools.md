<properties
	pageTitle="Strumenti e librerie di sviluppo di Azure Batch | Microsoft Azure"
	description="Con questi strumenti e librerie è possibile sviluppare applicazioni di Azure Batch."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Strumenti e librerie di sviluppo di Azure Batch
<p> Con questi strumenti e librerie è possibile sviluppare applicazioni di Azure Batch.

## Batch

+ [Libreria client di Azure Batch per .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet): sviluppare applicazioni client per eseguire processi con il servizio Batch.
+ [Libreria di gestione Batch](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet): gestire gli account Batch.
+ [Batch Explorer](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub): applicazione GUI ed esempio per sfogliare, accedere e aggiornare gli elementi principali in un account Batch, inclusi processi e attività, nodi di calcolo e pool, oltre a file in un nodo di calcolo. Vedere il [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Batch Apps

+ [Batch Apps Cloud SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview) (NuGet): abilitare le applicazioni per l'offload dei processi al servizio Batch.
+ [Estensione di Visual Studio per Batch Apps](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio Gallery): abilitare per il cloud le applicazioni in Visual Studio tramite Batch Apps Cloud SDK.
+ [Batch Apps Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview) (NuGet): interagire con applicazioni abilitate per l'offload di processi nel servizio Batch.
+ [Client Python per Batch Apps](https://github.com/Azure/azure-batch-apps-python) (GitHub): modulo del client Python per l'interazione con applicazioni configurate in un servizio Batch Apps.

>[AZURE.IMPORTANT]Azure rilascerà solo l'API delle app Batch come anteprima. Sono necessari solo attività di sviluppo per i progetti di test o di prova. Le funzionalità delle app di Batch fondamentali verranno integrate nell'API di Batch in versioni future del servizio.

## Risorse aggiuntive

+ [Esempi di codice](https://github.com/Azure/azure-batch-samples) (GitHub)
+ [Forum di Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=July15_HO5-->