---
title: Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse | Microsoft Docs
description: Informazioni sulla gestione della concorrenza e del carico di lavoro nel Data Warehouse di SQL Azure per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse
Per fornire prestazioni stimabili e scalabili, Microsoft Azure SQL Data Warehouse consente di controllare i livelli di concorrenza e le allocazioni di risorse, come la memoria e la classificazione in ordine di priorità della CPU. Questo articolo introduce i concetti di gestione della concorrenza e del carico di lavoro, spiegando in che modo entrambe le funzionalità sono state implementate e come vengono controllate nel data warehouse. La gestione del carico di lavoro di SQL Data Warehouse è concepita per facilitare il supporto di ambienti multiutente. Non è concepita per i carichi di lavoro multi-tenant.

## <a name="concurrency-limits"></a>Limiti di concorrenza
SQL Data Warehouse consente un massimo di 1.024 connessioni simultanee. Tutte le 1.024 connessioni possono inviare query contemporaneamente. Tuttavia, per ottimizzare la velocità effettiva, SQL Data Warehouse può accodare alcune query per garantire che a ognuna venga assegnata una concessione di memoria minima. L'accodamento avviene in fase di esecuzione della query. Accodando le query quando vengono raggiunti i limiti di concorrenza, SQL Data Warehouse è in grado di aumentare la velocità effettiva totale, assicurando che le query attive ottengano l'accesso alle risorse di memoria critiche necessarie.  

I limiti di concorrenza sono regolati da due concetti, *query simultanee* e *slot di concorrenza*. Perché una query venga eseguita, è necessario che rientri nel limite di concorrenza delle query e nell'allocazione di slot di concorrenza.

* Le query simultanee sono le query in esecuzione contemporaneamente. SQL Data Warehouse supporta fino a 32 query simultanee nelle DWU di dimensioni maggiori.
* In base alle Unità Data Warehouse (DWU), vengono allocati slot di concorrenza. Ogni 100 DWU, vengono forniti 4 slot di concorrenza. Ad esempio, per DW100 vengono allocati 4 slot di concorrenza e per DW1000 ne vengono allocati 40. Ogni query utilizza uno o più slot di concorrenza, a seconda della [classe di risorse](#resource-classes) della query. Le query in esecuzione nella classe di risorse smallrc utilizzano uno slot di concorrenza. Le query in esecuzione in una classe di risorse superiore usano più slot di concorrenza.

La tabella seguente descrive i limiti sia per le query simultanee che per gli slot di concorrenza a seconda delle dimensioni della DWU.

### <a name="concurrency-limits"></a>Limiti di concorrenza
| DWU | Numero massimo di query simultanee | Numero di slot di concorrenza allocati |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

Quando viene raggiunta una di queste soglie, le nuove query vengono accodate e vengono eseguite in base al principio FIFO (First-In-First-Out).  Al termine dell'esecuzione delle query e quando il numero di query e di slot risulta inferiore ai limiti, le code accodate vengono rilasciate. 

> [!NOTE]  
> *selezione* eseguite esclusivamente sulle viste del catalogo o sulle viste a gestione dinamica (DMV) non sono disciplinate da nessuno dei limiti di concorrenza. È possibile monitorare il sistema indipendentemente dal numero di query in esecuzione nel sistema.
> 
> 

## <a name="resource-classes"></a>Classi di risorse
Attraverso le classi di risorse è possibile controllare l'allocazione di memoria e cicli di CPU assegnati a una query. È possibile assegnare due tipi di classi di risorse a un utente sotto forma di ruoli del database. I due tipi di classi di risorse sono i seguenti:
1. Classi di risorse dinamiche (**smallrc, mediumrc, largerc, xlargerc**), che allocano una quantità variabile di memoria a seconda della DWU corrente. Ciò significa che quando si passa a una DWU più grande, le query ottengono automaticamente più memoria. 
2. Classi di risorse statiche (**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**), che allocano la stessa quantità di memoria indipendentemente dalla DWU corrente (a condizione che la DWU stessa abbia memoria sufficiente). Ciò significa che nelle DWU più grandi è possibile eseguire più query in ogni classe di risorse contemporaneamente.

Agli utenti delle classi **smallrc** e **staticrc10** viene assegnata una quantità di memoria più piccola e ciò permette di sfruttare una concorrenza maggiore. Al contrario, agli utenti delle classi **xlargerc** e **staticrc80** vengono assegnate grandi quantità di memoria e quindi è possibile eseguire contemporaneamente un numero minore di query.

Per impostazione predefinita, ogni utente è membro della classe di risorse piccola **smallrc**. Per aumentare la classe di risorse viene usata la procedura `sp_addrolemember`, mentre per diminuirla viene usata la procedura `sp_droprolemember`. Questo comando, ad esempio, assegna a loaduser una classe di risorse superiore, **largerc**:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Query che non rispettano le classi di risorse

Esistono alcuni tipi di query che non traggono vantaggio da un'allocazione di memoria maggiore. Il sistema ignora l'allocazione della classe di risorse ed esegue sempre queste query nella classe di risorse piccola. Se queste query vengono eseguite sempre nella classe di risorse piccola, è possibile eseguirle quando gli slot di concorrenza sono sotto pressione, per non consumare più slot del necessario. Vedere [Eccezioni della classe di risorse](#query-exceptions-to-concurrency-limits) per ulteriori informazioni.

## <a name="details-on-resource-class-assignment"></a>Dettagli sull'assegnazione della classe di risorse


Altri dettagli sulla classe di risorse:

* *Ruolo Alter* è obbligatoria per modificare la classe di risorse di un utente.
* Anche se è possibile aggiungere un utente a una o più classi di risorse superiori, le classi di risorse dinamiche hanno la precedenza su quelle statiche. Ciò significa che se un utente viene assegnato a entrambe le classi **mediumrc**(dinamica) e **staticrc80**(statica), la precedenza va a **mediumrc**.
 * Quando un utente è assegnato a più di una classe di risorse in un tipo di classe di risorse specifico (più classi di risorse dinamiche o più classi di risorse statiche), la precedenza va alla classe di risorse superiore. Ciò significa che se un utente viene assegnato a entrambe le classi mediumrc e largerc, la precedenza va alla classe di risorse superiore (largerc). Se un utente viene assegnato a entrambe le classi **staticrc20** e **statirc80**, la precedenza va a **staticrc80**.
* La classe di risorse dell'utente amministratore di sistema non può essere modificata.

Per un esempio dettagliato, vedere [Esempio di modifica della classe di risorse di un utente](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Allocazione della memoria
Esistono vantaggi e svantaggi legati all'incremento della classe di risorse dell'utente. Spostando un utente a una classe di risorse superiore, si consente alle rispettive query di accedere a una quantità maggiore di memoria e quindi l'esecuzione delle query è più rapida.  Una quantità maggiore di classi di risorse, tuttavia, riduce anche il numero di query simultanee che è possibile eseguire. Questo è il compromesso tra il fatto di allocare grandi quantità di memoria a una singola query o consentire l'esecuzione simultanea di altre query che necessitano di allocazioni di memoria. Se a un utente vengono allocate grandi quantità di memoria per una query, gli altri utenti non potranno accedere alla stessa memoria per eseguire una query.

Nella tabella seguente la memoria allocata è associata a ogni distribuzione per classe DWU e classe di risorse.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Allocazioni di memoria per ogni distribuzione per le classi di risorse dinamiche (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1.600 |
| DW500 |100 |400 |800 |1.600 |
| DW600 |100 |400 |800 |1.600 |
| DW1000 |100 |800 |1.600 |3.200 |
| DW1200 |100 |800 |1.600 |3.200 |
| DW1500 |100 |800 |1.600 |3.200 |
| DW2000 |100 |1.600 |3.200 |6.400 |
| DW3000 |100 |1.600 |3.200 |6.400 |
| DW6000 |100 |3.200 |6.400 |12.800 |

La tabella seguente indica la memoria allocata a ogni distribuzione in base alla DWU e alla classe di risorse statica. Si noti che le classi di risorse superiori hanno memoria ridotta per rispettare i limiti delle DWU globali.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Allocazioni di memoria per ogni distribuzione per le classi di risorse statiche (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1.600 |1.600 |1.600 |1.600 |
| DW500 |100 |200 |400 |800 |1.600 |1.600 |1.600 |1.600 |
| DW600 |100 |200 |400 |800 |1.600 |1.600 |1.600 |1.600 |
| DW1000 |100 |200 |400 |800 |1.600 |3.200 |3.200 |3.200 |
| DW1200 |100 |200 |400 |800 |1.600 |3.200 |3.200 |3.200 |
| DW1500 |100 |200 |400 |800 |1.600 |3.200 |3.200 |3.200 |
| DW2000 |100 |200 |400 |800 |1.600 |3.200 |6.400 |6.400 |
| DW3000 |100 |200 |400 |800 |1.600 |3.200 |6.400 |6.400 |
| DW6000 |100 |200 |400 |800 |1.600 |3.200 |6.400 |12.800 |

Come si può notare nella tabella precedente, una query in esecuzione in DW2000 nella classe di risorse **xlargerc** avrà accesso a 6.400 MB di memoria in ognuno dei 60 database distribuiti.  In SQL Data Warehouse sono presenti 60 distribuzioni. Per calcolare quindi l'allocazione totale di memoria per una query in una classe di risorse specifica, è necessario moltiplicare per 60 i valori precedenti.

### <a name="memory-allocations-system-wide-gb"></a>Allocazioni di memoria a livello di sistema (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Come si può notare in questa tabella di allocazioni di memoria a livello di sistema, a una query in esecuzione in DW2000 nella classe di risorse xlargerc viene allocato un totale di 375 GB di memoria (6.400 MB * 60 distribuzioni/1.024 per la conversione in GB) nell'intero SQL Data Warehouse.

Lo stesso calcolo si applica alle classi di risorse statiche.
 
## <a name="concurrency-slot-consumption"></a>Utilizzo di slot di concorrenza  
SQL Data Warehouse concede più memoria alle query in esecuzione nelle classi di risorse più grandi. La memoria è una risorsa fissa.  Maggiore sarà la quantità di memoria allocata per ogni query, quindi, minore sarà il numero di richieste simultanee che è possibile eseguire. La tabella seguente riprende tutti i concetti descritti finora in un'unica rappresentazione che mostra il numero di slot di concorrenza disponibili per DWU e gli slot usati da ogni classe di risorse.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Allocazione e consumo degli slot di concorrenza per le classi di risorse dinamiche  
| DWU | Numero massimo di query simultanee | Numero di slot di concorrenza allocati | Slot utilizzati da smallrc | Slot utilizzati da mediumrc | Slot utilizzati da largerc | Slot utilizzati da xlargerc |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Allocazione e consumo degli slot di concorrenza per le classi di risorse statiche  
| DWU | Numero massimo di query simultanee | Numero di slot di concorrenza allocati |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

Come si può notare da queste tabelle, l'esecuzione di SQL Data Warehouse come DW1000 alloca un massimo di 32 query simultanee e un totale di 40 slot di concorrenza. Se l'esecuzione avviene da parte di tutti gli utenti nella classe smallrc, vengono consentite 32 query simultanee, poiché ognuna richiede 1 slot di concorrenza. Se l'esecuzione avviene da parte di tutti gli utenti di un DW1000 in una classe mediumrc, per ogni query vengono allocati 800 MB a distribuzione, per un'allocazione di memoria totale pari a 47 GB per query, mentre il numero degli utenti simultanei viene limitato a 5 (40 slot di concorrenza, 8 per ogni utente mediumrc).

## <a name="selecting-proper-resource-class"></a>Scelta della classe di risorse appropriata  
È buona norma assegnare in modo permanente gli utenti a una classe di risorse invece di modificare la classe di risorse degli utenti. I caricamenti in tabelle columnstore cluster, ad esempio, creano indici di qualità superiore quando viene allocata una quantità di memoria maggiore. Per assicurarsi che caricamenti abbiano accesso alla memoria superiore, creare un utente specifico per il caricamento dei dati e assegnare permanentemente a questo utente una classe di risorse superiore.
Ci sono alcune procedure consigliate da seguire. Come indicato in precedenza, SQL Data Warehouse supporta due tipi di classi di risorse: le classi di risorse statiche e quelle dinamiche.
### <a name="loading-best-practices"></a>Procedure consigliate per il caricamento
1.  Se si prevedono caricamenti con quantità di dati regolari, una classe di risorse statica è una scelta appropriata. In un secondo momento, quando si aumenteranno le prestazioni per ottenere maggiore potenza di calcolo, il data warehouse potrà eseguire più query simultanee per impostazione predefinita, in quanto l'utente che esegue il caricamento non utilizza più memoria.
2.  Se si prevedono caricamenti di entità più grande in alcune occasioni, una classe di risorse dinamica è una scelta appropriata. In un secondo momento, quando si aumenteranno le prestazioni per ottenere maggiore potenza di calcolo, l'utente che esegue il caricamento riceverà più memoria per impostazione predefinita, pertanto i tempi di caricamento saranno più rapidi.

La memoria necessaria per elaborare in modo efficiente i caricamenti dipende dalla natura della tabella caricata e dalla quantità di dati elaborati. Ad esempio, il caricamento di dati nelle tabelle CCI richiede una determinata quantità di memoria per consentire l'ottimizzazione per i rowgroup CCI. Per altre informazioni, vedere le indicazioni relative a indici columnstore e caricamento di dati.

Come procedura consigliata, si consiglia di usare almeno 200 MB di memoria per i caricamenti.

### <a name="querying-best-practices"></a>Procedure consigliate per le query
Le query hanno diversi requisiti a seconda della complessità. L'aumento della memoria per ogni query o l'aumento della concorrenza sono entrambi metodi validi per aumentare la velocità effettiva globale a seconda delle esigenze di query.
1.  Se si prevedono query regolari complesse (ad esempio per generare report giornalieri e settimanali) e non è necessario sfruttare la concorrenza, una classe di risorse dinamica è una scelta appropriata. Se il sistema ha più dati da elaborare, un aumento delle prestazioni del data warehouse fornisce automaticamente più memoria per l'utente che esegue la query.
2.  Se si prevedono modelli di concorrenza variabili o giornalieri (ad esempio se vengono eseguite query sul database tramite un'interfaccia utente Web accessibile su vasta scala), una classe di risorse statica è una scelta appropriata. In un secondo momento, quando si aumenteranno le prestazioni del data warehouse, l'utente associato alla classe di risorse statica potrà eseguire automaticamente un numero maggiore di query simultanee.

La selezione di una concessione di memoria appropriata a seconda delle esigenze della query non è semplice, perché dipende da molti fattori, come la quantità di dati sottoposti a query, la natura degli schemi di tabella e i vari predicati di gruppo, selezione e join. Dal punto di vista generale, l'allocazione di più memoria consente tempi più rapidi per il completamento delle query, ma riduce la concorrenza complessiva. Se la concorrenza non è un problema, un'allocazione eccessiva di memoria non causa alcun danno. Per ottimizzare la velocità effettiva, potrebbe essere necessario provare diversi tipi di classi di risorse.

È possibile usare la stored procedure seguente per ottenere informazioni sulla concorrenza e sulla concessione di memoria per ogni classe di risorse in un determinato SLO e sulla classe di risorse migliore possibile per operazioni CCI a elevato utilizzo di memoria su una tabella CCI non partizionata con una classe di risorse specifica:

#### <a name="description"></a>Descrizione:  
Ecco lo scopo della stored procedure:  
1. Aiutare l'utente a ottenere informazioni sulla concorrenza e sulla concessione di memoria per ogni classe di risorse in un determinato SLO. L'utente deve specificare NULL sia per lo schema che per il nome di tabella, come illustrato nell'esempio seguente.  
2. Aiutare l'utente a ottenere informazioni sulla classe di risorse migliore possibile per operazioni CCI a elevato utilizzo di memoria (caricamento, copia di tabelle, ricompilazione dell'indice e così via) su una tabella CCI non partizionata con una classe di risorse specifica. La stored procedure usa lo schema di tabella per individuare la concessione di memoria necessaria.

#### <a name="dependencies--restrictions"></a>Dipendenze e restrizioni:
- Questa stored procedure non è progettata per calcolare i requisiti di memoria per una tabella CCI partizionata.    
- Questa stored procedure non prende in considerazione i requisiti di memoria per la parte SELECT di un'istruzione CTAS/INSERT-SELECT e presuppone che si tratti di un'istruzione SELECT semplice.
- Questa stored procedure usa una tabella temporanea che quindi può essere usata nella sessione in cui è stata creata la stored procedure.    
- Questa stored procedure dipende dalle risorse correnti (ad esempio, configurazione hardware e configurazione DMS) e in caso di modifiche non funziona più correttamente.  
- Questa stored procedure dipende dal limite di concorrenza esistente e in caso di modifiche non funziona più correttamente.  
- Questa stored procedure dipende dalle classi di risorse esistenti e in caso di modifiche non funziona più correttamente.  

>  [!NOTE]  
>  Se non si ottiene alcun output dopo l'esecuzione della stored procedure con i parametri forniti, i motivi potrebbero essere due. <br />1. Uno dei parametri di Data Warehouse contiene un valore SLO non valido <br />2. OPPURE non ci sono classi di risorse corrispondenti per l'operazione CCI se è stato fornito il nome della tabella. <br />Ad esempio, con DW100, la concessione di memoria massima disponibile è 400 MB e lo schema di tabella è sufficientemente ampio per soddisfare il requisito di 400 MB.
      
#### <a name="usage-example"></a>Esempio d'uso:
Sintassi:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: Fornire un parametro NULL per estrarre la DWU corrente dal database di Data Warehouse oppure fornire una DWU supportata nel formato "DW100"
2. @SCHEMA_NAME: Fornire un nome di schema della tabella
3. @TABLE_NAME: Fornire un nome di tabella

Esempi di esecuzione di questa stored procedure:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

La tabella Table1 usata negli esempi precedenti può venire creata come indicato di seguito:  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Ecco la definizione della stored procedure:
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>Priorità delle query
SQL Data Warehouse implementa le classi di risorse utilizzando i gruppi del carico di lavoro. Esistono in totale otto gruppi di carichi di lavoro che controllano il comportamento delle classi di risorse nelle DWU di varie dimensioni. Per qualsiasi DWU, SQL Data Warehouse usa solo quattro degli otto gruppi del carico di lavoro. Il senso di questo approccio è assegnare a ogni gruppo di carichi di lavoro una delle quattro classi di risorse, tra smallrc, mediumrc, largerc o xlargerc. È importante comprendere per alcuni gruppi di carichi di lavoro viene impostato il livello di *priorità*più elevato. La priorità viene usata per la pianificazione della CPU. Le query eseguite con priorità alta otterranno tre volte più cicli della CPU rispetto a quelle con priorità media. Di conseguenza, i mapping degli slot della concorrenza determinano anche la priorità della CPU. Quando una query utilizza 16 o più slot, viene eseguita con priorità alta.

Nella tabella seguente vengono riportati i mapping di priorità per ogni gruppo di carichi di lavoro.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapping dei gruppi di carichi di lavoro agli slot di concorrenza e relativa importanza
| Gruppi di carichi di lavoro | Mapping degli slot di concorrenza | MB/Distribuzione | Mapping delle priorità |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Media |
| SloDWGroupC01 |2 |200 |Media |
| SloDWGroupC02 |4 |400 |Media |
| SloDWGroupC03 |8 |800 |Media |
| SloDWGroupC04 |16 |1.600 |Alto |
| SloDWGroupC05 |32 |3.200 |Alto |
| SloDWGroupC06 |64 |6.400 |Alto |
| SloDWGroupC07 |128 |12.800 |Alto |

Come illustrato nel grafico **Allocation and consumption of concurrency slots** (Allocazione e utilizzo degli slot di concorrenza), DW500 utilizza 1, 4, 8 o 16 slot di concorrenza per smallrc, mediumrc, largerc e xlargerc rispettivamente. È possibile cercare questi valori nel grafico precedente per identificare la priorità di ciascuna classe di risorse.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapping dell'importanza delle classi di risorse in DW500
| classe di risorse | Gruppo del carico di lavoro | Numero di slot di concorrenza usati | MB/Distribuzione | priorità |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Media |
| mediumrc |SloDWGroupC02 |4 |400 |Media |
| largerc |SloDWGroupC03 |8 |800 |Media |
| xlargerc |SloDWGroupC04 |16 |1.600 |Alto |
| staticrc10 |SloDWGroupC00 |1 |100 |Media |
| staticrc20 |SloDWGroupC01 |2 |200 |Media |
| staticrc30 |SloDWGroupC02 |4 |400 |Media |
| staticrc40 |SloDWGroupC03 |8 |800 |Media |
| staticrc50 |SloDWGroupC03 |16 |1.600 |Alto |
| staticrc60 |SloDWGroupC03 |16 |1.600 |Alto |
| staticrc70 |SloDWGroupC03 |16 |1.600 |Alto |
| staticrc80 |SloDWGroupC03 |16 |1.600 |Alto |

Per esaminare in dettaglio le differenze nell'allocazione delle risorse di memoria dal punto di vista di resource governor o per analizzare l'utilizzo attivo e cronologico dei gruppi di carichi di lavoro in caso di risoluzione dei problemi, è possibile usare la query DMV seguente:

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Query che rispettano i limiti di concorrenza
La maggior parte delle query è regolata da classi di risorse. Queste query devono rientrare in entrambe le soglie delle query simultanee e degli slot di concorrenza. Un utente può scegliere di escludere una query dal modello di slot di concorrenza.

Le istruzioni seguenti rispettano le classi di risorse:

* INSERT SELECT
* AGGIORNAMENTO
* DELETE
* SELEZIONARE (quando si esegue una query sulle tabelle utente)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* MODIFICA TABELLA RICOMPILAZIONE
* CREATE INDEX
* CREARE L'INDICE COLUMNSTORE CLUSTER
* CREATE TABLE AS SELECT (CTAS)
* Caricamento dei dati
* Operazioni di spostamento dati condotte dal Servizio di spostamento dati (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Eccezioni query ai limiti di concorrenza
Alcune query non rispettano la classe di risorse alla quale è assegnato l'utente. Queste eccezioni ai limiti di concorrenza vengono effettuate quando le risorse di memoria necessarie per un particolare comando sono basse, spesso perché il comando è un'operazione dei metadati. Con queste eccezioni, si intende evitare l'allocazione di più memoria alle query che non ne hanno bisogno. In questi casi, viene sempre usata la classe di risorse piccola predefinita (smallrc), indipendentemente dalla classe di risorse effettivamente assegnata all'utente. Ad esempio, in smallrc verrà sempre eseguita `CREATE LOGIN` . Le risorse necessarie per svolgere questa operazione sono molto ridotte e non avrebbe senso includere la query nel modello di slot di concorrenza.  Per queste query non è previsto il limite di 32 utenti simultanei; è possibile eseguire un numero illimitato di queste query fino al limite di 1.024 sessioni.

Le istruzioni seguenti non rispettano le classi di risorse:

* CREATE o DROP TABLE
* ALTER TABLE... SWITCH, SPLIT o MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE o DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER o DROP USER
* CREATE, ALTER o DROP PROCEDURE
* CREATE o DROP VIEW
* INSERT VALUES
* SELECT da viste di sistema e DMV
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a> Esempio di modifica della classe di risorse di un utente
1. **Creare un account di accesso:** aprire una connessione al database **master** nel server SQL che ospita il database SQL Data Warehouse ed eseguire i comandi seguenti.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > È consigliabile creare un utente nel database master per gli utenti di SQL Data Warehouse di Azure. La creazione di un utente nel database master consente all'utente di accedere tramite strumenti come SSMS senza specificare un nome di database.  Consente inoltre di usare Esplora oggetti per visualizzare tutti i database in SQL server.  Per altre informazioni su creazione e gestione degli utenti, vedere [Proteggere un database in SQL Data Warehouse][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Creare un utente di SQL Data Warehouse:** aprire una connessione al database di **SQL Data Warehouse** ed eseguire il comando seguente.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Concedere le autorizzazioni:** nell'esempio seguente viene concessa l'autorizzazione `CONTROL` nel database **SQL Data Warehouse**. `CONTROL` a livello di database corrisponde a db_owner in SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Aumentare la classe di risorse:** per aggiungere un utente a un ruolo di gestione del carico di lavoro più elevato, usare la query seguente.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Diminuire la classe di risorse:** per rimuovere un utente da un ruolo di gestione del carico di lavoro, usare la query seguente.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Non è possibile rimuovere un utente da smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Rilevamento di query in coda e altre viste a gestione dinamica
È possibile utilizzare la DMV `sys.dm_pdw_exec_requests` per identificare le query in attesa in una coda di concorrenza. Le query in attesa di uno slot di concorrenza saranno in stato **sospeso**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

I ruoli di gestione del carico di lavoro possono essere visualizzati con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La query seguente mostra il ruolo a cui è assegnato ogni utente.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse prevede i tipi di attesa seguenti.

* **LocalQueriesConcurrencyResourceType**: query che si trovano all'esterno del framework di slot di concorrenza. Le query DMV e le funzioni di sistema  come `SELECT @@VERSION` sono esempi di query locali.
* **UserConcurrencyResourceType**: query che si trovano all'interno del framework di slot di concorrenza. Le query sulle tabelle dell'utente finale rappresentano esempi in cui si usa questo tipo di risorsa.
* **DmsConcurrencyResourceType**: attese risultanti dalle operazioni di spostamento dei dati.
* **BackupConcurrencyResourceType**: indica che è in esecuzione il backup di un database. Il valore massimo per questo tipo di risorsa è 1. Se più copie di backup sono stati richieste contemporaneamente, le altre verranno accodate.

La DMV `sys.dm_pdw_waits` può essere usata per visualizzare le risorse per cui una richiesta è in attesa.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

La DMV `sys.dm_pdw_resource_waits` mostra solo le attese di risorse utilizzate da una determinata query. Il tempo in attesa delle risorse misura solo il tempo richiesto per fornire le risorse, a differenza del tempo di attesa del segnale che rappresenta il tempo impiegato dal server SQL Server sottostante per pianificare la query sulla CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

L DMV `sys.dm_pdw_wait_stats` può essere usata per l'analisi della tendenza cronologica delle attese.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla gestione degli utenti e della sicurezza del database, vedere [Proteggere un database in SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Per ulteriori informazioni sulle classi di risorse più grandi che possono migliorare le qualità degli indici indice columnstore cluster, vedere [Ricompilazione degli indici per migliorare la qualità del segmento].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Ricompilazione degli indici per migliorare la qualità del segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
