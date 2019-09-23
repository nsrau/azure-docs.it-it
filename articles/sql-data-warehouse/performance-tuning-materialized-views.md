---
title: Ottimizzazione delle prestazioni con Azure SQL Data Warehouse viste materializzate | Microsoft Docs
description: Raccomandazioni e considerazioni che è necessario tenere presente quando si usano viste materializzate per migliorare le prestazioni delle query.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 6ed6e21f16287148c8764dd98bda378451440e58
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172779"
---
# <a name="performance-tuning-with-materialized-views"></a>Ottimizzazione delle prestazioni con viste materializzate 
Le viste materializzate in Azure SQL Data Warehouse forniscono un metodo di manutenzione basso per le query analitiche complesse per ottenere prestazioni rapide senza alcuna modifica di query. Questo articolo illustra le linee guida generali sull'uso delle viste materializzate.


## <a name="materialized-views-vs-standard-views"></a>Viste materializzate rispetto a viste standard
Azure SQL Data Warehouse supporta le viste standard e materializzate.  Entrambe sono tabelle virtuali create con espressioni SELECT e presentate alle query come tabelle logiche.  Le visualizzazioni incapsulano la complessità del calcolo comune dei dati e aggiungono un livello di astrazione alle modifiche di calcolo, pertanto non è necessario riscrivere le query.  

Una vista standard calcola i dati ogni volta che viene utilizzata la vista.  Non sono presenti dati archiviati su disco. Le persone utilizzano in genere viste standard come uno strumento che consente di organizzare gli oggetti logici e le query in un database.  Per utilizzare una vista standard, è necessario eseguire un riferimento diretto a una query. 

Una vista materializzata consente di pre-calcolare, archiviare e mantenere i dati in Azure SQL Data Warehouse esattamente come una tabella.  Non è necessario ricalcolare ogni volta quando viene usata una vista materializzata.  Per questo motivo, le query che utilizzano tutti i dati o subset dei dati nelle viste materializzate possono ottenere prestazioni più veloci.  Ancora meglio, le query possono usare una vista materializzata senza farvi riferimento diretto, pertanto non è necessario modificare il codice dell'applicazione.  

La maggior parte dei requisiti di una vista standard è ancora applicabile a una vista materializzata. Per informazioni dettagliate sulla sintassi di visualizzazione materializzata e su altri requisiti, vedere [creare una vista materializzata come SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).



| Confronto                     | visualizzazione                                         | Vista materializzata             
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------| 
|Visualizzare la definizione                 | Archiviato in data warehouse di Azure.              | Archiviato in data warehouse di Azure.    
|Visualizza contenuto                    | Generato ogni volta che viene utilizzata la visualizzazione.   | Pre-elaborati e archiviati in Azure data warehouse durante la creazione della visualizzazione. Aggiornato quando i dati vengono aggiunti alle tabelle sottostanti.                                             
|Aggiornamento dati                    | Sempre aggiornato                               | Sempre aggiornato                          
|Velocità di recupero dei dati di visualizzazione da query complesse     | Lento                                         | Veloce  
|Risorse di archiviazione extra                   | No                                           | Yes                             
|Sintassi                          | CREA VISTA                                  | CREA VISTA MATERIALIZZATA COME SELEZIONE           
     
## <a name="benefits-of-using-materialized-views"></a>Vantaggi dell'utilizzo di viste materializzate

Una vista materializzata progettata correttamente può offrire i vantaggi seguenti:

- Ridurre il tempo di esecuzione per le query complesse con JOIN e funzioni di aggregazione. Maggiore è la complessità della query, più elevata è la possibilità di risparmiare tempo di esecuzione. La maggior parte dei vantaggi si ottiene quando il costo di calcolo di una query è elevato e il set di dati risultante è ridotto.  

- Query Optimizer in Azure SQL Data Warehouse può utilizzare automaticamente viste materializzate distribuite per migliorare i piani di esecuzione delle query.  Questo processo è trasparente per gli utenti che forniscono prestazioni di query più veloci e non richiedono query per fare riferimento diretto alle viste materializzate. 

- Richiedere una manutenzione ridotta delle visualizzazioni.  Una vista materializzata archivia i dati in due posizioni, un indice columnstore cluster per i dati iniziali in fase di creazione della vista e un archivio Delta per le modifiche incrementali dei dati.  Tutte le modifiche apportate ai dati dalle tabelle di base vengono aggiunte automaticamente all'archivio Delta in modo sincrono.  Un processo in background (motore di Tuple) sposta periodicamente i dati dall'archivio Delta all'indice columnstore della visualizzazione.  Questa progettazione consente di eseguire query sulle viste materializzate per restituire gli stessi dati come query direttamente sulle tabelle di base. 
- I dati in una vista materializzata possono essere distribuiti in modo diverso rispetto alle tabelle di base.  
- I dati nelle viste materializzate ottengono gli stessi vantaggi di disponibilità elevata e resilienza dei dati nelle tabelle normali.  
 
Rispetto ad altri provider di data warehouse, le viste materializzate implementate in Azure SQL Data Warehouse offrono anche i seguenti vantaggi aggiuntivi: 

- Aggiornamento dati automatico e sincrono con modifiche ai dati nelle tabelle di base. Non è necessaria alcuna azione da parte dell’utente. 
- Supporto di funzioni di aggregazione generali. Vedere [creare una vista materializzata come Select (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).
- Supporto per la raccomandazione di visualizzazione materializzata specifica della query.  Vedere [Explain (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Scenari comuni  

Le viste materializzate vengono in genere utilizzate negli scenari seguenti: 

**Necessità di migliorare le prestazioni di query analitiche complesse su dati di grandi dimensioni**

Le query analitiche complesse utilizzano in genere più funzioni di aggregazione e join di tabella, causando operazioni di calcolo più complesse, ad esempio shuffle e join nell'esecuzione della query.  Per questo motivo, le query hanno più tempo per essere completate, specialmente in tabelle di grandi dimensioni.  Gli utenti possono creare viste materializzate per i dati restituiti dai calcoli comuni delle query, pertanto non è necessario eseguire il ricalcolo quando questi dati sono necessari per le query, consentendo un costo di calcolo inferiore e una risposta più rapida alle query. 

**Prestazioni più rapide senza modifiche minime delle query**

Le modifiche dello schema e delle query nei data warehouse vengono in genere conservate come minimo per supportare le normali operazioni ETL e la creazione di report.  Gli utenti possono utilizzare viste materializzate per ottimizzare le prestazioni delle query, se il costo sostenuto dalle visualizzazioni può essere sfalsato in base al miglioramento delle prestazioni di esecuzione delle query. Rispetto ad altre opzioni di ottimizzazione, ad esempio il ridimensionamento e la gestione delle statistiche, si tratta di una modifica di produzione molto meno incisiva per creare e mantenere una vista materializzata e il potenziale miglioramento delle prestazioni è anche superiore.

- La creazione o la gestione di viste materializzate non influisca sulle query in esecuzione sulle tabelle di base.
- Il Query Optimizer può utilizzare automaticamente le viste materializzate distribuite senza riferimento diretto alla vista in una query. Questa funzionalità riduce la necessità di modificare le query nell'ottimizzazione delle prestazioni. 

**Necessità di una strategia di distribuzione dei dati diversa per velocizzare le prestazioni delle query**

Azure data warehouse è un sistema di elaborazione parallela massiva distribuita (MPP).   I dati in una tabella data warehouse vengono distribuiti tra 60 nodi usando una delle tre [strategie di distribuzione](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute) (hash, round_robin o replicate).  La distribuzione dei dati viene specificata al momento della creazione della tabella e rimane invariata fino a quando la tabella non viene eliminata. La vista materializzata è una tabella virtuale su disco che supporta le distribuzioni di dati hash e round_robin.  Gli utenti possono scegliere una distribuzione di dati diversa dalle tabelle di base ma ottimali per le prestazioni delle query che utilizzano la maggior parte delle visualizzazioni.  

## <a name="design-guidance"></a>Indicazioni per la progettazione 

Ecco le linee guida generali sull'uso delle viste materializzate per migliorare le prestazioni delle query:

**Progettazione per il carico di lavoro**

Prima di iniziare a creare viste materializzate, è importante avere una conoscenza approfondita del carico di lavoro in termini di modelli di query, importanza, frequenza e dimensioni dei dati risultanti.  

Gli utenti possono eseguire EXPLAIN WITH_RECOMMENDATIONS < SQL_statement > per le visualizzazioni materializzate consigliate dal Query Optimizer.  Poiché queste raccomandazioni sono specifiche della query, una vista materializzata che avvantaggia una singola query potrebbe non essere ottimale per le altre query nello stesso carico di lavoro.  Valutare queste raccomandazioni con le esigenze del carico di lavoro.  Le visualizzazioni materializzate ideali sono quelle che sfruttano le prestazioni del carico di lavoro.  

**Tenere presente il compromesso tra query più veloci e i costi** 

Per ogni vista materializzata, è previsto un costo di archiviazione dei dati e un costo per la gestione della visualizzazione.  Man mano che vengono apportate modifiche ai dati nelle tabelle di base, le dimensioni della vista materializzata aumentano e viene modificata anche la struttura fisica.  Per evitare il calo delle prestazioni delle query, ogni vista materializzata viene gestita separatamente dal motore di data warehouse, tra cui lo spostamento delle righe dall'archivio Delta ai segmenti di indice columnstore e il consolidamento delle modifiche dei dati.  Il carico di lavoro di manutenzione diventa superiore quando aumenta il numero di viste materializzate e le modifiche alla tabella di base.   Gli utenti devono verificare se il costo sostenuto da tutte le visualizzazioni materializzate può essere offset in base al miglioramento delle prestazioni delle query.  

È possibile eseguire questa query per l'elenco di viste materializzate in un database: 

```sql
SELECT V.name as materialized_view, V.object_id 
FROM sys.views V 
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
``` 

Opzioni per ridurre il numero di viste materializzate: 

- Identificare i set di dati comuni usati spesso dalle query complesse nel carico di lavoro.  Creare viste materializzate per archiviare i set di dati in modo che l'utilità di ottimizzazione possa utilizzarli come blocchi predefiniti durante la creazione dei piani di esecuzione.  

- Eliminare le viste materializzate che hanno un utilizzo ridotto o che non sono più necessarie.  Una vista materializzata disabilitata non viene mantenuta, ma comporta comunque un costo di archiviazione.  

- Combinare viste materializzate create sulla stessa tabella di base o analoghe anche se i dati non si sovrappongono.  La pettinatura di viste materializzate può comportare una visualizzazione di dimensioni maggiori rispetto alla somma delle visualizzazioni separate, tuttavia il costo di manutenzione della vista dovrebbe ridursi.  Esempio:

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

Il data warehouse Optimizer può utilizzare automaticamente viste materializzate distribuite per migliorare le prestazioni di esecuzione delle query.  Questo supporto viene applicato in modo trasparente alle query che non fanno riferimento a viste e query che utilizzano aggregazioni non supportate nella creazione di viste materializzate.  Non è necessaria alcuna modifica alla query. È possibile controllare il piano di esecuzione stimato di una query per verificare se viene utilizzata una vista materializzata.  

**Monitorare le viste materializzate** 

Una vista materializzata viene archiviata nel data warehouse proprio come una tabella con indice columnstore cluster (CCI).  La lettura di dati da una vista materializzata include l'analisi dell'indice e l'applicazione delle modifiche dall'archivio Delta.  Quando il numero di righe nell'archivio Delta è troppo elevato, la risoluzione di una query da una vista materializzata può richiedere più tempo rispetto alla query diretta delle tabelle di base.  Per evitare un calo delle prestazioni delle query, è consigliabile eseguire [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest) per monitorare overhead_ratio (total_rows/base_view_row) della visualizzazione.  Se il overhead_ratio è troppo elevato, provare a ricompilare la vista materializzata in modo che tutte le righe nell'archivio Delta vengano spostate nell'indice columnstore.  

**Visualizzazione materializzata e memorizzazione nella cache del set di risultati**

Queste due funzionalità sono state introdotte in Azure SQL Data Warehouse intorno allo stesso tempo per l'ottimizzazione delle prestazioni delle query.  La memorizzazione nella cache del set di risultati viene utilizzata per ottenere una concorrenza elevata e una risposta rapida da query ripetitive sui dati statici.  Per usare il risultato memorizzato nella cache, il form della query di richiesta della cache deve corrispondere alla query che ha generato la cache.  Inoltre, il risultato memorizzato nella cache deve essere applicato all'intera query.  Le viste materializzate consentono di apportare modifiche ai dati nelle tabelle di base.  I dati nelle viste materializzate possono essere applicati a una parte di una query.  Questo supporto consente di usare le stesse viste materializzate da query diverse che condividono alcuni calcoli per ottenere prestazioni più veloci.

## <a name="example"></a>Esempio

In questo esempio viene usata una query di tipo TPCDS che trova i clienti che spendono più denaro tramite Catalog rispetto ai negozi, identificano i clienti preferiti e il paese di origine.   La query prevede la selezione dei primi 100 record dall'Unione di tre istruzioni Sub-SELECT che coinvolgono SUM () e GROUP BY. 

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

Controllare il piano di esecuzione stimato della query.  Sono presenti 18 shuffle e 17 operazioni join, che importano più tempo per l'esecuzione. A questo punto è possibile creare una vista materializzata per ognuna delle tre istruzioni Sub-SELECT.   

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
Controllare di nuovo il piano di esecuzione della query originale.  A questo punto, il numero di join cambia da 17 a 5 e non esiste più una sequenza casuale.  Fare clic sull'icona dell'operazione di filtro nel piano. l'elenco di output mostra che i dati vengono letti dalle viste materializzate invece che dalle tabelle di base.  

 ![Plan_Output_List_with_Materialized_Views](media/performance-tuning-materialized-views/output-list.png)

Con le viste materializzate, la stessa query viene eseguita molto più velocemente senza alcuna modifica del codice.  

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse](sql-data-warehouse-overview-develop.md).
