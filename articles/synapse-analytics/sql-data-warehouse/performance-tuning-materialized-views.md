---
title: Ottimizzazione delle prestazioni con viste materializzate
description: Consigli e considerazioni da conoscere quando si utilizzano viste materializzate per migliorare le prestazioni delle query.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 28dbe66d27ac75bee2ceecd160ead3dea6538034
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633823"
---
# <a name="performance-tuning-with-materialized-views"></a>Ottimizzazione delle prestazioni con viste materializzate

Le viste materializzate nel pool SQL Synapse forniscono un metodo di manutenzione basso per query analitiche complesse per ottenere prestazioni veloci senza alcuna modifica delle query. In questo articolo vengono illustrate le indicazioni generali sull'utilizzo delle visualizzazioni materializzate.

Le viste materializzate in Azure SQL Data Warehouse forniscono un metodo di bassa manutenzione per query analitiche complesse per ottenere prestazioni veloci senza alcuna modifica delle query. In questo articolo vengono illustrate le indicazioni generali sull'utilizzo delle visualizzazioni materializzate.

## <a name="materialized-views-vs-standard-views"></a>Viste materializzate e viste standard

Il pool SQL supporta le viste standard e materializzate.  Entrambe sono tabelle virtuali create con espressioni SELECT e presentate alle query come tabelle logiche.  Le viste incapsulano la complessità del calcolo dei dati comuni e aggiungono un livello di astrazione alle modifiche di calcolo in modo che non sia necessario riscrivere le query.  

Una visualizzazione standard calcola i dati ogni volta che viene utilizzata.  Non ci sono dati memorizzati su disco. Gli utenti utilizzano in genere le viste standard come strumento che consente di organizzare gli oggetti logici e le query in un database.  Per utilizzare una vista standard, una query deve fare riferimento diretto ad essa.

Una vista materializzata pre-calcola, archivia e gestisce i dati nel pool SQL come una tabella.  Non è necessario rieseguire il ricalcolo ogni volta che viene utilizzata una vista materializzata.  Ecco perché le query che utilizzano tutti o sottoinsiemi dei dati nelle viste materializzate possono ottenere prestazioni più veloci.  Ancora meglio, le query possono utilizzare una vista materializzata senza fare riferimento diretto ad essa, quindi non è necessario modificare il codice dell'applicazione.  

La maggior parte dei requisiti in una vista standard si applica ancora a una vista materializzata. Per informazioni dettagliate sulla sintassi della vista materializzata e su altri requisiti, fare riferimento a [CREATE MATERIALIZED VIEW AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).

| Confronto                     | Visualizza                                         | Vista materializzata
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Visualizzare la definizione                 | Archiviato nel pool SQL.              | Archiviato nel pool SQL.
|Visualizzare il contenuto                    | Generato ogni volta che viene utilizzata la vista.   | Pre-elaborato e archiviato nel pool SQL durante la creazione della vista. Aggiornato quando i dati vengono aggiunti alle tabelle sottostanti.
|Aggiornamento dati                    | Sempre aggiornato                               | Sempre aggiornato
|Velocità per recuperare i dati di visualizzazione da query complesse     | Lento                                         | Veloce  
|Spazio di archiviazione aggiuntivo                   | No                                           | Sì
|Sintassi                          | CREATE VIEW                                  | CREA VISTA MATERIALIZZATA COME SELEZIONE

## <a name="benefits-of-using-materialized-views"></a>Vantaggi dell'utilizzo di viste materializzate

Una vista materializzata progettata correttamente può fornire i seguenti vantaggi:

- Ridurre il tempo di esecuzione per le query complesse con JOIN e funzioni di aggregazione. Maggiore è la complessità della query, maggiore è il potenziale di risparmio in fase di esecuzione. Il massimo vantaggio si ottiene quando il costo di calcolo di una query è elevato e il set di dati risultante è ridotto.  
- L'ottimizzatore nel pool SQL può utilizzare automaticamente le viste materializzate distribuite per migliorare i piani di esecuzione delle query.  Questo processo è trasparente per gli utenti che forniscono prestazioni di query più veloci e non richiedono query per fare riferimento diretto alle viste materializzate.
- Richiedere una manutenzione bassa sulle viste.  Tutte le modifiche incrementali ai dati delle tabelle di base vengono aggiunte automaticamente alle viste materializzate in modo sincrono.  Questa progettazione consente l'esecuzione di query sulle viste materializzate per restituire gli stessi dati dell'esecuzione diretta di query sulle tabelle di base.
- I dati in una vista materializzata possono essere distribuiti in modo diverso dalle tabelle di base.  
- I dati nelle viste materializzate ottengano gli stessi vantaggi di disponibilità elevata e resilienza dei dati nelle tabelle normali.  

Le viste materializzate implementate nel pool SQL offrono inoltre i vantaggi aggiuntivi seguenti:The materialized views implemented in SQL pool also provide the following additional benefits:

Rispetto ad altri provider di data warehouse, le viste materializzate implementate in Azure SQL Data Warehouse offrono anche i vantaggi aggiuntivi seguenti:Comparing to other data warehouse providers, the materialized views implemented in Azure SQL Data Warehouse also provide the following additional benefits:

- Aggiornamento automatico e sincrono dei dati con modifiche dei dati nelle tabelle di base. Non è richiesta alcuna azione da parte dell'utente.
- Ampio supporto per le funzioni di aggregazione. Vedere [CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).
- Supporto per la raccomandazione della vista materializzata specifica per le query.  Vedere [EXPLAIN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Scenari comuni  

Le viste materializzate vengono in genere utilizzate negli scenari seguenti:Materialized views are typically used in following scenarios:

**Necessità di migliorare le prestazioni di query analitiche complesse su dati di grandi dimensioni**

Le query analitiche complesse utilizzano in genere più funzioni di aggregazione e join di tabelle, causando operazioni più complesse come shuffle e join nell'esecuzione delle query.  Ecco perché il completamento di tali query richiede più tempo, in particolare su tabelle di grandi dimensioni.  

Gli utenti possono creare viste materializzate per i dati restituiti dai calcoli comuni delle query, quindi non è necessario eseguire il ricalcolo quando questi dati sono necessari per le query, consentendo costi di calcolo inferiori e risposta alle query più rapida.

**Necessità di prestazioni più veloci senza modifiche alle query o senza modifiche minime**

Le modifiche allo schema e alle query nei pool SQL vengono in genere ridotte al minimo per supportare le normali operazioni ETL e la creazione di report.  Gli utenti possono utilizzare viste materializzate per l'ottimizzazione delle prestazioni delle query, se il costo sostenuto dalle viste può essere compensato dal miglioramento delle prestazioni delle query.

Rispetto ad altre opzioni di ottimizzazione, ad esempio la scalabilità e la gestione delle statistiche, si tratta di una modifica di produzione molto meno impattante per creare e mantenere una vista materializzata e il suo potenziale miglioramento delle prestazioni è anche superiore.

- La creazione o la gestione di viste materializzate non influisce sulle query in esecuzione sulle tabelle di base.
- Query Optimizer può utilizzare automaticamente le viste materializzate distribuite senza riferimento diretto alla vista in una query. Questa funzionalità riduce la necessità di modifica delle query nell'ottimizzazione delle prestazioni.

**Necessità di strategia di distribuzione dei dati diversa per prestazioni delle query più veloci**

Il pool SQL è un sistema di elaborazione parallela (MPP) distribuito.   I dati in una tabella del pool SQL vengono distribuiti tra 60 nodi utilizzando una delle tre strategie di [distribuzione](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (hash, round_robin o replicate).  

La distribuzione dei dati viene specificata al momento della creazione della tabella e rimane invariata fino a quando la tabella non viene eliminata. La vista materializzata come tabella virtuale su disco supporta le distribuzioni di dati hash e round_robin.  Gli utenti possono scegliere una distribuzione dei dati diversa dalle tabelle di base ma ottimale per le prestazioni delle query che utilizzano maggiormente le viste.  

## <a name="design-guidance"></a>Indicazioni per la progettazione

Di seguito sono riportate le indicazioni generali sull'utilizzo di viste materializzate per migliorare le prestazioni delle query:Here is the general guidance on using materialized views to improve query performance:

**Progettare per il carico di lavoro**

Prima di iniziare a creare viste materializzate, è importante avere una conoscenza approfondita del carico di lavoro in termini di modelli di query, importanza, frequenza e dimensioni dei dati risultanti.  

Gli utenti possono eseguire EXPLAIN WITH_RECOMMENDATIONS <> SQL_statement per le viste materializzate consigliate da Query Optimizer.  Poiché queste raccomandazioni sono specifiche delle query, una vista materializzata che offre vantaggi a una singola query potrebbe non essere ottimale per altre query nello stesso carico di lavoro.  

Valutare questi consigli tenendo presenti le esigenze di carico di lavoro.  Le viste materializzate ideali sono quelle che beneficiano delle prestazioni del carico di lavoro.  

**Essere consapevoli del compromesso tra query più veloci e il costo**

Per ogni vista materializzata, è previsto un costo di archiviazione dei dati e un costo per la gestione della vista.  Man mano che i dati cambiano nelle tabelle di base, le dimensioni della vista materializzata aumentano e cambia anche la sua struttura fisica.  Per evitare la riduzione delle prestazioni delle query, ogni vista materializzata viene gestita separatamente dal motore del pool SQL.  

Il carico di lavoro di manutenzione aumenta quando aumenta il numero di viste materializzate e le modifiche alla tabella di base.   Gli utenti devono verificare se il costo sostenuto da tutte le viste materializzate può essere compensato dal miglioramento delle prestazioni delle query.  

È possibile eseguire questa query per l'elenco delle viste materializzate in un database:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opzioni per ridurre il numero di viste materializzate:

- Identificare i set di dati comuni utilizzati di frequente dalle query complesse nel carico di lavoro.  Creare viste materializzate per archiviare tali set di dati in modo che l'ottimizzatore possa utilizzarli come blocchi predefiniti durante la creazione di piani di esecuzione.  

- Eliminare le viste materializzate con un utilizzo basso o che non sono più necessarie.  Una vista materializzata disabilitata non viene mantenuta, ma comporta comunque costi di archiviazione.  

- Combinare viste materializzate create nelle tabelle di base uguali o simili anche se i relativi dati non si sovrappongono.  La pettinatura delle viste materializzate potrebbe comportare una dimensione di visualizzazione maggiore rispetto alla somma delle viste separate, tuttavia il costo di manutenzione della vista dovrebbe ridurmi.  Ad esempio:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Non tutte le ottimizzazioni delle prestazioni richiedono la modifica delle query**

L'ottimizzatore del pool SQL può utilizzare automaticamente le viste materializzate distribuite per migliorare le prestazioni delle query.  Questo supporto viene applicato in modo trasparente alle query che non fanno riferimento alle viste e alle query che utilizzano aggregazioni non supportate nella creazione di viste materializzate.  Non è necessaria alcuna modifica della query. È possibile controllare il piano di esecuzione stimato di una query per verificare se viene utilizzata una vista materializzata.  

**Monitorare le viste materializzate**

Una vista materializzata viene archiviata nel pool SQL come una tabella con indice columnstore cluster (CCI).  La lettura dei dati da una vista materializzata include la scansione dei segmenti dell'indice CCI e l'applicazione di eventuali modifiche incrementali dalle tabelle di base. Quando il numero di modifiche incrementali è troppo elevato, la risoluzione di una query da una vista materializzata può richiedere più tempo rispetto all'esecuzione diretta di query sulle tabelle di base.  

Per evitare una riduzione delle prestazioni delle query, è consigliabile eseguire [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) per monitorare le overhead_ratio della visualizzazione (total_rows / max(1, base_view_row)).  Gli utenti devono REBUILDrizzare la vista materializzata se il suo overhead_ratio è troppo alto.

**Visualizzazione materializzata e memorizzazione nella cache del set di risultati**

Queste due funzionalità vengono introdotte nel pool SQL insieme allo stesso tempo per l'ottimizzazione delle prestazioni delle query.  La memorizzazione nella cache del set di risultati viene utilizzata per ottenere una concorrenza elevata e una risposta rapida da query ripetitive su dati statici.  

Per utilizzare il risultato memorizzato nella cache, il modulo della query di richiesta della cache deve corrispondere alla query che ha prodotto la cache.  Inoltre, il risultato memorizzato nella cache deve essere applicato all'intera query.  

Le viste materializzate consentono la modifica dei dati nelle tabelle di base.  I dati nelle viste materializzate possono essere applicati a una parte di una query.  Questo supporto consente alle stesse viste materializzate di essere utilizzate da query diverse che condividono alcuni calcoli per prestazioni più veloci.

## <a name="example"></a>Esempio

In questo esempio viene utilizzata una query simile a TPCDS che consente di identificare i clienti che spendono più denaro tramite catalogo rispetto ai punti vendita, identificare i clienti preferiti e il paese di origine.   La query prevede la selezione di TOP 100 record dall'UNIONE di tre istruzioni sub-SELECT che coinvolgono SUM() e GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Controllare il piano di esecuzione stimato della query.  Ci sono 18 riproduzioni casuali e 17 operazioni di join, che richiedono più tempo per l'esecuzione. Ora creiamo una vista materializzata per ognuna delle tre istruzioni sub-SELECT.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Controllare nuovamente il piano di esecuzione della query originale.  Ora il numero di join cambia da 17 a 5 e non c'è più nessun shuffle.  Fare clic sull'icona dell'operazione Filtro nel piano, l'elenco di output mostra che i dati vengono letti dalle viste materializzate anziché dalle tabelle di base.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

Con le viste materializzate, la stessa query viene eseguita molto più velocemente senza alcuna modifica del codice.  

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere Cenni preliminari sullo sviluppo di [pool SQL Synapse](sql-data-warehouse-overview-develop.md).
