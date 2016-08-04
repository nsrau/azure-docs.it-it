<properties 
	pageTitle="Introduzione a Azure Storage in cinque minuti | Microsoft Azure" 
	description="Imparare rapidamente a usare BLOB, tabelle e code di Microsoft Azure mediante Avvio rapido di Archiviazione di Azure, Visual Studio e l'emulatore di archiviazione di Azure. Eseguire la prima applicazione di Archiviazione di Azure nel servizio di archiviazione di Azure" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="get-started-article" 
	ms.date="07/21/2016"
	ms.author="tamram"/>

# Introduzione a Azure Storage in cinque minuti 

## Panoramica

Lo sviluppo con Archiviazione di Azure è una procedura semplice. In questa esercitazione viene illustrato come ottenere un'applicazione di archiviazione di Azure backup rapidità e facilità. Si useranno i modelli di avvio rapido inclusi in Azure SDK per .NET. Tali modelli contengono codice pronto all'uso che illustra alcuni scenari di programmazione di base con Archiviazione di Azure.

Per ottenere altre informazioni su Archiviazione di Azure prima di approfondire le informazioni sul codice, vedere [Passaggi successivi](#next-steps).

## Prerequisiti

Prima di iniziare, verificare di disporre dei prerequisiti seguenti.

1. Per compilare l'applicazione, è necessario disporre di [Visual Studio](https://www.visualstudio.com/) installato nel computer.

2. Installare la versione più recente [Azure SDK per .NET](https://azure.microsoft.com/downloads/). il SDK include i progetti di esempio delle Guide rapide di Azure, l'emulatore di archiviazione di Azure e [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Assicurarsi di avere [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) installato nel computer, come richiesto per i progetti di esempio delle Guide rapide di Azure che verrà utilizzato in questa esercitazione.

	Se non si è certi di quale versione di .NET Framework è installata sul computer in use, vedere [How to: Determine Which .NET Framework Versions Are Installed](https://msdn.microsoft.com/vstudio/hh925568.aspx). In alternativa, premere il pulsante **Start** o il tasto Windows e digitare **Pannello di controllo**. Fare clic su **Programmi** > **Programmi e Funzionalità**, e determinare se .NET Framework 4.5 è elencato tra i programmi installati.

4. È necessaria una sottoscrizione di Azure e un account di archiviazione di Azure.

    - Per ottenere una sottoscrizione Azure, vedere la versione di [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/) e le [Offerte per i membri](https://azure.microsoft.com/pricing/member-offers/)(per i membri di MSDN, Microsoft Partner Network, BizSpark e altri programmi Microsoft).
    - Per creare un account di archiviazione in Azure, vedere l'articolo relativo alla [creazione di un account di archiviazione](storage-create-storage-account.md#create-a-storage-account).

## Eseguire la prima applicazione di Archiviazione di Azure in Archiviazione di Azure nel cloud

Ora verrà creata una semplice applicazione di Archiviazione di Azure usando uno dei progetti di esempio di avvio rapido di Azure in Visual Studio. Questa esercitazione si concentra sui progetti di esempio per Archiviazione di Azure: **Archiviazione di Azure: BLOB**, **Archiviazione di Azure: file**, **Archiviazione di Azure: code** e **Archiviazione di Azure: tabelle**:

1. Avviare Visual Studio.
2. Scegliere **Nuovo progetto** dal menu **File**.
3. Nella finestra di dialogo **Nuovo progetto** fare clic su **Installato** > **Modelli** > **Visual C#** > **Cloud** > **Avvio rapido** > **Servizi dati**. a. Scegliere uno dei modelli seguenti: **Archiviazione di Azure: BLOB**, **Archiviazione di Azure: file**, **Archiviazione di Azure: code** o **Archiviazione di Azure: tabelle**. b. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.
	- 3\.c. Specificare un nome per il progetto e creare la nuova soluzione di Visual Studio, come illustrato:
	
	![Avvio rapido di Azure][Image1]

È possibile esaminare il codice sorgente prima di eseguire l'applicazione. Per esaminare il codice, selezionare **Esplora soluzioni** nel menu **Visualizza** di Visual Studio. Quindi, fare doppio clic sul file Program.cs.

A questo punto, eseguire l'applicazione di esempio:

1.	In Visual Studio, selezionare **Esplora Soluzioni** sul menu **Visualizza** . Aprire il file App.config e impostare come commento la stringa di connessione per l'emulatore di archiviazione di Azure:

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Rimuovere i simboli di commento dalla stringa di connessione per il servizio di archiviazione di Azure e fornire il nome e la chiave di accesso dell'account di archiviazione nel file App.config: `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	Per recuperare la chiave di accesso dell'account di archiviazione, vedere [Gestire le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#manage-your-storage-access-keys).

3.	Dopo aver specificato il nome e la chiave di accesso dell'account di archiviazione nel file App.config, nel menu **File** fare clic su Salva tutto per **salvare tutti** i file di progetto.
4.	Sul menu **Compila** fare clic su **Compila soluzione**.
5.	Nel menu **Debug** premere **F11** per eseguire la soluzione dettagliata o premere **F5** per eseguire la soluzione.


## Eseguire la prima applicazione di Archiviazione di Azure nell'emulatore di archiviazione di Azure

L'[emulatore di archiviazione di Azure](storage-use-emulator.md) offre un ambiente locale che emula i servizi BLOB, code e tabelle di Azure per scopi di sviluppo. È possibile utilizzare l'emulatore di archiviazione per testare l'applicazione di archiviazione locale, senza creare un account di archiviazione o di sottoscrizione di Azure e senza incorrere in alcun costo.

Proviamo, verrà creata una semplice applicazione di Archiviazione di Azure usando uno dei progetti di esempio di Avvio rapido di Azure in Visual Studio. Questa esercitazione si concentra sui progetti di esempio di **archiviazione Blob di Azure**, **archiviazione tabelle di Azure** e **Code di archiviazione di Azure** :

1. Avviare Visual Studio.
2. Scegliere **Nuovo progetto** dal menu **File**.
3. Nella finestra di dialogo **Nuovo progetto** fare clic su **Installato** > **Modelli** > **Visual C#** > **Cloud** > **Avvio rapido** > **Servizi dati**. a. Scegliere uno dei modelli seguenti: **Archiviazione di Azure: BLOB**, **Archiviazione di Azure: file**, **Archiviazione di Azure: code** o **Archiviazione di Azure: tabelle**. b. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**. c. Specificare un nome per il progetto e creare la nuova soluzione di Visual Studio, come illustrato:
	
	![Avvio rapido di Azure][Image1]

4.	In Visual Studio, selezionare **Esplora Soluzioni** sul menu **Visualizza** . Aprire il file App.config e impostare come commento la stringa di connessione per l'account di archiviazione di Azure, se è già stato aggiunto. Quindi rimuovere il commento della stringa di connessione per l'emulatore di archiviazione di Azure:

	`<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

È possibile esaminare il codice sorgente prima di eseguire l'applicazione. Per esaminare il codice, selezionare **Esplora soluzioni** nel menu **Visualizza** di Visual Studio. Quindi, fare doppio clic sul file Program.cs.

Successivamente, eseguire l'applicazione di esempio nell'emulatore di archiviazione di Azure:

1.	Premere il tasto **Start** o il tasto Windows, cercare *Emulatore di archiviazione di Microsoft Azure* e avviare l'applicazione. Quando l'emulatore si avvia, verranno visualizzate un'icona e una notifica nell'area di visualizzazione attività di Windows.
2.	In Visual Studio, fare clic su **Compila Soluzione** sul menu **Build**.
3.	Nel menu **Debug** premere **F11** per eseguire la soluzione dettagliata o **F5** per eseguire la soluzione dall’inizio alla fine.

## Passaggi successivi

Per ulteriori informazioni sull'archiviazione Azure, vedere la risorsa seguente:

* [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md)
* [Guida introduttiva ad Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Introduzione all'archiviazione BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md)
* [Introduzione all'archiviazione tabelle di Azure con .NET](storage-dotnet-how-to-use-tables.md)
* [Introduzione all'archiviazione code di Azure con .NET](storage-dotnet-how-to-use-queues.md)
* [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md)
* [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
* [Documentazione di Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Libreria client di archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [API REST dei servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 

<!---HONumber=AcomDC_0727_2016-->