<properties
   pageTitle="Avvio rapido: soluzioni del database SQL di Azure"
   description="Informazioni sulle soluzioni del database SQL di Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# Avvio rapido: esplorare soluzioni del database SQL di Azure

Questo articolo contiene informazioni di avvio rapido sulle soluzioni di database SQL di Azure. Queste guide di avvio rapido si trovano nel repository degli esempi di SQL Server su GitHub e illustrano l'utilizzo del database SQL in una soluzione completa basata su scenari reali. Per esercitazioni dettagliate passo per passo che illustrano l'uso di una particolare funzionalità del database SQL, vedere [Esplorare le esercitazioni sul database SQL di Azure](sql-database-explore-tutorials.md).

## Eseguire la demo WingTipTickets e l'esercitazione pratica

La demo e l'esercitazione pratica di [Azure SQL Database WingTipTickets](https://github.com/microsoft/wingtiptickets) illustrano un'applicazione di esempio basata su un database SQL di Azure e su Ricerca di Azure usata per la vendita di biglietti per un concerto.


## Raccogliere e monitorare i dati sull'utilizzo delle risorse in più pool

La [Guida introduttiva: Telemetria di pool elastici con PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) offre una soluzione per la raccolta e il monitoraggio dell'utilizzo delle risorse del database SQL su più pool in una sottoscrizione. In presenza di un numero elevato di database in una sottoscrizione, è difficile monitorare separatamente i singoli pool elastici.

Per risolvere il problema, è possibile combinare i cmdlet di PowerShell per database SQL e le query T-SQL per raccogliere dati sull'utilizzo delle risorse da più pool e i relativi database. Ciò consente di monitorare e analizzare in modo più efficiente l'utilizzo delle risorse.

Questa Guida introduttiva fornisce un set di script di PowerShell e query T-SQL, oltre alla documentazione sul funzionamento della soluzione e su come implementarla.

## Introduzione ai database elastici in uno scenario SaaS

 La [Guida introduttiva: dashboard personalizzato di pool elastico per SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) offre una soluzione per uno scenario SaaS (Software-as-a-Solution) che utilizza la funzionalità di database elastico del database SQL per fornire un back-end di database economico e scalabile per un'applicazione SaaS.

In questa soluzione viene illustrata in dettaglio l'implementazione di un'app Web. Questa app Web consente di visualizzare il carico creato in un database elastico da un generatore di carico che utilizza un dashboard personalizzato a integrazione del portale di Azure.

Questa Guida introduttiva presenta un generatore di carico e un'app Web di monitoraggio, insieme alla documentazione sulle finalità dell'applicazione e su come utilizzarla.

## Creare un database SQL di Azure tramite Entity Framework e sviluppo con Code First

Nel video e nell'esempio su [Code First in un nuovo database](https://msdn.microsoft.com/data/jj193542.aspx) viene fornita un'introduzione allo sviluppo con Code First destinato alla creazione di un nuovo database. Questo scenario è destinato a un database che non esiste, ma che verrà creato da Code First. In alternativa, lo scenario crea un database vuoto a cui vengono aggiunte nuove tabelle con Code First.

Code First consente di definire il modello utilizzando le classi C# o Visual Basic .NET. Facoltativamente, è possibile eseguire una configurazione aggiuntiva con gli attributi delle classi e proprietà o usando un'API Fluent.

## Integrare strumenti di database elastici in un'applicazione Entity Framework

L'esempio [Libreria client dei database elastici con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) illustra le modifiche da apportare a un'applicazione Entity Framework per integrarla negli [strumenti di database elastici](sql-database-elastic-scale-get-started.md). Vengono descritti in particolare la [gestione delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md) e il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) con l'approccio Code First di Entity Framework.

[Code First per un nuovo database per Entity Framework](http://msdn.microsoft.com/data/jj193542.aspx) servirà da esempio per questo articolo. Il codice di esempio che accompagna questo documento fa parte del set di esempi sugli strumenti dei database elastici negli esempi di codice di Visual Studio.

## Integrazione degli strumenti di database elastici con protezione a livello di riga

[Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md) illustra le modifiche da apportare a un'applicazione Entity Framework per integrare gli [strumenti di database elastici](sql-database-elastic-scale-get-started.md) con la [sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131). Questo esempio illustra come usare insieme queste tecnologie per compilare un'applicazione con un livello dati altamente scalabile che supporta partizioni multi-tenant.

Questa operazione può essere eseguita con ADO.NET SqlClient o Entity Framework. Questo esempio estende la [Libreria client dei database elastici con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) aggiungendo il supporto per i database con partizioni multi-tenant. Viene compilata una semplice applicazione console per la creazione di blog e post, con quattro tenant e due database con partizioni multi-tenant.

## Creare sondaggi online con l'applicazione Tailspin Surveys

L'[applicazione di esempio Tailspin Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) è un'applicazione Web multi-tenant che consente agli utenti di creare sondaggi online. L'esempio illustra alcuni problemi comuni quando si gestiscono le identità utente in un'applicazione multi-tenant, tra cui iscrizione, autenticazione, autorizzazione e ruoli dell'app.

## Scopri le ultime funzionalità di sicurezza dei database SQL nell'applicazione demo di Contoso

L'[applicazione demo di Contoso](https://github.com/Microsoft/azure-sql-security-sample) illustra le ultime funzionalità di sicurezza dei database SQL.

## Passaggi successivi

[Esplorare le esercitazioni sul database SQL di Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0907_2016-->