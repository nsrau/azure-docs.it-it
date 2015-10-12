<properties 
	pageTitle="Spostamento dei dati da e verso l'archiviazione BLOB di Azure | Microsoft Azure" 
	description="Spostamento dei dati da e verso l'archiviazione BLOB di Azure" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="bradsev;sunliangms;sachouks;mohabib" />

# Spostamento dei dati da e verso l'archiviazione BLOB di Azure

[AZURE.INCLUDE [selettore dello strumento di archiviazione BLOB](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Introduzione

In questo argomento vengono descritti tre metodi per spostare dati da e verso l'archiviazione BLOB di Azure:

- Utilizzo di Esplora archivi Azure
- Utilizzo dell'utilità della riga di comando AzCopy
- Utilizzo di Azure SDK in Python

Quale sia il metodo adatto dipenderà dallo scenario. L’articolo [Scenari relativi ad ADAPT (Advanced Analytics Process and Technology) in Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md) consente di determinare le risorse necessarie per un'ampia gamma di flussi di lavoro di analisi scientifica dei dati usati nel processo di analisi avanzata.

> [AZURE.NOTE]Per una completa introduzione dell'archiviazione BLOB di Azure, fare riferimento a [Informazioni di base sui BLOB di Azure](../storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

> [AZURE.TIP]In alternativa, è possibile usare [Data factory di Azure](https://azure.microsoft.com/it-IT/services/data-factory/) per creare e pianificare una pipeline che scarica i dati dall'archiviazione BLOB di Azure, li passa a un servizio Web di Azure Machine Learning pubblicato, riceve i risultati dell'analisi predittiva e carica i risultati nella risorsa di archiviazione. Per altre informazioni, vedere [Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning](../data-factory/data-factory-create-predictive-pipelines.md).

## Prerequisiti

In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e delle chiavi di archiviazione corrispondenti per quell’account. Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure.

- Per configurare una sottoscrizione di Azure, vedere [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/it-IT/pricing/free-trial/).
- Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull’account e la chiave, vedere [Informazioni sugli account di archiviazione di Azure](../storage-create-storage-account.md).




 

<!---HONumber=Oct15_HO1-->