<properties 
	pageTitle="Introduzione a Azure Storage in cinque minuti | Microsoft Azure" 
	description="Imparare rapidamente a usare BLOB, tabelle e code di Microsoft Azure mediante Avvio rapido di Azure, Visual Studio e l'Emulatore di archiviazione di Azure. Eseguire la prima applicazione di Archiviazione di Azure nel servizio di archiviazione di Azure" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="05/28/2015" 
	ms.author="tamram;selcint"/>

# Introduzione a Azure Storage in cinque minuti 

È facile iniziare lo sviluppo sull’archiviazione di Azure, In questa esercitazione viene illustrato come ottenere un'applicazione di archiviazione di Azure backup rapidità e facilità. Verrà illustrata in due scenari per facilmente aumentando di archiviazione Azure:

- [Eseguire la prima applicazione di Archiviazione di Azure nell'emulatore di archiviazione di Azure](#run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator)
- [Eseguire la prima applicazione di Archiviazione di Azure in Archiviazione di Azure nel cloud](#run-your-first-azure-storage-application-against-azure-storage-in-the-cloud)

Per ottenere altre informazioni si Archiviazione di Azure prima di approfondire le informazioni sul codice, vedere [Passaggi successivi](#next-steps).

## Prerequisiti

Prima di iniziare la distribuzione, verificare che i prerequisiti siano disponibili.

1. Per compilare l'applicazione, è necessario disporre di [Visual Studio](https://www.visualstudio.com/) installato nel computer. 
2. Installare la versione più recente [Azure SDK per .NET](http://azure.microsoft.com/downloads/). il SDK include i progetti di esempio delle Guide rapide di Azure, l'emulatore di archiviazione di Azure e [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).
3. Assicurarsi di avere [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) installato nel computer, come richiesto per i progetti di esempio delle Guide rapide di Azure che verrà utilizzato in questa esercitazione. Se non si è certi di quale versione di .NET Framework è installata sul computer in use, vedere [How to: Determine Which .NET Framework Versions Are Installed](https://msdn.microsoft.com/vstudio/hh925568.aspx). In alternativa, premere il pulsante **Start** o il tasto Windows e digitare **Pannello di controllo**. Fare clic su **Programmi** > **Programmi e Funzionalità**, e determinare se .NET Framework 4.5 è elencato tra i programmi installati.

La versione più recente dei file binari di Azure Storage Client Library è disponibile in [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).


## Eseguire la prima applicazione di Archiviazione di Azure nell'emulatore di archiviazione di Azure

Quando si sviluppa un'applicazione che utilizza l'archiviazione di Azure, è possibile eseguire il [emulatore di archiviazione Azure](storage-use-emulator.md). L'emulatore di archiviazione di Microsoft Azure offre un ambiente locale che emula i servizi BLOB, code e tabelle di Azure per scopi di sviluppo. È possibile utilizzare l'emulatore di archiviazione per testare l'applicazione di archiviazione locale, senza creare un account di archiviazione o di sottoscrizione di Azure e senza incorrere in alcun costo.

Proviamo, verrà creata una semplice applicazione di Archiviazione di Azure usando uno dei progetti di esempio di avvio rapido di Azure in Visual Studio. Questa esercitazione si concentra sui progetti di esempio di **archiviazione Blob di Azure**, **archiviazione tabelle di Azure** e **Code di archiviazione di Azure** :

1. Avviare Visual Studio.
2. Scegliere **Nuovo progetto** dal menu **File**.
3. Nella finestra di dialogo **Nuovo progetto** fare clic su **Installato** > **Modelli** > **Visual C#** > **Cloud** > **Quick Starts** > **Data Services**.
	- 3\.a. Scegliere uno dei seguenti modelli: Archiviazione Blob di Azure, Archiviazione tabelle di Azure o Code di archiviazione di Azure. 
	- 3\.b. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.	
	- 3\.c. Specificare un nome per il progetto e creare la nuova soluzione di Visual Studio, come illustrato:
	
	![Guide rapide di Azure][Image1]

È possibile esaminare il codice sorgente prima di eseguire l'applicazione. Per esaminare il codice, selezionare **Esplora soluzioni** nel menu **Visualizza** di Visual Studio. Quindi, fare doppio clic sul file Program.cs.

Successivamente, eseguire l'applicazione di esempio nell'emulatore di archiviazione di Azure:

1.	Premere il tasto **Start** o il tasto Windows e digitare *Emulatore di archiviazione Azure*, ed avviare l’applicazione.
2.	In Visual Studio, fare clic su **Compila Soluzione** sul menu **Build**. 
3.	Sul menu **Debug**, Premere **F11** per eseguire la soluzione dettagliata o premere **F5** per eseguire la soluzione dall’inizio alla fine.

## Eseguire la prima applicazione di Archiviazione di Azure in Archiviazione di Azure nel cloud

Per eseguire l'archiviazione di Azure nel cloud, è necessario una sottoscrizione di Azure e un account di archiviazione, se non è uno già:

- Per ottenere una sottoscrizione Azure, vedere la versione di [Versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/), [Opzioni di acquisto](http://azure.microsoft.com/pricing/purchase-options/) e le [Offerte per i membri](http://azure.microsoft.com/pricing/member-offers/)(per i membri di MSDN, Microsoft Partner Network, BizSpark e altri programmi Microsoft).
- Per creare un account di archiviazione Standard in Azure, vedere [Creare, gestire o eliminare un account di archiviazione](storage-create-storage-account.md).

Ora verrà creata una semplice applicazione di Archiviazione di Azure usando uno dei progetti di esempio di avvio rapido di Azure in Visual Studio. Questa esercitazione si concentra sui progetti di esempio di **archiviazione BLOB di Azure**, ** archiviazione tabelle di Azure** e **code di archiviazione di Azure**.

1. Avviare Visual Studio.
2. Scegliere **Nuovo progetto** dal menu **File**.
3. Nella finestra di dialogo **Nuovo progetto** fare clic su **Installato** > **Modelli** > **Visual C#** > **Cloud** > **Quick Starts** > **Data Services**.
	- 3\.a. Scegliere uno dei seguenti modelli: Archiviazione Blob di Azure, Archiviazione tabelle di Azure o Code di archiviazione di Azure.
	- 3\.b. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.
	- 3\.c. Specificare un nome per il progetto e creare la nuova soluzione di Visual Studio, come illustrato: 

È possibile esaminare il codice sorgente prima di eseguire l'applicazione. Per esaminare il codice, selezionare **Esplora soluzioni** nel menu **Visualizza** di Visual Studio. Quindi, fare doppio clic sul file Program.cs.

A questo punto, eseguire l'applicazione di esempio:

1.	In Visual Studio, selezionare **Esplora Soluzioni** sul menu **Visualizza** . Fare doppio clic sul file App.config e impostare come commento la stringa di connessione per l'emulatore di archiviazione dell'SDK di Azure:

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Rimuovere i simboli di commento dalla stringa di connessione per il servizio di archiviazione di Azure e fornire il nome e la chiave di accesso dell'account di archiviazione nel file App.config: `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	Per trovare il nome e la chiave di accesso dell'account di archiviazione, vedere la pagina [Informazioni sull’account di archiviazione](storage-whatis-account.md).

3.	Dopo aver specificato il nome e la chiave di accesso dell'account di archiviazione nel file App.config, nel menu **File** fare clic su Salva tutto per **salvare tutti** i file di progetto.
4.	Sul menu **Compila** fare clic su **Compila soluzione**.
5.	Nel menu **Debug** premere **F11** per eseguire la soluzione dettagliata o premere **F5** per eseguire la soluzione.


## Passaggi successivi

Per ulteriori informazioni sull'archiviazione Azure, vedere la risorsa seguente:

* [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md)
* [Come usare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md)
* [Come usare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md)
* [Come usare l'archiviazione di accodamento da .NET](storage-dotnet-how-to-use-queues.md)
* [Documentazione di Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/)
* [Libreria client di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [API REST di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 

<!---HONumber=July15_HO3-->