---
title: Concetti relativi alle query elastiche con Azure SQL Data Warehouse | Microsoft Docs
description: Concetti relativi alle query elastiche con Azure SQL Data Warehouse
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 295cc59fdb23105534b4e7431902eaa720643330
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>Come usare una query elastica con SQL Data Warehouse



Le query elastiche con Azure SQL Data Warehouse consentono di scrivere codice Transact-SQL in un database SQL inviato in remoto a un'istanza di Azure SQL Data Warehouse tramite l'uso di tabelle esterne. A seconda dello scenario, l'uso di questa funzionalità consente risparmi sui costi e architetture dalle prestazioni migliori.

Questa funzionalità consente due scenari principali:

1. Isolamento del dominio
2. Esecuzione remota di query

### <a name="domain-isolation"></a>Isolamento del dominio

L'isolamento del dominio si riferisce allo scenario di data mart classico. In alcuni scenari potrebbe essere necessario fornire un dominio logico di dati agli utenti a valle isolati dal resto del data warehouse per svariati motivi, tra cui:

1. Isolamento delle risorse: il database SQL è ottimizzato per rendere disponibili a una grande base di utenti simultanei carichi di lavoro leggermente diversi rispetto alle query analitiche di grandi dimensioni per le quali è riservato il data warehouse. L'isolamento garantisce che carichi di lavoro specifici vengano gestiti dagli strumenti più appropriati.
2. Isolamento di sicurezza: per separare un subset di dati autorizzato in modo selettivo tramite determinati schemi.
3. Sandboxing: per fornire un set di esempio di dati come "ambiente di prova" per esplorare le query di produzione e così via.

Le query elastiche possono consentire di selezionare facilmente subset di dati di SQL Data Warehouse e di spostarli in un'istanza di database SQL. Questo isolamento non esclude la possibilità di abilitare anche l'esecuzione remota di query consentendo scenari "cache" più interessanti.

### <a name="remote-query-execution"></a>Esecuzione remota di query

Le query elastiche consentono l'esecuzione remota di query in un'istanza di SQL Data Warehouse. È possibile usare il meglio del database SQL e di SQL Data Warehouse separando i dati ad accesso sporadico e quelli ad accesso frequente tra i due database. Gli utenti possono mantenere i dati più recenti in un database SQL, che può gestire report e grandi quantità di utenti aziendali medi. Tuttavia, quando sono necessari più dati o risorse di calcolo, un utente può scaricare parte della query in un'istanza di SQL Data Warehouse, in cui le aggregazioni su larga scala possono essere elaborate in modo molto più rapido ed efficiente.



## <a name="elastic-query-overview"></a>Panoramica delle query elastiche

È possibile usare una query elastica per rendere disponibili i dati presenti in SQL Data Warehouse in istanze di database SQL. La query elastica consente alle query da un database SQL di fare riferimento a tabelle in un'istanza di SQL Data Warehouse remota. 

Il primo passaggio consiste nel creare la definizione di un'origine dati esterna che fa riferimento all'istanza di SQL Data Warehouse, che usa le credenziali utente esistenti all'interno di SQL Data Warehouse. Non è necessario apportare modifiche all'istanza di SQL Data Warehouse remota. 

> [!IMPORTANT] 
> 
> L'utente deve disporre dell'autorizzazione ALTER ANY EXTERNAL DATA SOURCE. Questa autorizzazione è inclusa nell'autorizzazione ALTER DATABASE. Sono necessarie le autorizzazioni ALTER ANY EXTERNAL DATA SOURCE per fare riferimento alle origini dati remote.

Viene quindi creata una definizione di tabella esterna remota in un'istanza di database SQL che punta a una tabella remota in SQL Data Warehouse. In una query che usa una tabella esterna la parte della query che fa riferimento alla tabella esterna viene inviata all'istanza di SQL Data Warehouse per l'elaborazione. Al termine della query, il set di risultati viene inviato di nuovo all'istanza di database SQL chiamante. Per una breve esercitazione sulla configurazione di una query elastica tra il database SQL e SQL Data Warehouse, vedere [Configure Elastic Query with SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse] (Configurare una query elastica con SQL Data Warehouse).

Per altre informazioni sulla query elastica con il database SQL, vedere [Panoramica delle query elastiche del database SQL di Azure][Azure SQL Database elastic query overview].



## <a name="best-practices"></a>Procedure consigliate

### <a name="general"></a>Generale

- Quando si usa l'esecuzione remota di query, assicurarsi di selezionare solo le colonne necessarie e di applicare i filtri corretti. Questo tipo di esecuzione non solo aumenta le risorse di calcolo necessarie, ma anche le dimensioni del set di risultati e di conseguenza la quantità di dati che devono essere spostati tra le due istanze.
- Mantenere i dati per scopi analitici sia in SQL Data Warehouse sia nel database SQL in indici columnstore in cluster per prestazioni di analisi ottimali.
- Assicurarsi che le tabelle di origine siano partizionate per lo spostamento di dati e query.
- Assicurarsi che le istanze del database SQL usate come cache siano partizionate per consentire aggiornamenti più granulari e una gestione semplificata. 
- Idealmente, usare database PremiumRS, perché forniscono i vantaggi in termini di analisi degli indici columnstore in cluster, in particolare per i carichi di lavoro con I/O elevato, a un prezzo scontato rispetto ai database Premium.
- Dopo il caricamento, usare colonne di identificazione della data di validità o di caricamento per gli upsert nelle istanze del database SQL in modo da mantenere l'integrità dell'origine della cache. 
- Creare un account di accesso e un utente separati nell'istanza di SQL Data Warehouse per le credenziali di accesso remoto del database SQL definite nell'origine dati esterna. 

### <a name="elastic-querying"></a>Esecuzione di query elastiche

- La tabella esterna e la tabella memorizzata nella cache internamente sono presenti come oggetti diversi con l'istanza di database SQL. Prendere in considerazione la creazione di una visualizzazione sulla parte memorizzata nella cache della tabella e la tabella esterna, in modo da unire entrambe le tabelle e applicare filtri nel punto di limite di ogni tabella.

  Si supponga di voler mantenere l'anno più recente di dati in un'istanza di database SQL. Sono disponibili due tabelle: **ext.Orders**, che fa riferimento alle tabelle degli ordini del data warehouse, e **dbo.Orders**, che rappresenta i dati corrispondenti agli anni più recenti nell'istanza di database SQL. Invece di chiedere agli utenti di scegliere se eseguire query su una tabella o un'altra, creare una visualizzazione sulla parte superiore di entrambe le tabelle nel punto di partizione dell'anno più recente.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  Una visualizzazione creata in questo modo consente al compilatore di query di determinare se deve usare l'istanza del data warehouse per rispondere alla query degli utenti. 

  Si verifica un sovraccarico dovuto all'invio, alla compilazione, all'esecuzione e allo spostamento dei dati associati a ogni query elastica sull'istanza del data warehouse. Tenere presente che ogni query elastica viene conteggiata rispetto agli slot di concorrenza e usa risorse.  


- Se si prevede di eseguire ulteriormente il drill-down nel set dei risultati dall'istanza del data warehouse, provare a materializzarlo in una tabella temporale nel database SQL per ottimizzare le prestazioni e impedire l'utilizzo non necessario delle risorse.

### <a name="moving-data"></a>Spostamento dei dati 

- Se possibile, semplificare la gestione dati con tabelle di origine solo di accodamento in modo che gli aggiornamenti siano facilmente gestibili tra le istanze del data warehouse e del database.
- Spostare i dati a livello di partizione con la semantica di scaricamento e riempimento per ridurre al minimo il costo della query nel livello del data warehouse e la quantità di dati spostati da mantenere aggiornati nell'istanza di database. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Quando scegliere Azure Analysis Services o il database SQL

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- Si prevede di usare la cache con uno strumento di business intelligence che invia un numero elevato di query di piccole dimensioni
- È necessario latenza di query dell'ordine di frazioni di secondo
- Si ha esperienza nella gestione e/o nello sviluppo di modelli per Analysis Services 

#### <a name="sql-database"></a>Database SQL

- Si vuole eseguire query sui dati della cache con SQL
- È necessario usare l'esecuzione remota per determinate query
- I requisiti richiedono una cache di dimensioni maggiori



## <a name="faq"></a>domande frequenti

D: È possibile usare i database all'interno di un pool di database elastico con una query elastica?

A: Sì. I database SQL all'interno di un pool elastico possono usare una query elastica. 

D: Il numero di database che è possibile usare per la query elastica è limitato?

R: I server logici sono soggetti a limiti di DTU per impedire ai clienti di spendere accidentalmente più del dovuto. Se si abilitano diversi database per la query elastica insieme a un'istanza di SQL Data Warehouse, si potrebbe raggiungere il limite in modo imprevisto. In questo caso, inviare una richiesta per aumentare il limite di DTU per il server logico. È possibile aumentare la quota [creando un ticket di supporto](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e selezionando *Quota* come tipo di richiesta

D: È possibile usare la sicurezza a livello di riga/Dynamic Data Masking con una query elastica?

R: I clienti che vogliono usare le funzionalità di sicurezza più avanzate con il database SQL devono innanzitutto spostare e archiviare i dati nel database SQL. Attualmente non è possibile applicare la sicurezza a livello di riga o DDM su dati sottoposti a query tramite tabelle esterne. 

D: È possibile scrivere dall'istanza di database SQL all'istanza del data warehouse?

R: Questa funzionalità non è attualmente supportata. Per creare/votare questa funzionalità se si vuole che venga aggiunta in futuro, visitare la [pagina dei commenti e suggerimenti] [Feedback page]. 

D: È possibile usare i tipi di dati spaziali come geometria/geografia?

R: È possibile archiviare i tipi di dati spaziali in SQL Data Warehouse come valori varbinary(max). Quando si esegue una query elastica su queste colonne, è possibile convertirle nei tipi appropriati in fase di esecuzione.

![tipi di dati spaziali](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->