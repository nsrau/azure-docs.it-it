<properties
   pageTitle="Avvio rapido: soluzioni del database SQL di Azure"
   description="Informazioni sulle soluzioni del database SQL di Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="06/01/2016"
   ms.author="carlrab"/>

# Avvio rapido: esplorare soluzioni del database SQL di Azure

Questo articolo contiene informazioni di avvio rapido sulle soluzioni di database SQL di Azure. Queste procedure di avvio rapido dimostrano l'uso del database SQL in soluzioni complete basate su scenari reali. Per esercitazioni dettagliate passo per passo che illustrano l'uso di una particolare funzionalità del database SQL di Azure, vedere [Esplorare le esercitazioni sul database SQL di Azure](sql-database-explore-tutorials.md).

## Raccogliere e monitorare i dati sull'utilizzo delle risorse in più pool

Questa procedura di avvio rapido offre una soluzione per la raccolta e il monitoraggio dell'utilizzo delle risorse del database SQL di Azure su più pool in una sottoscrizione. In presenza di un numero elevato di database in una sottoscrizione, è difficile monitorare separatamente i singoli pool elastici. Per risolvere il problema, è possibile combinare i cmdlet di PowerShell per database SQL e le query T-SQL per raccogliere dati sull'utilizzo delle risorse da più pool e i relativi database per il monitoraggio e l'analisi dell'utilizzo delle risorse.

[Gestire più pool elastici nei database SQL usando PowerShell e Power BI](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) nel repository degli esempi GitHub di SQL Server fornisce un insieme di script PowerShell e query T-SQL insieme a documentazione su cosa fa e come usarlo.

## Introduzione all'uso dei pool elastici in uno scenario SaaS

Questa procedura di avvio rapido offre una soluzione per uno scenario SaaS (Software-as-a-Solution) che sfrutta i pool elastici per fornire un back-end di database economico e scalabile per un'applicazione SaaS. In questa soluzione si vedrà la procedura per l'implementazione di un'App Web che consente di visualizzare il carico creato in un pool elastico da un generatore di carico usando un dashboard personalizzato che integra il portale di Azure.

[Saas-scenario-with-elastic-pools](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) nel repository degli esempi GitHub di SQL Server è disponibile un'app per la generazione di carico e il monitoraggio insieme alla documentazione su cosa fa e come usarla.

## Creazione di un database SQL di Azure tramite Entity Framework e sviluppo con Code First

Nel video e nell'esempio seguente viene fornita un'introduzione allo sviluppo con Code First destinato alla creazione di un nuovo database. Questo scenario prevede di specificare come destinazione un database che non esiste e che verrà creato da Code First oppure un database vuoto a cui Code First aggiungerà nuove tabelle. Code First consente di definire il modello usando classi C# o VB.Net. È possibile eseguire facoltativamente una configurazione aggiuntiva con gli attributi delle classi e proprietà o usando un'API Fluent. Vedere [Code First per un nuovo database](https://msdn.microsoft.com/data/jj193542.aspx).

## Integrare strumenti di database elastici in un'applicazione Entity Framework

Questo esempio descrive le modifiche necessarie in un'applicazione Entity Framework per l'integrazione con gli [strumenti di database elastici](sql-database-elastic-scale-get-started.md). Vengono descritti in particolare la [gestione delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md) e il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) con l'approccio Code First di Entity Framework. [Code First per un nuovo database per Entity Framework](http://msdn.microsoft.com/data/jj193542.aspx) servirà da esempio per questo articolo. Il codice di esempio che accompagna questo documento fa parte del set di esempi sugli strumenti dei database elastici negli esempi di codice di Visual Studio. Vedere la libreria client di database elastici con Entity Framework in [Introduzione agli strumenti di database elastici](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md).

## Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga

Questo esempio descrive le modifiche necessarie in un'applicazione Entity Framework per l'integrazione con gli [strumenti di database elastici](sql-database-elastic-scale-get-started.md) con [sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131). Questo esempio illustra come usare insieme queste tecnologie per compilare un'applicazione con un livello dati altamente scalabile che supporta partizioni multi-tenant, tramite ADO.NET SqlClient e/o Entity Framework. Questo esempio estende la [Libreria client dei database elastici con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) aggiungendo il supporto per i database con partizioni multi-tenant. Viene compilata una semplice applicazione console per la creazione di blog e post, con quattro tenant e due database con partizioni multi-tenant. Vedere le informazioni relative ad applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga in [Introduzione agli strumenti di database elastici](sql-database-elastic-tools-multi-tenant-row-level-security.md).

## Applicazione Tailspin Surveys

Questo esempio è un'applicazione Web multi-tenant, chiamata Surveys, che consente agli utenti di creare sondaggi online. L'esempio illustra alcuni dei principali problemi quando si gestiscono le identità utente in un'applicazione multi-tenant, tra cui iscrizione, autenticazione, autorizzazione e i ruoli dell'app. Per eseguire questo esempio, vedere [How to run the Tailspin Surveys sample application](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) (Come eseguire l'applicazione di esempio Tailspin Surveys).

## Passaggi successivi

[Esplorare le esercitazioni del database SQL di Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0615_2016-->