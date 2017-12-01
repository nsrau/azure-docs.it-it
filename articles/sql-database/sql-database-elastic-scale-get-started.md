---
title: Introduzione ai processi di database elastici | Microsoft Docs
description: "Descrizione di base della funzionalità relativa agli strumenti di database elastici del database SQL di Azure, che include un'app di esempio di facile esecuzione."
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
ms.openlocfilehash: e7e072e310cabc2c4520df7e9f4f9e45b8218998
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Iniziare a utilizzare gli strumenti di database elastici
Questo documento presenta l'esperienza di sviluppo per la [libreria client del database elastico](sql-database-elastic-database-client-library.md) tramite l'esecuzione dell'app di esempio. Nell'esempio viene creata una semplice applicazione partizionata si esplorano le funzionalità chiave degli strumenti di database elastici. L'applicazione è incentrata sui casi d'uso per la [gestione delle mappe delle partizioni](sql-database-elastic-scale-shard-map-management.md), il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) e l'[esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md). La libreria client è disponibile su .Net e Java. 

## <a name="elastic-database-tools-for-java"></a>Strumenti di database elastici per Java
### <a name="prerequisites"></a>Prerequisiti
* Java Developer Kit (JDK) versione 1.8 o successiva
* [Maven](http://maven.apache.org/download.cgi)
* Un server logico in Azure o in un SQL Server locale

### <a name="download-and-run-the-sample-app"></a>Scaricare ed eseguire l'app di esempio
Eseguire i passaggi seguenti per compilare i file JAR e iniziare a usare il progetto di esempio: 
1. Clonare il [repository GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) che contiene la libreria client e l'app di esempio. 
2. Modificare il file _./sample/src/main/resources/resource.properties_ nel modo seguente.
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME
3. Nella directory _./sample_ eseguire il comando seguente per compilare il progetto di esempio.<br>

    ```
    mvn install
    ```
    
4. Nella directory _./sample_ eseguire il comando seguente per avviare il progetto di esempio. 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Provare a usare le varie opzioni per scoprire di più sulle funzionalità della libreria client. È possibile esplorare il codice per avere informazioni sull'implementazione di app di esempio.

    ![Progress-java][5]
    
Congratulazioni. È stata creata ed eseguita la prima applicazione partizionata usando gli strumenti di database elastici nel database SQL. Usare Visual Studio o SQL Server Management Studio per connettersi al database SQL e osservare rapidamente le partizioni create dall'esempio. Si noteranno nuovi database con partizionamento e un database di gestione delle mappe con partizionamento creati dall'applicazione di esempio. Per aggiungere la libreria client al progetto Maven, aggiungere la dipendenza seguente al file POM.<br>

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Strumenti di database elastici per .Net 
### <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2012 o versione successiva con C#. Scaricare una versione gratuita dalla pagina [Download di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 o versione successiva. Per ottenere la versione più recente, vedere [Installing NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) (Installazione di NuGet).

### <a name="download-and-run-the-sample-app"></a>Scaricare ed eseguire l'app di esempio
Per installare la libreria, visitare [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). La libreria viene installata con l'app di esempio descritta nella sezione seguente.

Per scaricare ed eseguire l'applicazione di esempio, seguire questa procedura: 

1. Scaricare l'esempio [Elastic DB Tools for Azure SQL - Getting Started](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) (Strumenti di database elastici per SQL Azure - Guida introduttiva) da MSDN. Decomprimere l'esempio nel percorso desiderato.

2. Per creare un progetto, aprire la soluzione **ElasticScaleStarterKit.sln** dalla directory **C#**.

3. Nella soluzione per l'esempio di progetto aprire il file **app.config**. Seguire quindi le istruzioni nel file per aggiungere il nome del server del database SQL di Azure e le informazioni di accesso (nome utente e password).

4. Compilare ed eseguire l'applicazione. Quando viene richiesto, consentire a Visual Studio di ripristinare i pacchetti NuGet della soluzione. Viene scaricata la versione più recente della libreria client dei database elastici da NuGet.

5. Provare a usare le varie opzioni per scoprire di più sulle funzionalità della libreria client. Annotare i passaggi eseguiti dall'applicazione nell'output della console ed esplorare liberamente il codice sottostante.
   
    ![Avanzamento][4]

È stata creata ed eseguita la prima applicazione partizionata usando gli strumenti di database elastici nel database SQL. Usare Visual Studio o SQL Server Management Studio per connettersi al database SQL e osservare rapidamente le partizioni create dall'esempio. Si noteranno nuovi database con partizionamento e un database di gestione delle mappe con partizionamento creati dall'applicazione di esempio.

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti per Azure e per il database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>Elementi chiave dell'esempio di codice
* **Gestione di partizioni e mappe delle partizioni**: il codice mostra come usare le partizioni, gli intervalli e i mapping nel file **ShardManagementUtils.cs**. Per altre informazioni, vedere [Aumentare il numero di istanze dei database con il gestore delle mappe partizioni](http://go.microsoft.com/?linkid=9862595).  

* **Routing dipendente dai dati**: il routing delle transazioni nella partizione appropriata viene mostrato in **DataDependentRoutingSample.cs**. Per altre informazioni, vedere [Routing dipendente dai dati](http://go.microsoft.com/?linkid=9862596). 

* **Esecuzione di query su più partizioni**: l'esecuzione di query su più partizioni viene mostrata nel file **MultiShardQuerySample.cs**. Per altre informazioni, vedere [Esecuzione di query su più partizioni](http://go.microsoft.com/?linkid=9862597).

* **Aggiunta di partizioni vuote**: l'aggiunta iterativa di nuove partizioni vuote viene eseguita dal codice nel file **CreateShardSample.cs**. Per altre informazioni, vedere [Aumentare il numero di istanze dei database con il gestore delle mappe partizioni](http://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Altre operazioni di scalabilità elastica
* **Divisione di una partizione esistente**: la possibilità di dividere partizioni viene fornita tramite lo **strumento di divisione-unione**. Per altre informazioni, vedere [Spostamento di dati tra database cloud con scalabilità orizzontale](sql-database-elastic-scale-overview-split-and-merge.md).

* **Unione di partizioni esistenti**: le unioni di partizioni vengono eseguite anche tramite lo **strumento di divisione-unione**. Per altre informazioni, vedere [Spostamento di dati tra database cloud con scalabilità orizzontale](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Costi
Gli strumenti di database elastici sono gratuiti. L'uso di strumenti di database elastici non comporta costi aggiuntivi oltre a quelli applicati per l'utilizzo di Azure. 

Quindi, nel caso dei nuovi database creati dall'applicazione di esempio, il costo dipende dall'edizione del database SQL scelta e dall'utilizzo di Azure da parte dell'applicazione.

Per informazioni sui prezzi, vedere [Prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli strumenti di database elastici, vedere le pagine seguenti:

* Esempi di codice: 
  * Strumenti di database elastici ([.NET](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastic DB Tools for Azure SQL - Entity Framework Integration](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE) (Strumenti di database elastici per SQL di Azure - Integrazione con Entity Framework)
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

