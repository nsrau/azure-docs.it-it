<properties 
	pageTitle="Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure" 
	description="Spiegazione di base della funzionalità di scalabilità elastica del database SQL di Azure, che include un'app di esempio di facile esecuzione." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="sidneyh@microsoft.com"/>

# Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure (anteprima)

L'aumento o la riduzione della capacità su richiesta è una delle promesse principali del cloud computing. Mantenere questa promessa è sempre stato complesso per il livello database delle applicazioni cloud. Nel corso degli ultimi anni il settore si è orientato su modelli di progettazione consolidati, noti come partizionamento orizzontale. Benché il modello generale di partizionamento orizzontale risolva questo problema, la creazione e la gestione di applicazioni tramite il partizionamento orizzontale richiedono investimenti significativi nelle infrastrutture, indipendentemente dalla logica di business dell'applicazione. 

La funzionalità di scalabilità elastica del database SQL di Azure, disponibile in anteprima, permette al livello dati di un'applicazione di aumentare e ridurre il numero di istanze tramite procedure di partizionamento orizzontale standard di settore, semplificando al tempo stesso in modo significativo lo sviluppo e la gestione delle applicazioni cloud partizionate. La scalabilità elastica offre funzionalità per lo sviluppo e per la gestione, fornite tramite un insieme di librerie .NET e i modelli di servizio di Azure, che possono essere ospitati nella sottoscrizione Azure per gestire le applicazioni a scalabilità elevata. La scalabilità elastica del database SQL di Azure implementa gli aspetti relativi all'infrastruttura del partizionamento orizzontale, permettendo quindi di concentrarsi sulla logica di business dell'applicazione. 

In questo documento viene presentata l'esperienza di sviluppo per la scalabilità elastica del database SQL di Azure. 

Per altre informazioni su questa funzionalità, vedere le [informazioni generali sulla scalabilità elastica](http://go.microsoft.com/?linkid=9862592).

Per un elenco di tutti gli argomenti sulla scalabilità elastica, vedere la relativa [mappa documento](./sql-database-elastic-scale-documentation-map.md)

## Applicazione di esempio della scalabilità elastica

Nell'esempio viene creata una semplice applicazione partizionata si esplorano le funzionalità chiave della scalabilità elastica. Per scaricare ed eseguire l'applicazione, attenersi alla procedura descritta di seguito o nel video di [introduzione alla scalabilità elastica](http://go.microsoft.com/?linkid=9862983). 

### Prerequisiti
Per eseguire l'app di esempio è necessario usare Visual Studio e avere accesso a un database SQL di Azure in esecuzione su Azure. Se non si dispone già di una sottoscrizione Azure, è possibile effettuare la [sottoscrizione di prova gratuita](http://azure.microsoft.com/pricing/free-trial/).
#### Visual Studio e Nuget

1. È richiesto Visual Studio 2012 o versioni successive con C#. Scaricare una versione gratuita dalla pagina [Download di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 o versioni successive. Per ottenere la versione più recente, vedere il documento relativo all'[installazione di NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)
#### Creare un database SQL di Azure

* Vedere [Introduzione al database SQL di Microsoft Azure](http://azure.microsoft.com/documentation/articles/sql-database-get-started/).

## Scaricare ed eseguire l'app di esempio
L'applicazione di esempio **Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure** illustra gli aspetti più importanti dell'esperienza di sviluppo delle applicazioni partizionate usando la scalabilità elastica del database SQL di Azure. L'applicazione è incentrata sui casi d'uso chiave per la [gestione delle mappe di partizione](http://go.microsoft.com/?linkid=9862595), il [routing dipendente dai dati](http://go.microsoft.com/?linkid=9862596) e l'[esecuzione di query su più partizioni](http://go.microsoft.com/?linkid=9862597). Per scaricare ed eseguire l'applicazione di esempio, seguire questa procedura: 

1. Aprire Visual Studio e scegliere **File -> Nuovo -> Progetto**.
2. Nella finestra di dialogo, fare clic su **Online**.

    ![New Project>Online][2]
3. Quindi fare clic su **Visual C#** in **Esempi**.

    ![Click Visual C#][3]
4. Nella casella di ricerca digitare **Elastic Scale** per cercare l'esempio. Viene visualizzato il titolo **Elastic Scale with Azure SQL Database-Getting Started**.

    ![Search Box][1]
 
5. Selezionare l'esempio, scegliere un nome e una posizione per il nuovo progetto e premere **OK** per creare il progetto.
6. Aprire il file **app.config** nella soluzione per il progetto di esempio e seguire le istruzioni del file per aggiungere il nome del server di database SQL Azure e le informazioni di accesso (nome utente e password).
7. Compilare ed eseguire l'applicazione. Quando viene richiesto, consentire a Visual Studio di ripristinare i pacchetti NuGet della soluzione. In questo modo verrà scaricata la versione più recente delle librerie client della scalabilità elastica da NuGet.
8. Provare a usare le varie opzioni per ottenere altre informazioni sulle funzionalità della scalabilità elastica. Annotare i passaggi eseguiti dall'applicazione nell'output della console ed esplorare liberamente il codice sottostante.

    ![progress][4]

Complimenti. È stata creata ed eseguita la prima applicazione di scalabilità elastica nel database SQL di Azure. Esaminare rapidamente i partizionamenti creati dall'applicazione di esempio effettuando la connessione con SQL Server Management Studio al server del database di Azure. Si noteranno nuovi database con partizionamento e un database di gestione delle mappe con partizionamento creati dall'applicazione di esempio.

**Nota**   Se non è disponibile SQL Server Management Studio, vedere [Gestione di database SQL di Azure tramite SQL Server Management Studio](http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/), che include istruzioni per scaricare lo strumento.  

### Elementi chiave dell'esempio di codice

1. **Gestione di partizionamenti e di mappe con partizionamento**: il codice illustra come usare i partizionamenti, gli intervalli e i mapping nel file **ShardMapManagerSample.cs**. Altre informazioni su questo argomento sono disponibili nella pagina [Gestione mappe partizioni](http://go.microsoft.com/?linkid=9862595).  
2. **Routing dipendente dai dati**: il routing delle transazioni al partizionamento appropriato è mostrato in **DataDependentRoutingSample.cs**. Per informazioni dettagliate, vedere [Routing dipendente dai dati](http://go.microsoft.com/?linkid=9862596). 
3. **Esecuzione di query su più partizionamenti**: l'esecuzione di query su più partizionamenti è illustrata nel file **MultiShardQuerySample.cs**. Per informazioni dettagliate, vedere [Esecuzione di query su più partizioni](http://go.microsoft.com/?linkid=9862597).
4. **Aggiunta di partizionamenti vuoti**: l'aggiunta iterativa di nuove partizioni vuote viene eseguita dal codice nel
file **AddNewShardsSample.cs**. Informazioni dettagliate su questo argomento sono disponibili nella pagina [Gestione mappe partizioni](http://go.microsoft.com/?linkid=9862595).

### Altre operazioni di scalabilità elastica

1. **Suddivisione di un partizionamento esistente**: La possibilità di suddividere partizionamenti viene fornita tramite il **servizio di suddivisione/unione**. Altre informazioni su questo servizio sono disponibili nella pagina [Servizio di suddivisione/unione](http://go.microsoft.com/?linkid=9862795).
2. **Unione di partizionamenti esistenti**: le unioni di partizioni esistenti vengono inoltre eseguite tramite il **servizio di suddivisione/unione**. Per altre informazioni, fare riferimento a [Servizio di suddivisione/unione](http://go.microsoft.com/?linkid=9862795).   


## Costi

Le librerie e i modelli di servizio della scalabilità elastica sono gratuiti. La scalabilità elastica non comporta costi aggiuntivi oltre a quelli applicati per l'uso di Azure. 

Quindi, nel caso dei nuovi database creati dall'applicazione di esempio, il costo dipende dall'edizione del database SQL di Azure scelta e dall'uso di Azure da parte dell'applicazione.

Per informazioni sui prezzi, vedere [Dettagli prezzi del database SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Passaggi successivi
Per altre informazioni sulla funzionalità di scalabilità elastica, vedere:

* [Pagina di formazione sulla scalabilità elastica](./sql-database-elastic-scale-documentation-map.md) 
-    Esempi di codice: 
    -    [Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Scalabilità elastica con il database SQL di Azure - Integrazione con Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Elasticità di partizionamento in Script Center](http://go.microsoft.com/?linkid=9862617)
-    Blog: [Annuncio della scalabilità elastica](http://go.microsoft.com/?linkid=9862608)
-    Channel 9: [Video sulla panoramica della scalabilità elastica](http://go.microsoft.com/?linkid=9862609)
-    Forum di discussione: [Forum sul database SQL di Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[Applicazione di esempio della scalabilità elastica]: #The-Elastic-Scale-Sample-Application
[Scaricare ed eseguire l'app di esempio]: #Download-and-Run-the-Sample-App
[Costi]: #Cost
[Passaggi successivi]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

<!--HONumber=47-->
