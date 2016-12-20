---
title: Advisor per database SQL di Azure
description: L&quot;advisor per database SQL di Azure offre raccomandazioni per i database SQL esistenti che consentono di migliorare le prestazioni correnti delle query.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f7bffa4f13d7a4fa0ea3c120a826638ee5d6c65a


---
# <a name="sql-database-advisor"></a>Advisor per database SQL
> [!div class="op_single_selector"]
> * [Panoramica di Advisor per database SQL](sql-database-advisor.md)
> * [Portale](sql-database-advisor-portal.md)
> 
> 

Il database SQL di Azure apprende e si adatta all'applicazione e offre raccomandazioni personalizzate che consentono di ottimizzare le prestazioni dei database SQL. SQL Database Advisor offre raccomandazioni per la creazione e la rimozione di indici, la parametrizzazione di query e la risoluzione dei problemi di schema. L'advisor consente di valutare le prestazioni analizzando la cronologia relativa all'utilizzo del database SQL. È consigliabile usare le raccomandazioni più adatte per l'esecuzione del carico di lavoro tipico del database. 

Sono disponibili le raccomandazioni seguenti per i server V12 (non sono disponibili raccomandazioni per i server V11). Attualmente è possibile impostare l'applicazione automatica delle raccomandazioni per la creazione e l'eliminazione di indici. Per informazioni dettagliate, vedere [Abilitare la gestione automatica degli indici](sql-database-advisor-portal.md#enable-automatic-index-management).

## <a name="create-index-recommendations"></a>Raccomandazioni relative alla creazione di indici
**creazione di indici** vengono visualizzate quando il servizio del database SQL rileva un indice mancante che, se creato, potrebbe risultare vantaggioso per il carico di lavoro dei database (solo indici non cluster).

## <a name="drop-index-recommendations"></a>Raccomandazioni relative all'eliminazione di indici
**eliminazione di indici** vengono visualizzate quando il servizio del database SQL rileva indici duplicati (attualmente in anteprima e applicabile solo agli indici duplicati).

## <a name="parameterize-queries-recommendations"></a>Raccomandazioni relative alla creazione di query con parametri
Le raccomandazioni relative alla **creazione di query con parametri** vengono visualizzate quando sono presenti una o più query che vengono ricompilate costantemente ma finiscono con lo stesso piano di esecuzione di query. Questa condizione fa emergere l'opportunità di applicare l'impostazione forzata di parametri, in modo da permettere la memorizzazione nella cache e il riutilizzo delle query in futuro, con conseguente miglioramento delle prestazioni e riduzione dell'utilizzo delle risorse. 

Ogni query eseguita inizialmente su SQL Server deve essere compilata per generare un piano di esecuzione. Ogni piano generato viene aggiunto alla cache dei piani e le esecuzioni successive della stessa query possono riusare il piano dalla cache, eliminando la necessità di un'ulteriore compilazione. 

Le applicazioni che inviano query che includono valori senza parametri possono causare un overhead delle prestazioni, in cui per ogni query di questo tipo con valori di parametri diversi viene compilato di nuovo il piano di esecuzione. In molti casi le stesse query con valori di parametri diversi generano gli stessi di piani di esecuzione, ma questi piani vengono comunque aggiunti separatamente alla cache dei piani. La ricompilazione dei piani di esecuzione determina l'uso delle risorse del database, l'aumento della durata della query e l'overflow della cache dei piani con conseguente rimozione dei piani dalla cache. Questo comportamento di SQL Server può essere modificato impostando l'opzione di parametrizzazione forzata nel database. 

Per stimare l'impatto di questa raccomandazione, viene illustrato un confronto tra l'uso effettivo e quello previsto della CPU (nel caso di applicazione della raccomandazione). Oltre al risparmio della CPU, anche la durata della query diminuisce per il tempo impiegato nella compilazione. Anche il sovraccarico della cache dei piani sarà nettamente ridotto, consentendo alla maggioranza dei piani di rimanere nella cache e di essere riusati. Questa raccomandazione può essere applicata rapidamente e facilmente facendo clic sul comando **Applica**. 

Dopo l'applicazione, questa raccomandazione abiliterà la parametrizzazione forzata sul database entro pochi minuti e inizierà il processo di monitoraggio che dura circa 24 ore. Trascorso questo intervallo di tempo, sarà possibile visualizzare il report di convalida che mostra l'utilizzo della CPU del database nelle 24 ore prima e dopo l'applicazione la raccomandazione. SQL Database Advisor include un meccanismo di sicurezza che ripristina automaticamente la raccomandazione applicata in caso di rilevamento di una regressione delle prestazioni.

## <a name="fix-schema-issues-recommendations"></a>Raccomandazioni relative alla correzione di problemi di schema
Le raccomandazioni relative alla **correzione di problemi di schema** vengono visualizzate quando il servizio del database SQL rileva un'anomalia nel numero di errori SQL correlati allo schema nel database SQL di Azure. In genere questa indicazione viene visualizzata quando il database rileva più errori correlati allo schema (nome di colonna non valido, nome di oggetto non valido e così via) nell'arco di un'ora.

I "problemi di schema" rappresentano una classe di errori di sintassi in SQL Server che si verificano quando la definizione della query SQL e la definizione dello schema del database non sono allineate. Ad esempio, una delle colonne previste dalla query può essere assente nella tabella di destinazione o viceversa. 

Le raccomandazioni relative alla "correzione di problemi di schema" vengono visualizzate quando il servizio del database SQL di Azure rileva un'anomalia nel numero di errori SQL correlati allo schema nel database SQL di Azure. La tabella seguente illustra gli errori correlati ai problemi di schema:

| Codice di errore SQL | Message |
| --- | --- |
| 201 |La procedura o funzione '*' richiede il parametro '*', che non è stato specificato. |
| 207 |Il nome di colonna '*' non è valido. |
| 208 |Il nome di oggetto '*' non è valido. |
| 213 |Il nome della colonna o il numero dei valori specificati non corrisponde alla definizione della tabella. |
| 2812 |Non è stato possibile trovare la stored procedure '*'. |
| 8144 |Numero eccessivo di argomenti specificati per la procedura o la funzione *. |

## <a name="next-steps"></a>Passaggi successivi
Monitorare le raccomandazioni e continuare ad applicarle in modo da migliorare le prestazioni. I carichi di lavoro dei database sono dinamici e cambiano in modo continuo. SQL Database Advisor continua a monitorare e offrire raccomandazioni potenzialmente utili per migliorare le prestazioni del database. 

* Vedere l'articolo su [Advisor per database SQL nel portale di Azure](sql-database-advisor-portal.md) per istruzioni sul relativo uso.
* Per imparare a esaminare l'impatto sulle prestazioni delle query principali, vedere [Query Performance Insight del database SQL di Azure](sql-database-query-performance.md).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Archivio query](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md)




<!--HONumber=Nov16_HO3-->


