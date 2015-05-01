<properties 
	pageTitle="Introduzione all'uso di BLOB, tabelle e code di Azure in 5 minuti" 
	description="Informazioni su come imparare rapidamente a usare BLOB, tabelle e code di Microsoft Azure mediante Avvio rapido di Azure e Visual Studio." 
	services="storage" 
	documentationCenter=".net" 
	authors="Selcin" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/18/2015" 
	ms.author="selcint"/>

# Introduzione all'uso di BLOB, tabelle e code di Azure in 5 minuti 

In questa esercitazione viene illustrato come programmare rapidamente usando **BLOB**, **tabelle** e **code** di Archiviazione di Azure sviluppando una semplice applicazione Azure in Visual Studio. 

L'esercitazione include due scenari principali per imparare rapidamente a usare Archiviazione di Azure:

- Eseguire la prima applicazione di Archiviazione di Azure nell'emulatore di archiviazione di Azure
- Eseguire la prima applicazione di Archiviazione di Azure nel servizio di archiviazione di Azure

Per ottenere altre informazioni si Archiviazione di Azure prima di approfondire le informazioni sul codice, vedere [Passaggi successivi](#next-steps).

## Eseguire la prima applicazione di Archiviazione di Azure nell'emulatore di archiviazione di Azure

Questa sezione descrive come programmare usando **BLOB**, **tabelle** e **code** di Archiviazione di Azure sviluppando un'applicazione di esempio che accede all'[emulatore di archiviazione di Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx). L'emulatore di archiviazione di Microsoft Azure offre un ambiente locale che emula i servizi BLOB, code e tabelle di Azure per scopi di sviluppo. Usando l'emulatore di archiviazione, è possibile testare l'applicazione sui servizi di archiviazione in locale, senza sostenere alcun costo.

Per completare questa sezione, assicurarsi innanzitutto di eseguire le attività prerequisite seguenti:

1. Per compilare l'applicazione, è necessario disporre di [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) installato nel computer. Se Visual Studio non è installato, è possibile installare Visual Studio Express per Web al momento dell'installazione di [Azure SDK 2.5 per Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) o versioni successive. 
2. Assicurarsi che [Azure SDK 2.5 per Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) o versioni successive sia installato nel computer in quanto include i progetti di esempio di avvio rapido di Azure e la [libreria clienti di Archiviazione di Azure per .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).  
3. Verificare che [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) sia installato nel computer in quanto è necessario per i progetti di esempio di avvio rapido di Azure. Se non si è certi di quale versione di .NET Framework è installata nel computer in uso, vedere [Procedura: determinare le versioni di .NET Framework installate[https://msdn.microsoft.com/vstudio/hh925568.aspx). In alternativa, premere il pulsante **Start** o il tasto Windows e digitare **Pannello di controllo**. Quindi, fare clic su **Programmi** > **Programmi e funzionalità**. Verificare se .NET Framework 4.5 è elencato nei programmi installati.

Ora verrà creata una semplice applicazione di Archiviazione di Azure usando uno dei progetti di esempio di avvio rapido di Azure in Visual Studio. Questa esercitazione si concentra sui progetti di esempio di **archiviazione BLOB di Azure**, **archiviazione tabelle di Azure** e **code di archiviazione di Azure**. Per ogni progetto di esempio, si applicano le seguenti istruzioni fatta eccezione per il passaggio 3.a che prevede la scelta di un modello diverso:

1. Premere il pulsante **Start** o il tasto Windows, digitare "Visual Studio 2013" o "VS Express 2013 per il Web". Fare clic sul programma per avviarlo.
2. Scegliere **Nuovo progetto** dal menu **File**.
3. Nella finestra di dialogo **Nuovo progetto**, fare clic su **Installato** > **Modelli** > **Visual C#** > **Cloud** > **QuickStarts** > **DataServices**.
	- 3.a.  Scegliere uno dei seguenti modelli: Archiviazione BLOB di Azure, Archiviazione tabelle di Azure o Code di archiviazione di Azure. 
	- 3.b. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.	
	- 3.c. A seconda del modello scelto, denominare l'applicazione, ad esempio **DataBlobStorage**, **DataTableStorage** o **DataStorageQueue**. Fare clic su **OK**. Verrà creata una nuova soluzione Visual Studio. Vedere la seguente schermata come esempio.
	
	![Azure QuickStarts][Image1]

Si consiglia di esaminare il codice sorgente per informazioni su come programmare usando Archiviazione di Azure prima di eseguire l'applicazione. Per esaminare il codice, selezionare **Esplora soluzioni** nel menu **Visualizza** di Visual Studio. Quindi, fare doppio clic sul file Program.cs. 

A questo punto, eseguire l'applicazione di esempio usando l'[emulatore di archiviazione di Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx) installato come parte di Azure SDK:

1.	Avviare l'emulatore di archiviazione di Azure: Premere il pulsante **Start** o il tasto Windows e digitare "emulatore di archiviazione di Azure". Selezionarlo dall'elenco di applicazioni per avviarlo.
2.	In Visual Studio fare clic su **Compila soluzione** nel menu **Compila**. 
3.	Nel menu **Debug** premere **F11** per eseguire la soluzione dettagliata o premere **F5** per eseguire la soluzione.

## Eseguire la prima applicazione di Archiviazione di Azure in Archiviazione di Azure nel cloud
Questa sezione descrive come programmare usando **BLOB**, **tabelle** e **code** di Archiviazione di Azure sviluppando un'applicazione di esempio che accede al [servizio Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/).

Per completare questa sezione, assicurarsi innanzitutto di eseguire le attività prerequisite seguenti:

1. Per compilare l'applicazione, è necessario disporre di [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) installato nel computer. Se Visual Studio non è installato, è possibile installare Visual Studio Express per Web al momento dell'installazione di [Azure SDK 2.5 per Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) o versioni successive. 
2. Assicurarsi che [Azure SDK 2.5 per Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) o versioni successive sia installato nel computer in quanto include i progetti di esempio di avvio rapido di Azure e la [libreria clienti di Archiviazione di Azure per .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).  
3. Verificare che [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) sia installato nel computer in quanto è necessario per i progetti di esempio di avvio rapido di Azure. Se non si è certi di quale versione di .NET Framework è installata nel computer in uso, vedere [Procedura: Determinare le versioni di .NET Framework installate](https://msdn.microsoft.com/vstudio/hh925568.aspx). In alternativa, premere il pulsante **Start** o il tasto Windows e digitare **Pannello di controllo**. Quindi, fare clic su **Programmi** > **Programmi e funzionalità**. Verificare se .NET Framework 4.5 è elencato nei programmi installati.
4.	Ottenere una sottoscrizione di Azure (se non se ne ha già una) e creare inoltre un account di **archiviazione Standard**:
	- Per ottenere una sottoscrizione Azure, vedere la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/), le [opzioni di acquisto](http://azure.microsoft.com/pricing/purchase-options/) e le [offerte per i membri](http://azure.microsoft.com/pricing/member-offers/) (per i membri di MSDN, Microsoft Partner Network, BizSpark e altri programmi Microsoft).
	- Per creare un account di **archiviazione Standard** in Azure, vedere [Creare, gestire o eliminare un account di archiviazione](storage-create-storage-account.md). **Nota:** sono disponibili due tipi di account di archiviazione: account di archiviazione Standard e account di archiviazione Premium. L'account di archiviazione Standard fornisce l'accesso all'archiviazione BLOB, tabelle e accodamento di Azure. L'account di archiviazione Premium è attualmente disponibile solo per l'archiviazione di dati sui dischi usati da Macchine virtuali di Azure. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

Ora verrà creata una semplice applicazione di Archiviazione di Azure usando uno dei progetti di esempio di avvio rapido di Azure in Visual Studio. Questa esercitazione si concentra sui progetti di esempio di **archiviazione BLOB di Azure**, **archiviazione tabelle di Azure** e **code di archiviazione di Azure**. Per ogni progetto di esempio, si applicano le seguenti istruzioni fatta eccezione per il passaggio 3.a che prevede la scelta di un modello diverso:

1. Premere il pulsante **Start** o il tasto Windows, digitare "Visual Studio 2013" o "VS Express 2013 per il Web". Fare clic sul programma per avviarlo.
2. Scegliere **Nuovo progetto** dal menu **File**.
3. Nella finestra di dialogo **Nuovo progetto**, fare clic su **Installato** > **Modelli** > **Visual C#** > **Cloud** > **QuickStarts** > **DataServices**.
	- 3.a. Scegliere uno dei seguenti modelli: Archiviazione BLOB di Azure, Archiviazione tabelle di Azure o Code di archiviazione di Azure. 
	- 3.b. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.
	- 3.c. A seconda del modello scelto, denominare l'applicazione, ad esempio **DataBlobStorage**, **DataTableStorage** o **DataStorageQueue**. Fare clic su **OK**. Verrà creata una nuova soluzione Visual Studio. 

Si consiglia di esaminare il codice sorgente per informazioni su come programmare usando Archiviazione di Azure prima di eseguire l'applicazione. Per esaminare il codice, selezionare **Esplora soluzioni** nel menu **Visualizza** di Visual Studio. Quindi, fare doppio clic sul file Program.cs. 

A questo punto, eseguire l'applicazione di esempio:

1.	In Visual Studio selezionare **Esplora soluzioni** nel menu **Visualizza**. Fare doppio clic sul file App.config e impostare come commento la stringa di connessione per l'emulatore di archiviazione dell'SDK di Azure: 

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Rimuovere i simboli di commento dalla stringa di connessione per il servizio di archiviazione di Azure e fornire il nome e la chiave di accesso dell'account di archiviazione nel file App.config:
	`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"` 

	Per trovare il nome e la chiave di accesso dell'account di storage, vedere la pagina [Informazioni sull'account di archiviazione](storage-whatis-account.md). 

3.	Dopo aver specificato il nome e la chiave di accesso dell'account di archiviazione nel file App.config, nel menu **File** fare clic su **Salva tutto** per salvare tutti i file di progetto. 
4.	Nel menu **Compila** fare clic su **Compila soluzione**. 
5.	Nel menu **Debug** premere **F11** per eseguire la soluzione dettagliata o premere **F5** per eseguire la soluzione.


## Passaggi successivi
In questa esercitazione si è appreso a programmare usando l'archiviazione BLOB di Azure, l'archiviazione tabelle di Azure e le code di archiviazione di Azure. 

Per altre informazioni su queste funzionalità, visitare i seguenti collegamenti:

* [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md)
* [Come usare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md)
* [Come usare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md)
* [Come usare l'archiviazione di accodamento da .NET](storage-dotnet-how-to-use-queues.md)
* [Documentazione di Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/)
* [Riferimento MSDN ad Archiviazione di Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
* [Libreria client di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [API REST di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png



<!--HONumber=52-->