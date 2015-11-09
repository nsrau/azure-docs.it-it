<properties 
	pageTitle="Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando Esplora archiviazione di Azure | Microsoft Azure" 
	description="Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando Storage Explorer di Azure" 
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
	ms.date="09/23/2015" 
	ms.author="bradsev" />

# Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando Storage Explorer di Azure

Collegamenti a indicazioni sulle tecnologie usate per spostare i dati in e/o da un archivio BLOB di Azure sono disponibili qui:

[AZURE.INCLUDE [selettore dello strumento di archiviazione BLOB](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Introduzione

Esplora archivi Azure è uno strumento basato su Windows gratuito per analizzare e modificare i dati in un account di archiviazione di Azure. Può essere scaricato da [Esplora archivi Azure](http://azurestorageexplorer.codeplex.com/).

> [AZURE.NOTE]Se si utilizza una macchina virtuale che è stata impostata con gli script forniti da [Macchine virtuali della scienza dei dati in Azure](machine-learning-data-science-virtual-machines.md), allora Esplora archivi di Azure è già installato nella macchina virtuale.

> [AZURE.NOTE]Per una completa introduzione dell'archiviazione BLOB di Azure, fare riferimento a [Informazioni di base sui BLOB di Azure](../storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Prerequisiti

In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e delle chiavi di archiviazione corrispondenti per quell’account. Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure.

- Per configurare una sottoscrizione di Azure, vedere [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/it-IT/pricing/free-trial/).
- Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull’account e la chiave, vedere [Informazioni sugli account di archiviazione di Azure](../storage-create-storage-account.md).


<a id="explorer"></a>
## Utilizzo di Esplora archivi Azure 

I passaggi seguenti documentano come caricare e scaricare i dati utilizzando Esplora archivi Azure.

1.  Avviare Esplora archivi Azure 
2.  Se l'account al quale si desidera accedere non è stato aggiunto a Esplora archivi Azure, fare clic sul pulsante "Aggiungi account" per aggiungere l'account. Se è già stato aggiunto, selezionare l'account nell'elenco a discesa "--Selezionare un account di archiviazione--". ![Creare un'area di lavoro][1] <br>
3. Immettere il nome e la chiave dell'account di archiviazione, quindi fare clic su Aggiungi account di archiviazione. È possibile aggiungere più account di archiviazione, ognuno dei quali verrà visualizzato in una scheda. I contenitori in questo account di archiviazione vengono visualizzati nel riquadro sinistro. Selezionare un contenitore per visualizzare i BLOB nel contenitore nel riquadro di destra. ![Creare un'area di lavoro][2] <br> ![Creare un'area di lavoro][3] <br>
4. Caricare i dati facendo clic sul pulsante "Carica". Selezionare uno o più file da caricare dal file system e scegliere "Apri" per iniziare a caricare i file.
5. Scaricare i dati selezionando il BLOB di archiviazione nel contenitore corrispondente e facendo clic sul pulsante "Scarica".

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!---HONumber=Nov15_HO1-->