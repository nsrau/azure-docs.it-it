---
title: Iniziare a utilizzare gli strumenti di database elastici
description: "Spiegazione di base della funzionalità relativa agli strumenti di database elastici del database SQL di Azure, che include un&quot;app di esempio di facile esecuzione."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f77c49c47ae7b75a2cbc1cd7521739cafdfba342
ms.lasthandoff: 04/03/2017


---
# <a name="get-started-with-elastic-database-tools"></a>Iniziare a utilizzare gli strumenti di database elastici
In questo documento viene presentata l'esperienza di sviluppo eseguendo l’app di esempio. Nell'esempio viene creata una semplice applicazione partizionata si esplorano le funzionalità chiave degli strumenti di database elastici. L'esempio illustra le funzioni della [libreria client di database elastico](sql-database-elastic-database-client-library.md)

Per installare la libreria, visitare [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Si noti che la libreria viene installata con l'app di esempio descritta di seguito.

## <a name="prerequisites"></a>Prerequisiti
1. È richiesto Visual Studio 2012 o versioni successive con C#. Scaricare una versione gratuita dalla pagina [Download di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. NuGet 2.7 o versioni successive. Per ottenere la versione più recente, vedere il documento relativo all' [installazione di NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Scaricare ed eseguire l'app di esempio
L'applicazione di esempio **Introduzione al database elastico con SQL di Azure** illustra gli aspetti più importanti dell'esperienza di sviluppo delle applicazioni partizionate usando gli strumenti di database elastici con SQL di Azure. L'applicazione è incentrata sui casi di utilizzo chiave per la [gestione delle mappe di partizione](sql-database-elastic-scale-shard-map-management.md), il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) e l'[esecuzione di query su più partizionamenti](sql-database-elastic-scale-multishard-querying.md). Per scaricare ed eseguire l'applicazione di esempio, seguire questa procedura: 

1. Scaricare l'esempio [Elastic DB Tools for Azure SQL - Getting Started](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) (Strumenti di database elastici per SQL Azure - Guida introduttiva) da MSDN. Decomprimere l'esempio in un percorso a piacere.
2. Aprire la soluzione **ElasticScaleStarterKit.sln** dalla directory **C#** per creare un progetto.
3. Aprire il file **app.config** nella soluzione per il progetto di esempio e seguire le istruzioni del file per aggiungere il nome del server di database SQL Azure e le informazioni di accesso (nome utente e password).
4. Compilare ed eseguire l'applicazione. Quando viene richiesto, consentire a Visual Studio di ripristinare i pacchetti NuGet della soluzione. In questo modo verrà scaricata la versione più recente delle librerie client del database elastico da NuGet.
5. Provare a usare le varie opzioni per ottenere altre informazioni sulle funzionalità della libreria client. Annotare i passaggi eseguiti dall'applicazione nell'output della console ed esplorare liberamente il codice sottostante.
   
    ![progress][4]

È stata creata ed eseguita la prima applicazione partizionata usando gli strumenti di database elastici nel database SQL di Azure. Esaminare rapidamente i partizionamenti creati dall'applicazione di esempio effettuando la connessione con Visual Studio o SQL Server Management Studio al server del database di Azure. Si noteranno nuovi database con partizionamento e un database di gestione delle mappe con partizionamento creati dall'applicazione di esempio.

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>Elementi chiave dell'esempio di codice
1. **Gestione di partizionamenti e di mappe con partizionamento**: il codice illustra come usare i partizionamenti, gli intervalli e i mapping nel file **ShardManagementUtils.cs**. Altre informazioni su questo argomento sono disponibili nella pagina [Gestione mappe partizioni](http://go.microsoft.com/?linkid=9862595).  
2. **Routing dipendente dai dati**: il routing delle transazioni al partizionamento appropriato è mostrato in **DataDependentRoutingSample.cs**. Per altri dettagli, vedere [Routing dipendente dai dati](http://go.microsoft.com/?linkid=9862596). 
3. **Esecuzione di query su più partizionamenti**: l'esecuzione di query su più partizionamenti è illustrata nel file **MultiShardQuerySample.cs**. Per altri dettagli, vedere [Esecuzione di query su più partizionamenti](http://go.microsoft.com/?linkid=9862597).
4. **Aggiunta di partizionamenti vuoti**: l'aggiunta iterativa di nuovi partizionamenti vuoti viene eseguita dal codice nel file **CreateShardSample.cs**. Informazioni dettagliate su questo argomento sono disponibili qui: [Gestione mappe partizioni](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Altre operazioni di scalabilità elastica
1. **Suddivisione di un partizionamento esistente**: la possibilità di suddividere partizionamenti viene fornita tramite lo **strumento di suddivisione/unione**. È possibile trovare ulteriori informazioni su questo strumento nella pagina [Panoramica sullo strumento di suddivisione/unione](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Unione di partizionamenti esistenti**: le unioni di partizioni esistenti vengono inoltre eseguite tramite lo **strumento di suddivisione/unione**. Per ulteriori informazioni, vedere [Panoramica sullo strumento di suddivisione/unione](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Costi
Gli strumenti di database elastici sono gratuiti. Gli strumenti di database elastici non comportano costi aggiuntivi oltre a quelli applicati per l'uso di Azure. 

Quindi, nel caso dei nuovi database creati dall'applicazione di esempio, il costo dipende dall'edizione del database SQL di Azure scelta e dall'uso di Azure da parte dell'applicazione.

Per informazioni sui prezzi, vedere [Dettagli prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sugli strumenti di database elastici, vedere:

* [Mappa della documentazione sugli strumenti di database elastici](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
* Esempi di codice: 
  * [Introduzione al database elastico con SQL di Azure](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [Database elastico con SQL di Azure - Integrazione con Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticità di partizionamento in Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Annuncio della scalabilità elastica](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [Implementing Scale-Out Using Sharding with the Elastic Database Client Library Video](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) (Video sull'implementazione della scalabilità orizzontale usando il partizionamento orizzontale con la libreria client di database elastici) 
* Channel 9: [Video sulla panoramica della scalabilità elastica](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Forum di discussione: [Forum sul database SQL di Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Per misurare le prestazioni: [Contatori delle prestazioni per Gestore mappe partizioni](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png


