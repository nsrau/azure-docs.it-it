---
title: Raccomandazioni per le prestazioni - Database SQL di Azure | Microsoft Docs
description: Il database SQL di Azure offre raccomandazioni per i database SQL esistenti che consentono di migliorare le prestazioni correnti delle query.
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: 
ms.openlocfilehash: b3cd5f2138f4d16a1a1590b025d020410ebcce3c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="performance-recommendations-for-sql-database"></a>Raccomandazioni per le prestazioni per il database SQL

Il database SQL di Azure è in grado di apprendere e di adattarsi all'applicazione e fornisce raccomandazioni personalizzate che consentono di ottimizzare le prestazioni dei database SQL. Il database SQL valuta e analizza in modo continuo la cronologia di uso dei database SQL. Le raccomandazioni fornite si basano su modelli di carico di lavoro specifici del database e consentono di migliorarne le prestazioni.

> [!TIP]
> L'[ottimizzazione automatica](sql-database-automatic-tuning.md) è il metodo consigliato per ottimizzare le prestazioni, mentre [Intelligent Insights](sql-database-intelligent-insights.md) è il metodo consigliato per monitorarle. 
>

## <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici
Il database SQL monitora in modo continuo le query in esecuzione e identifica gli indici che potrebbero migliorare le prestazioni. Se la mancanza di un determinato indice è individuata con sufficiente attendibilità, viene creata una nuova raccomandazione **Crea indice**.

 Il database SQL di Azure valuta l'attendibilità stimando il miglioramento delle prestazioni che l'indice potrebbe offrire nel tempo. In base al miglioramento delle prestazioni stimato, le raccomandazioni vengono classificate come alta, media o bassa. 

Gli indici creati tramite raccomandazioni sono sempre contrassegnati come indici creati automaticamente. Per individuare gli indici creati automaticamente, esaminare la vista sys.indexes. Gli indici creati automaticamente non bloccano i comandi ALTER/RENAME. 

Se si tenta di eliminare la colonna associata a un indice creato automaticamente, il comando passa e l'indice creato automaticamente viene eliminato con il comando. Gli indici normali bloccano il comando ALTER/RENAME sulle colonne indicizzate.

Dopo l'applicazione delle raccomandazioni per l'indice, il database SQL di Azure confronta le prestazioni delle query con quelle di base. Se il nuovo indice migliora le prestazioni, la raccomandazione è contrassegnata come positiva e il report di impatto diventa disponibile. Se l'indice non migliora le prestazioni, viene automaticamente ripristinato. Il database SQL Usa questo processo per garantire che le raccomandazioni migliorino le prestazioni del database.

Per ogni raccomandazione **crea indice**, esistono criteri di sicurezza che non consentono di applicare la raccomandazione se l'uso delle risorse di un database o di un pool è elevato. I criteri di sicurezza tengono conto di CPU, I/O dati, I/O log e spazio di archiviazione disponibile. 

Se il valore di CPU, I/O dati oppure I/O log è più alto dell'80% nei 30 minuti precedenti, la raccomandazione di creazione indici è posticipata. Se lo spazio di archiviazione disponibile sarà inferiore del 10% dopo la creazione dell'indice, la raccomandazione entra in uno stato di errore. Se, dopo un paio di giorni, si ritiene che l'indice sia ancora utile per l'ottimizzazione automatica, il processo inizia nuovamente. 

Questo processo viene ripetuto fino a quando non è presente spazio di archiviazione sufficiente per creare un indice o fino a quando l'indice non è più considerato utile.

## <a name="drop-index-recommendations"></a>Raccomandazioni relative all'eliminazione di indici
Oltre a rilevare indici mancanti, il database SQL analizza continuamente le prestazioni degli indici esistenti. Se un indice non viene usato, il database SQL di Azure consiglia di eliminarlo. L'eliminazione di un indice è consigliabile in due casi:
* L'indice è un duplicato di un altro indice (ovvero con colonna, schema di partizione e filtri identici)
* L'indice non è stato usato per un periodo prolungato (93 giorni).

Anche le raccomandazioni relative all'eliminazione di indici sono sottoposte a verifica dopo l'implementazione. Se le prestazioni migliorano, il report di impatto diventa disponibile. Se le prestazioni diminuiscono, la raccomandazione viene ripristinata.


## <a name="parameterize-queries-recommendations"></a>Raccomandazioni relative alla creazione di query con parametri
Le raccomandazioni relative alla *creazione di query con parametri* vengono visualizzate quando sono presenti una o più query che vengono ricompilate costantemente ma finiscono con lo stesso piano di esecuzione di query. Questa determina la possibilità di applicare la parametrizzazione forzata che consente a sua volta di memorizzare nella cache i piani di query e di usarli nuovamente in futuro al fine di migliorare le prestazioni e ridurre l'uso delle risorse. 

Ogni query eseguita inizialmente su SQL Server deve essere compilata per generare un piano di esecuzione e ogni piano generato viene aggiunto alla cache dei piani. Nelle esecuzioni successive della stessa query è possibile usare nuovamente tale piano, condizione che elimina la necessità di un'altra compilazione. 

Le query con valori senza parametri possono provocare un overhead delle prestazioni perché il piano di esecuzione viene ricompilato ogni volta che i valori senza parametri sono diversi. In molti casi, le stesse query con valori di parametro diversi generano gli stessi piani di esecuzione che tuttavia vengono aggiunti ancora separatamente alla cache dei piani. 

Il processo di ricompilazione dei piani di esecuzione usa risorse del database, aumenta il tempo di durata delle query e provoca un overflow della cache dei piani. Questi eventi, a loro volta, provocano la rimozione dei piani dalla cache. Questo comportamento di SQL Server può essere modificato impostando l'opzione di parametrizzazione forzata nel database. 

Per stimare l'impatto della raccomandazione, viene offerto un confronto tra l'uso effettivo e quello previsto della CPU (ovvero nel caso di applicazione della raccomandazione). Tale raccomandazione consente di ottenere risparmi in termini di CPU e di ridurre la durata delle query e l'overhead per la cache dei piani, in modo che più piani possano rimanere nella cache ed essere usati nuovamente. È possibile applicare questa raccomandazione rapidamente selezionando il comando **Applica**. 

Dopo l'applicazione della raccomandazione, in pochi minuti nel database viene applicata la parametrizzazione forzata che avvia il processo di monitoraggio, che dura circa 24 ore. Dopo questo periodo, è possibile visualizzare il report di convalida. Il report mostra l'uso della CPU del database di 24 ore prima e dopo l'applicazione della raccomandazione. Advisor per database SQL include un meccanismo di sicurezza che ripristina automaticamente la raccomandazione applicata nel caso in cui venga rilevata una regressione delle prestazioni.

## <a name="fix-schema-issues-recommendations-preview"></a>Raccomandazioni relative alla correzione di problemi di schema (anteprima)

> [!IMPORTANT]
> Le raccomandazioni relative alla correzione dei problemi di schema sono attualmente in fase di deprecazione. È consigliabile usare [Intelligent Insights](sql-database-intelligent-insights.md) per monitorare i problemi di prestazioni del database, ad esempio i problemi di schema interessati in precedenza dalle raccomandazioni di correzione indicate.
> 

Le raccomandazioni **correzioni di problema di schema** vengono visualizzate quando il servizio di database SQL rileva un'anomalia nel numero di errori SQL correlati allo schema nel database SQL. In genere questa raccomandazione viene visualizzata quando il database rileva più errori correlati allo schema (nome di colonna o di oggetto non valido e così via) nell'arco di un'ora.

Con "Problemi dello schema" si indica una classe di errori di sintassi in SQL Server. Tali errori si verificano quando la definizione della query SQL e quella dello schema del database non sono allineate. Una delle colonne previste dalla query, ad esempio, potrebbe essere assente nella tabella di destinazione o viceversa. 

Le raccomandazioni relative alla "correzione di problemi di schema" vengono visualizzate quando il servizio di database SQL di Azure rileva un'anomalia nel numero di errori SQL correlati al database SQL. La tabella seguente illustra gli errori correlati ai problemi di schema:

| Codice di errore SQL | Message |
| --- | --- |
| 201 |La procedura o funzione '*' richiede il parametro '*', che non è stato specificato. |
| 207 |Il nome di colonna '*' non è valido. |
| 208 |Il nome di oggetto '*' non è valido. |
| 213 |Il nome della colonna o il numero dei valori specificati non corrisponde alla definizione della tabella. |
| 2812 |Non è stato possibile trovare la stored procedure '*'. |
| 8144 |Numero eccessivo di argomenti specificati per la procedura o la funzione *. |

## <a name="next-steps"></a>Passaggi successivi
Monitorare le raccomandazioni e continuare ad applicarle in modo da migliorare le prestazioni. I carichi di lavoro dei database sono dinamici e cambiano in modo continuo. Advisor per database SQL continua a monitorare e a offrire raccomandazioni potenzialmente utili per migliorare le prestazioni del database. 

* Per altre informazioni sull'ottimizzazione automatica di indici del database e di piani di esecuzione di query, vedere [Ottimizzazione automatica nel database SQL di Azure](sql-database-automatic-tuning.md).
* Per altre informazioni sul monitoraggio automatico delle prestazioni del database con diagnostica automatizzata e analisi della causa radice dei problemi di prestazioni, vedere [Intelligent Insights in Azure SQL](sql-database-intelligent-insights.md).
*  Per altre informazioni su come usare le raccomandazioni per le prestazioni nel portale di Azure, vedere [Raccomandazioni per le prestazioni nel portale di Azure](sql-database-advisor-portal.md).
* Per imparare a esaminare l'impatto sulle prestazioni delle query principali, vedere [Query Performance Insight del database SQL di Azure](sql-database-query-performance.md).


