---
title: Introduzione a strumenti di Database elastico - Azure | Documenti Microsoft
description: "Descrizione generale della funzionalità Strumenti di Database elastico del Database SQL di Azure, tra cui un'app di esempio to run."
services: sql-database
documentationcenter: 
manager: jstrauss
author: anumjs
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: anjangsh
ms.openlocfilehash: 615e5b0bf299a844ea2d37476fc704c48e17b363
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Introduzione a strumenti di Database elastico
Questo documento viene presentato l'esperienza dello sviluppatore per la [libreria client di database elastico](sql-database-elastic-database-client-library.md) grazie alla possibilità eseguire un'app di esempio. L'app di esempio crea una semplice applicazione partizionata ed Esplora le funzionalità principali della funzionalità Strumenti di Database elastico del Database SQL di Azure. L'applicazione è incentrata sui casi d'uso per la [gestione delle mappe delle partizioni](sql-database-elastic-scale-shard-map-management.md), il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) e l'[esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md). La libreria client è disponibile per .NET come linguaggio. 

## <a name="elastic-database-tools-for-java"></a>Strumenti di Database elastici per Java
### <a name="prerequisites"></a>Prerequisiti
* Un Java Developer Kit (JDK), versione 1.8 o versioni successive
* [Maven](http://maven.apache.org/download.cgi)
* Un server logico in Azure o un'istanza di SQL Server locale

### <a name="download-and-run-the-sample-app"></a>Scaricare ed eseguire l'app di esempio
Per compilare i file JAR e iniziare a utilizzare il progetto di esempio, eseguire le operazioni seguenti: 
1. Clone di [repository GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) contenente la libreria client, insieme all'app di esempio. 

2. Modificare il _./sample/src/main/resources/resource.properties_ file per configurare quanto segue:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Per compilare il progetto di esempio, nel _. esempio_ directory, eseguire il comando seguente:

    ```
    mvn install
    ```
    
4. Per avviare il progetto di esempio, nel _. esempio_ directory, eseguire il comando seguente: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Per ulteriori informazioni sulle funzionalità della libreria client, sperimentare le varie opzioni. È possibile esplorare il codice per avere informazioni sull'implementazione di app di esempio.

    ![Progress-java][5]
    
Congratulazioni! Avere correttamente compilato ed eseguito la prima applicazione partizionata usando gli strumenti di Database elastico nel Database SQL Azure. Usare Visual Studio o SQL Server Management Studio per connettersi al database SQL e osservare rapidamente le partizioni create dall'esempio. Si noteranno nuovi database con partizionamento e un database di gestione delle mappe con partizionamento creati dall'applicazione di esempio. 

Per aggiungere la libreria client a un progetto di Maven, aggiungere la dipendenza seguente nel file POM:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Strumenti di Database elastici per .NET 
### <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2012 o versione successiva con C#. Scaricare una versione gratuita dalla pagina [Download di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 o versione successiva. Per ottenere la versione più recente, vedere [Installing NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) (Installazione di NuGet).

### <a name="download-and-run-the-sample-app"></a>Scaricare ed eseguire l'app di esempio
Per installare la libreria, visitare [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). La libreria viene installata con l'app di esempio descritta nella sezione seguente.

Per scaricare ed eseguire l'applicazione di esempio, seguire questa procedura: 

1. Scaricare l'esempio [Elastic DB Tools for Azure SQL - Getting Started](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) (Strumenti di database elastici per SQL Azure - Guida introduttiva) da MSDN. Decomprimere l'esempio nel percorso desiderato.

2. Per creare un progetto, aprire la soluzione *ElasticScaleStarterKit.sln* dalla directory *C#*.

3. Nella soluzione per l'esempio di progetto aprire il file *app.config*. Seguire le istruzioni nel file per aggiungere il nome del server di Database SQL di Azure e le informazioni di accesso (nome utente e password).

4. Compilare ed eseguire l'applicazione. Quando richiesto, consentire a Visual Studio ripristinare i pacchetti NuGet della soluzione. Questa azione Scarica la versione più recente della libreria client di database elastico da NuGet.

5. Per ulteriori informazioni sulle funzionalità della libreria client, sperimentare le varie opzioni. Si noti la procedura che accetta l'applicazione nella console di output e frattempo è possibile esplorare il codice dietro le quinte.
   
    ![Avanzamento][4]

Congratulazioni! Avere correttamente compilato ed eseguito la prima applicazione partizionata usando gli strumenti di Database elastico nel Database SQL. Usare Visual Studio o SQL Server Management Studio per connettersi al database SQL e osservare rapidamente le partizioni create dall'esempio. Si noteranno nuovi database con partizionamento e un database di gestione delle mappe con partizionamento creati dall'applicazione di esempio.

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti per Azure e per il database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>Elementi chiave dell'esempio di codice
* **Esegue il mapping di gestione di partizioni e partizioni**: il codice viene illustrato l'utilizzo con mapping in partizioni e gli intervalli di *ShardManagementUtils.cs* file. Per altre informazioni, vedere [Aumentare il numero di istanze dei database con il gestore delle mappe partizioni](http://go.microsoft.com/?linkid=9862595).  

* **Routing dipendente dai dati**: Routing di transazioni per la partizione di destra viene visualizzato nel *DataDependentRoutingSample.cs* file. Per altre informazioni, vedere [Routing dipendente dai dati](http://go.microsoft.com/?linkid=9862596). 

* **Esecuzione di query su più partizioni**: l'esecuzione di query tra partizioni è illustrato nel *MultiShardQuerySample.cs* file. Per altre informazioni, vedere [Esecuzione di query su più partizioni](http://go.microsoft.com/?linkid=9862597).

* **Aggiunta di partizioni vuote**: l'iterativo l'aggiunta di nuove partizioni vuote viene eseguita dal codice nel *CreateShardSample.cs* file. Per altre informazioni, vedere [Aumentare il numero di istanze dei database con il gestore delle mappe partizioni](http://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Altre operazioni di scalabilità elastica
* **Suddividere una partizione esistente**: la possibilità di suddividere le partizioni viene fornita dallo strumento di unione di menu combinato. Per altre informazioni, vedere [Spostamento di dati tra database cloud con scalabilità orizzontale](sql-database-elastic-scale-overview-split-and-merge.md).

* **L'unione delle partizioni esistenti**: unione di partizioni vengono eseguite anche tramite lo strumento di unione di menu combinato. Per altre informazioni, vedere [Spostamento di dati tra database cloud con scalabilità orizzontale](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Costi
La libreria di strumenti di Database elastico è disponibile. Quando si utilizzano strumenti di Database elastico spese non aggiuntive oltre il costo dell'uso di Azure. 

Quindi, nel caso dei nuovi database creati dall'applicazione di esempio, Il costo di questa funzionalità dipende è scegliere l'edizione del Database SQL e l'utilizzo di Azure dell'applicazione.

Per informazioni sui prezzi, vedere [Prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sugli strumenti di Database elastico, vedere gli articoli seguenti:

* Esempi di codice: 
  * Strumenti di database elastici ([.NET](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Strumenti di Database elastici per SQL Azure - integrazione di Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticità di partizionamento in Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Elastic Scale announcement](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/) (Annuncio della scalabilità elastica)
* Microsoft Virtual Academy: [Implementing Scale-Out Using Sharding with the Elastic Database Client Library Video](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) (Video sull'implementazione della scalabilità orizzontale usando il partizionamento orizzontale con la libreria client di database elastici) 
* Channel 9: [video di panoramica della scalabilità elastica](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Forum di discussione: [forum sul database SQL di Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
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
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

