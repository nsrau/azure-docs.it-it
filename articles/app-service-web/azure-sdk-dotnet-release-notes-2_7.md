
<properties 
   pageTitle="Note sulla versione di Azure SDK per .NET 2.7" 
   description="Note sulla versione di Azure SDK per .NET 2.7" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/09/2015"
   ms.author="juliako"/>


# Note sulla versione di Azure SDK per .NET 2.7

Questo documento contiene le note sulla versione per Azure SDK per .NET 2.7.

Azure SDK 2.7 è supportato solo in Visual Studio 2015 e Visual Studio 2013. [Azure SDK 2.6](http://azure.microsoft.com/downloads/) è l'ultimo SDK supportato per Visual Studio 2012.

Per informazioni dettagliate su questa versione, vedere il [post di annuncio di Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

##Miglioramenti relativi all'accesso per Visual Studio 2015

Azure 2.7 SDK per Visual Studio 2015 supporta le nuove funzionalità di gestione di identità in Visual Studio 2015. Ciò include il supporto per gli account che accedono ad Azure tramite il controllo degli accessi in base al ruolo, Cloud Solution Provider, DreamSpark e altri tipi di account e sottoscrizioni.

Questi miglioramenti per l'accesso sono disponibili solo in Visual Studio 2015. Il supporto per Visual Studio 2013 verrà incluso in un aggiornamento futuro.


##Mobile SDK

Aggiornamento dei modelli di **applicazioni per dispositivi mobili** in linea con la versione più recente del [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e del processo di installazione.

##Bus di servizio 

Correzioni di bug e miglioramenti generali. Per altri dettagli sugli aggiornamenti e le funzionalità, vedere le note sulla versione della versione più recente del pacchetto [NuGet del bus di servizio](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

##Strumenti HDInsight 

In questa versione sono stati introdotti i seguenti aggiornamenti. Questi aggiornamenti sono in anteprima. Per altre informazioni, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Grafici di hive per processi Hive su Tez
- Supporto completo di IntelliSense per DML Hive
- Supporto del modello Pig
- Modelli Storm per servizi di Azure

###Modifiche di rilievo

- Il vecchio progetto **Storm** deve essere aggiornato quando si usa questa versione degli strumenti. Per altre informazioni, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express non è più supportato. Per altre informazioni, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=619108).

##Strumenti del servizio app di Azure

In questa versione sono stati introdotti gli aggiornamenti seguenti per le estensioni degli strumenti Web. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

- Aggiunta del supporto per gli account DreamSpark
- Modifica completa degli strumenti di Azure per supportare le nuove API di gestione delle risorse di Azure
- Aggiunta del supporto per Azure App Service in [Cloud Explorer](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)

###Problemi noti

I nodi dello slot di distribuzione di app Web non vengono visualizzati nel nodo Slot in Esplora server e i nodi figlio dello slot di distribuzione di app Web non vengono caricati in Cloud Explorer. Questo problema è stato risolto e preparato per la prossima versione dell'SDK.


##<a id="cloud_explorer"></a>Cloud Explorer per Visual Studio 2015

Azure SDK 2.7 include Cloud Explorer per Visual Studio 2015 che consente di visualizzare le risorse di Azure, controllare le relative proprietà ed eseguire azioni fondamentali per gli sviluppatori dall'interno di Visual Studio.

Cloud Explorer supporta quanto segue:

- Visualizzazioni Gruppo di risorse e Tipo di risorsa delle risorse di Azure 
- Ricerca delle risorse in base al nome (disponibile nella visualizzazione Tipo di risorsa)
- Supporto per le sottoscrizioni e le risorse con Controllo degli accessi in base al ruolo applicato 
- Pannello delle azioni integrato che mostra le azioni destinate agli sviluppatori specifiche per le risorse selezionate. Ad esempio: collegare il debugger remoto per le macchine virtuali create utilizzando lo Stack di gestione delle risorse, visualizzare i dati di diagnostica per le macchine virtuali e così via.
- Pannello delle proprietà integrato che mostra le proprietà destinate agli sviluppatori comunemente necessarie durante sviluppo e test 
- Cambio rapido dell'account da usare durante l'enumerazione delle risorse (usare il comando Impostazioni sulla barra degli strumenti) 
- Filtro delle sottoscrizioni da usare durante l'enumerazione delle risorse (usare il comando Impostazioni sulla barra degli strumenti) 
- Collegamenti diretti al Portale di anteprima di Azure per la gestione delle risorse e dei gruppi di risorse 
 
 
##Strumenti di Gestione risorse di Azure 

Gli strumenti di gestione risorse di Azure sono stati aggiornati per funzionare con Controllo degli accessi in base al ruolo e i nuovi tipi di sottoscrizione. Queste modifiche includono la possibilità di usare i nuovi account di archiviazione, oltre all'archiviazione classica, per archiviare gli elementi durante la distribuzione.

Se si usa un progetto Gruppo di risorse di Azure da una versione precedente dell'SDK con SDK 2.7, è necessario un nuovo script per eseguire la distribuzione con un nuovo account di archiviazione al posto dell'archiviazione classica. Verrà richiesta conferma prima di apportare modifiche al progetto per aggiungere il nuovo script. Lo script precedente verrà rinominato e sarà necessario apportare manualmente eventuali modifiche al nuovo script.
 
 
##Strumenti di Esplora archivi 

- Supporto per la visualizzazione di BLOB di accodamento. Altre informazioni sono disponibili in [questo post di blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Supporto per la visualizzazione degli account di archiviazione Premium tramite Esplora server. Esplora server visualizzerà solo i BLOB di pagine per gli account di archiviazione Premium, perché sono l'unico tipo supportato per gli account di archiviazione Premium.

##Strumenti di Data factory di Azure per Visual Studio 

Introduzione agli **strumenti di Data factory di Azure** per Visual Studio. L'elenco seguente include le funzionalità abilitate. Per altre informazioni, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=617530).

- **Creazione basata su modelli**: selezionare i modelli basati su casi di utilizzo, i modelli di spostamento dei dati o i modelli di elaborazione dei dati per distribuire una soluzione di integrazione dei dati completa e iniziare velocemente a lavorare con Data factory. 
- **Integrazione con Esplora soluzioni per la creazione e distribuzione di entità Data factory**: creare e distribuire pipeline ed entità correlate come progetti di Visual Studio. 
- **Integrazione con la vista Diagramma per l'interazione visiva durante la creazione**: creare visivamente pipeline e set di dati con l'aiuto della vista Diagramma. 
- **Integrazione con Esplora server per l'esplorazione e l'interazione con le entità già distribuite**: usare Esplora server per individuare le data factory già distribuite e le entità corrispondenti. Importare una data factory distribuita o qualsiasi entità (pipeline, servizio collegato, set di dati) nel progetto. 
- **Modifica di JSON con convalida dello schema e IntelliSense avanzato**: configurare e modificare con efficienza documenti JSON delle entità di Data factory con funzionalità IntelliSense avanzate e convalida dello schema 
- **Pubblicazione in più ambienti**: pubblicare le pipeline create negli ambienti di sviluppo, test o produzione mediante la creazione di file di configurazione separati per ogni ambiente.
- **Supporto dell'elaborazione dei dati basata su Pig, Hive e .NET**: supporto per script Pig e Hive nel progetto di Data factory. Supporto dei riferimenti al progetto C# per la gestione dell'attività .NET.

##Vedere anche

[Post di annuncio di Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Informazioni sul Supporto e il Ritiro di Azure SDK per .NET e API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

<!---HONumber=August15_HO7-->