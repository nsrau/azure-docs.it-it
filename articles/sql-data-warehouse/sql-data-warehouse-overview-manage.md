---
title: Gestire i database in Azure SQL Data Warehouse | Documentazione Microsoft
description: Panoramica della gestione dei database di SQL Data Warehouse. Include strumenti di gestione, prestazioni di DWU e di scalabilità orizzontale, risoluzione dei problemi di prestazioni delle query, definizione dei criteri di protezione e ripristino di un database da un danneggiamento dei dati o da un'interruzione dell'alimentazione locale.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: ''
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: acf521bdc15dfab4c7e43081159bc1385768838e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gestire i database in Azure SQL Data Warehouse
SQL Data Warehouse automatizza molti aspetti della gestione dei database. Ad esempio, per aumentare le prestazioni è necessario solo regolare e pagare il corretto livello di risorse di calcolo e lasciare che SQL Data Warehouse esegua tutte le operazioni di scalabilità orizzontale e scalabilità inversa.

Senza dubbio si vorrà monitorare il carico di lavoro per identificare le esigenze di prestazioni, nonché risolvere i problemi relativi a query con esecuzione prolungata. Sarà anche necessario eseguire alcune attività di sicurezza per gestire le autorizzazioni per gli utenti e gli accessi.

Questa panoramica illustra questi aspetti della gestione di SQL Data Warehouse.

* Strumenti di gestione
* Ridimensionare le risorse di calcolo
* Sospendere e riprendere
* Procedure consigliate per le prestazioni
* Monitoraggio delle query
* Sicurezza
* Eseguire il backup e il ripristino

## <a name="management-tools"></a>Strumenti di gestione
È possibile usare un'ampia gamma di strumenti per gestire i database in SQL Data Warehouse. Durante la gestione dei database si svilupperanno preferenze per determinati strumenti per ciascun tipo di attività da eseguire.

### <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure][Azure portal] è un portale basato sul Web in cui è possibile creare, aggiornare ed eliminare i database e monitorare le risorse del database. Si tratta dello strumento ideale se si sta appena iniziando a usare Azure, se si gestisce un numero ridotto di database del data warehouse o se è necessario operare rapidamente.

Per un'introduzione al portale di Azure, vedere [Creare un SQL Data Warehouse (portale di Azure)][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools in Visual Studio
[SQL Server Data Tools][SQL Server Data Tools] (SSDT) in Visual Studio consente di connettersi, gestire e sviluppare i database. È opportuno che gli sviluppatori di applicazioni con una certa familiarità con Visual Studio o altri ambienti di sviluppo integrato (IDE), provino a usare SSDT in Visual Studio.

SSDT include Esplora oggetti di SQL Server, che consente di visualizzare, connettersi ed eseguire gli script su database di SQL Data Warehouse. Per connettersi rapidamente a SQL Data Warehouse, è sufficiente fare clic sul pulsate **Apri in Visual Studio** sulla barra dei comandi quando i dettagli del database sono visualizzati nel portale di Azure.  

Per iniziare a usare SSDT in Visual Studio, vedere [Eseguire query su Azure SQL Data Warehouse con Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Strumenti da riga di comando
Gli strumenti da riga di comando sono ideali per l'automazione dei carichi di lavoro.  PowerShell e sqlcmd sono due ottimi modi per automatizzare i processi.  È consigliabile servirsi di questi strumenti per gestire un numero elevato di server logici e distribuire le modifiche relative alle risorse in un ambiente di produzione, in quanto è possibile scrivere gli script per le attività necessarie e quindi automatizzare tali attività.

### <a name="dynamic-management-views"></a>Viste a gestione dinamica
Le viste a gestione dinamica sono alla base della gestione di SQL Data Warehouse. Quasi tutte le informazioni che emergono nel portale si basano sulle viste a gestione dinamica. Per visualizzare un elenco di DMV di SQL Data Warehouse, vedere [Viste di sistema di SQL Data Warehouse][SQL Data Warehouse system views].

Per un'introduzione, vedere [Connettersi ed eseguire query con SQLCMD][Connect and query with sqlcmd] e [Creare un database (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo
In SQL Data Warehouse è possibile ridimensionare rapidamente le prestazioni aumentando o riducendo le risorse di calcolo di CPU, memoria e larghezza di banda I/O. Per ridimensionare le prestazioni, è sufficiente modificare il numero di Unità Data Warehouse (DWU) allocate al proprio database da SQL Data Warehouse. SQL Data Warehouse apporta rapidamente la modifica e gestisce tutte le modifiche sottostanti all'hardware o al software.

Per altre informazioni sulla scalabilità delle DWU, vedere la sezione [Scalare le prestazioni].

## <a name="pause-and-resume"></a>Sospendere e riprendere
Per ridurre i costi, è possibile sospendere e riprendere le risorse di calcolo su richiesta. Ad esempio, se non si usa il database durante la notte e nei fine settimana, è possibile sospenderlo in questi intervalli di tempo e riprenderne l'esecuzione durante il giorno. Mentre il database è sospeso, non verranno addebitate DWU.

Per altre informazioni, vedere le sezioni [Sospendere il calcolo][Pause compute] e [Riprendere il calcolo][Resume compute].

## <a name="performance-best-practices"></a>Procedure consigliate per le prestazioni
Quando si inizia a usare una nuova tecnologia, scoprire i suggerimenti e consigli che meglio funzionano da subito consente di risparmiare molto tempo.  Sono disponibili procedure consigliate in molti di questi argomenti.

Per vedere alcuni riepiloghi delle considerazioni più importanti nello sviluppo di un carico di lavoro, vedere [Procedure consigliate per SQL Data Warehouse][SQL Data Warehouse Best Practices].

## <a name="query-monitoring"></a>Monitoraggio delle query
A volte una query è in esecuzione da troppo tempo, ma non si è certi di quale sia la causa del problema. SQL Data Warehouse offre viste a gestione dinamica (DMV) che è possibile usare per trovare la query la cui esecuzione richiede troppo tempo.

Per trovare le query con esecuzione prolungata, vedere [Monitoraggio del carico di lavoro mediante DMV][Monitor your workload using DMVs].

## <a name="security"></a>Sicurezza
Per mantenere un sistema sicuro, è necessario vigilare e proteggersi da qualsiasi tipo di accesso non autorizzato. Un sistema di sicurezza deve accertare che siano state applicate le regole del firewall, in modo che solo gli indirizzi IP autorizzati possano connettersi. È necessaria l'autenticazione corretta delle credenziali dell'utente. Dopo che un utente si è connesso al database, l'utente deve avere le autorizzazioni solo per eseguire un numero minimo di azioni. Per proteggere i dati, è possibile usare la crittografia. È anche importante predisporre azioni di controllo e rilevamento, in modo che sia possibile ripercorrere gli eventi in caso di eventuali attività sospette.

Per altre informazioni sulla gestione della sicurezza, vedere le [informazioni generali sulla sicurezza][Security overview].

## <a name="back-up-and-restore"></a>Eseguire il backup e il ripristino
Eseguire backup affidabili dei dati è una parte fondamentale di qualsiasi database di produzione. SQL Data Warehouse tiene al sicuro i dati eseguendo automaticamente il backup dei database attivi a intervalli regolari. Questi backup consentono di eseguire il ripristino dagli scenari in cui sono stati corrotti i dati oppure sono stati eliminati accidentalmente i dati o il database.  Per informazioni relative a pianificazione del backup dei dati, criteri di conservazione e come ripristinare un database, vedere l'articolo relativo al [ripristino da snapshot][Restore from snapshot].

## <a name="next-steps"></a>Passaggi successivi
L'uso di buoni principi di progettazione dei database semplificherà la gestione dei database in SQL Data Warehouse. Per altre informazioni, vedere la [panoramica sullo sviluppo][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: pause-and-resume-compute-portal.md#pause-compute
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: pause-and-resume-compute-portal.md#resume-compute
[Scalare le prestazioni]: quickstart-scale-compute-portal.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
