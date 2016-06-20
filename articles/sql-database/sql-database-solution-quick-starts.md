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

## Passaggi successivi

[Esplorare le esercitazioni del database SQL di Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0608_2016-->