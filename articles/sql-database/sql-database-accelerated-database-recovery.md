---
title: Recupero del database accelerato
description: Il database SQL di Azure include una nuova funzionalità che consente un ripristino rapido e coerente del database, un rollback di transazione istantaneo e un troncamento aggressivo del log per i database singoli e i database in pool nel database SQL di Azure, e i database in Azure SQL Data Warehouse.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80238668"
---
# <a name="accelerated-database-recovery"></a>Ripristino accelerato del database

**Accelerated Database Recovery (ADR)** è una funzionalità del motore di database SQL che migliora notevolmente la disponibilità dei database, soprattutto in presenza di transazioni a esecuzione prolungata, riprogettando il processo di ripristino del motore di database SQL. ADR è attualmente disponibile per database SQL di Azure singolo, pool elastico e istanza gestita e database in Azure SQL Data Warehouse (attualmente in anteprima). I principali vantaggi del ripristino accelerato del database sono i seguenti:

- **Ripristino rapido e coerente del database**

  Grazie al ripristino accelerato del database, le transazioni a esecuzione prolungata non influiscono sul tempo di ripristino complessivo e ciò consente un ripristino rapido e coerente del database indipendentemente dal numero di transazioni attive nel sistema o dalle relative dimensioni.

- **Rollback di transazione istantaneo**

  Con il ripristino accelerato del database, il rollback di transazione è istantaneo, indipendentemente dal tempo in cui la transazione è stata attiva o dal numero di aggiornamenti eseguiti.

- **Troncamento del log aggressivo**

  Con ADR, il log delle transazioni viene troncato in modo aggressivo, anche in presenza di transazioni a esecuzione prolungata attive, che ne impedisce la crescita fuori controllo.

## <a name="the-current-database-recovery-process"></a>Processo di ripristino del database corrente

Il ripristino del database in SQL Server segue il modello di ripristino [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e prevede tre fasi, visualizzate e descritte nel dettaglio nella figura seguente.

![Processo di ripristino corrente](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fase di analisi**

  Eseguire l'analisi del log delle transazioni a partire dall'inizio dell'ultimo checkpoint riuscito (o dall'LSN di pagina dirty meno recente) fino al termine, per determinare lo stato di ogni transazione al momento dell'arresto SQL Server.

- **Fase di rollforward**

  Eseguire l'analisi del log delle transazioni dalla transazione di cui non è stato eseguito il commit meno recente fino alla fine, per portare il database allo stato in cui si trovava al momento dell'arresto anomalo del sistema ripetendo tutte le operazioni di commit.

- **Fase di rollback**

  Per ogni transazione attiva al momento dell'arresto anomalo, esegue il rollback del log, annullando le operazioni eseguite dalla transazione.

In base a questa progettazione, il tempo necessario per il ripristino del motore di database SQL da un riavvio imprevisto è pressoché proporzionale alla dimensione della transazioni attiva più lunga presente nel sistema al momento dell'arresto anomalo. Il ripristino richiede un rollback di tutte le transazioni incomplete. La quantità di tempo necessaria è proporzionale al lavoro eseguito dalla transazione e al tempo per cui è stata attiva. Pertanto, il processo di ripristino SQL Server può richiedere molto tempo in presenza di transazioni con esecuzione prolungata, ad esempio operazioni BULK INSERT di grandi dimensioni o operazioni di compilazione di indici su una tabella di grandi dimensioni.

In base a questa progettazione, anche l'annullamento o il rollback di una transazione di grandi dimensioni può richiedere molto tempo poiché viene usata la stessa fase di rollback descritta sopra.

Inoltre, il motore di database SQL non può troncare il log delle transazioni quando sono presenti transazioni con esecuzione prolungata perché i relativi record di log sono necessari per i processi di ripristino e rollback. In seguito a questa progettazione del motore di database SQL, alcuni clienti usavano affrontare il problema che le dimensioni del log delle transazioni crescono molto e consumano grandi quantità di spazio su disco.

## <a name="the-accelerated-database-recovery-process"></a>Processo di ripristino accelerato del database

Il ripristino accelerato del database (ADR) risolve i problemi descritti sopra riprogettando completamente il processo di ripristino del motore di database SQL per:

- Rendere il ripristino costante e istantaneo, in quanto non è necessario analizzare il log da o fino all'inizio della transazione attiva meno recente. Con ADR, il log delle transazioni viene elaborato solo dall'ultimo checkpoint completato o dal numero di sequenza del file di log (LSN) della pagina dirty più recente. Di conseguenza, le transazioni a esecuzione prolungata non influiscono sul tempo di ripristino.
- Ridurre al minimo lo spazio necessario per il log delle transazioni in quanto non è più necessario elaborare il log per l'intera transazione. Di conseguenza, è possibile troncare il log delle transazioni in modo aggressivo quando si verificano checkpoint e backup.

A un livello elevato, ADR consente il ripristino rapido del database tramite il controllo delle versioni di tutte le modifiche fisiche del database e l'annullamento di operazioni logiche, che sono limitate e che possono essere annullate quasi immediatamente. Tutte le transazioni attive al momento dell'arresto anomalo vengono contrassegnate come interrotte e, pertanto, tutte le versioni generate da queste transazioni possono essere ignorate dalle query utente simultanee.

Il processo di ripristino accelerato del database prevede le stesse tre fasi del processo di ripristino corrente. Il funzionamento delle tre fasi del ripristino accelerato del database (ADR) è illustrato nella figura seguente e descritto nel dettaglio di seguito.

![Processo di ripristino accelerato del database](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Fase di analisi**

  Il processo resta identico a quello precedente, con l'aggiunta di ricostruire i record di log di sgobbare e di copia per le operazioni senza versione.
  
- Fase di **rollforward**

  Suddivisa in due fasi (P)
  - Fase 1

      Rollforward da sLog (dalla transazione meno recente di cui non è stato eseguito il commit fino all'ultimo checkpoint). La fase di rollforward è un'operazione rapida, in quanto è necessario elaborare solo pochi record da sLog.
      
  - Fase 2

     Il rollforward dal log delle transazioni inizia dall'ultimo checkpoint (anziché dalla transazione meno recente di cui non è stato eseguito il commit)
     
- **Fase di rollback**

   Con il ripristino accelerato del database (ADR) la fase di rollback viene eseguita quasi istantaneamente usando sLog per annullare le operazioni senza controllo delle versioni e l'archivio versioni permanente (Persisted Version Store, PVS) con ripristino logico per eseguire il rollback basato sulla versione a livello di riga.

## <a name="adr-recovery-components"></a>Componenti del ripristino accelerato del database

I quattro componenti chiave del ripristino accelerato del database sono i seguenti:

- **Archivio versioni permanente (Persisted Version Store, PVS)**

  L'archivio versioni permanente è un nuovo meccanismo del motore di database SQL per il mantenimento delle versioni di riga generate nel database anziché nell'archivio versioni `tempdb` tradizionale. L'archivio versioni permanente (PVS) abilita l'isolamento delle risorse e migliora la disponibilità delle repliche secondarie leggibili.

- **Ripristino logico**

  Il ripristino logico è il processo asincrono responsabile dell'esecuzione dell'annullamento basato sulla versione a livello di riga che fornisce il rollback della transazione immediata e Annulla per tutte le operazioni con versione. Il ripristino logico viene eseguito da:

  - Tenere traccia di tutte le transazioni interrotte e contrassegnarle come invisibile ad altre transazioni. 
  - Esecuzione del rollback utilizzando PVS per tutte le transazioni utente, anziché analizzare fisicamente il log delle transazioni e annotare le modifiche una alla volta.
  - Rilascio di tutti i blocchi immediatamente dopo l'interruzione della transazione. Poiché Abort implica semplicemente il contrassegno delle modifiche nella memoria, il processo è molto efficiente e pertanto non è necessario che i blocchi vengano mantenuti per un lungo periodo di tempo.

- **sLog**

  sLog è un flusso di log in memoria secondario che archivia i record di log per le operazioni senza controllo delle versioni, ad esempio l'invalidamento della cache dei metadati, le acquisizioni di blocchi e così via. sLog ha le caratteristiche seguenti:

  - Volume ridotto e in memoria
  - Salvataggio in modo permanente sul disco mediante la serializzazione durante il processo di checkpoint
  - Troncamento periodico quando viene eseguito il commit delle transazioni
  - Accelerazione delle fasi di rollback e rollforward grazie all'elaborazione solo delle operazioni senza controllo delle versioni  
  - Troncamento del log delle transazioni aggressivo mantenendo solo i record di log necessari

- **Pulizia**

  La pulizia è un processo asincrono che viene riattivato periodicamente e pulisce le versioni di pagina non necessarie.

## <a name="accelerated-database-recovery-patterns"></a>Modelli di recupero accelerato del database

I tipi di carichi di lavoro seguenti traggono più vantaggio dall'ADR:

- Carichi di lavoro con transazioni a esecuzione prolungata.
- I carichi di lavoro che hanno rilevato casi in cui le transazioni attive causano un aumento significativo del log delle transazioni.  
- Carichi di lavoro in cui si sono verificati lunghi periodi di indisponibilità del database a causa di SQL Server ripristino con esecuzione prolungata, ad esempio il riavvio imprevisto SQL Server o il rollback manuale della transazione

