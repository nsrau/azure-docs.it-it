---
title: Recupero del database accelerato
titleSuffix: Azure SQL
description: Il recupero accelerato del database offre un ripristino di database rapido e coerente, un rollback delle transazioni istantaneo e un troncamento del log aggressivo per i database nel portfolio SQL di Azure.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 05/19/2020
ms.openlocfilehash: a6d95bbcb0873086a799dcf216beab4a6b0d33de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84344697"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Recupero accelerato del database in SQL di Azure 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Il **recupero accelerato del database** è una funzionalità del motore di database SQL Server che migliora notevolmente la disponibilità dei database, soprattutto in presenza di transazioni a esecuzione prolungata, riprogettando il processo di ripristino del motore di database SQL Server. ADR è attualmente disponibile per database SQL di Azure, Istanza gestita SQL di Azure, SQL Server nella macchina virtuale di Azure e database in Azure sinapsi Analytics (attualmente in anteprima). I principali vantaggi del ripristino accelerato del database sono i seguenti:

- **Ripristino rapido e coerente del database**

  Grazie al ripristino accelerato del database, le transazioni a esecuzione prolungata non influiscono sul tempo di ripristino complessivo e ciò consente un ripristino rapido e coerente del database indipendentemente dal numero di transazioni attive nel sistema o dalle relative dimensioni.

- **Rollback di transazione istantaneo**

  Con il ripristino accelerato del database, il rollback di transazione è istantaneo, indipendentemente dal tempo in cui la transazione è stata attiva o dal numero di aggiornamenti eseguiti.

- **Troncamento del log aggressivo**

  Con ADR, il log delle transazioni viene troncato in modo aggressivo, anche in presenza di transazioni a esecuzione prolungata attive, che ne impedisce la crescita fuori controllo.

## <a name="standard-database-recovery-process"></a>Processo di ripristino del database standard

Il recupero del database segue il modello di recupero [Ariete](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) ed è costituito da tre fasi, illustrate nel diagramma seguente e descritte in modo più dettagliato dopo il diagramma.

![Processo di ripristino corrente](./media/accelerated-database-recovery/current-recovery-process.png)

- **Fase di analisi**

  Eseguire l'analisi del log delle transazioni dall'inizio dell'ultimo checkpoint riuscito (o dell'LSN di pagina dirty meno recente) fino alla fine, per determinare lo stato di ogni transazione al momento dell'arresto del database.

- **Fase di rollforward**

  Eseguire l'analisi del log delle transazioni dalla transazione di cui non è stato eseguito il commit meno recente fino alla fine, per portare il database allo stato in cui si trovava al momento dell'arresto anomalo del sistema ripetendo tutte le operazioni di commit.

- **Fase di rollback**

  Per ogni transazione attiva al momento dell'arresto anomalo, esegue il rollback del log, annullando le operazioni eseguite dalla transazione.

In base a questa progettazione, il tempo necessario per il recupero del motore di database SQL Server da un riavvio imprevisto è (approssimativamente) proporzionale alle dimensioni della transazione attiva più estesa nel sistema al momento dell'arresto anomalo. Il ripristino richiede un rollback di tutte le transazioni incomplete. La quantità di tempo necessaria è proporzionale al lavoro eseguito dalla transazione e al tempo per cui è stata attiva. Pertanto, il processo di ripristino può richiedere molto tempo in presenza di transazioni a esecuzione prolungata, ad esempio operazioni di inserimento bulk di grandi dimensioni o operazioni di compilazione di indici su una tabella di grandi dimensioni.

In base a questa progettazione, anche l'annullamento o il rollback di una transazione di grandi dimensioni può richiedere molto tempo poiché viene usata la stessa fase di rollback descritta sopra.

Inoltre, il motore di database SQL Server non è in grado di troncare il log delle transazioni quando sono presenti transazioni con esecuzione prolungata perché i relativi record di log sono necessari per i processi di ripristino e rollback. In seguito a questa progettazione del motore di database di SQL Server, alcuni clienti usavano affrontare il problema che le dimensioni del log delle transazioni crescono molto e consumano grandi quantità di spazio su disco.

## <a name="the-accelerated-database-recovery-process"></a>Processo di ripristino accelerato del database

ADR risolve i problemi precedenti riprogettando completamente la SQL Server processo di ripristino del motore di database per:

- Rendere il ripristino costante e istantaneo, in quanto non è necessario analizzare il log da o fino all'inizio della transazione attiva meno recente. Con ADR, il log delle transazioni viene elaborato solo dall'ultimo checkpoint completato o dal numero di sequenza del file di log (LSN) della pagina dirty più recente. Di conseguenza, le transazioni a esecuzione prolungata non influiscono sul tempo di ripristino.
- Ridurre al minimo lo spazio necessario per il log delle transazioni in quanto non è più necessario elaborare il log per l'intera transazione. Di conseguenza, è possibile troncare il log delle transazioni in modo aggressivo quando si verificano checkpoint e backup.

A livello generale, il ripristino accelerato del database consente il ripristino rapido del database tramite il controllo delle versioni di tutte le modifiche fisiche del database e il rollback solo delle operazioni logiche, che sono limitate e che consentono un rollback quasi istantaneo. Tutte le transazioni attive al momento dell'arresto anomalo vengono contrassegnate come interrotte e, pertanto, tutte le versioni generate da queste transazioni possono essere ignorate dalle query utente simultanee.

Il processo di ripristino accelerato del database prevede le stesse tre fasi del processo di ripristino corrente. Il funzionamento delle tre fasi del ripristino accelerato del database (ADR) è illustrato nella figura seguente e descritto nel dettaglio di seguito.

![Processo di ripristino accelerato del database](./media/accelerated-database-recovery/adr-recovery-process.png)

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

- **Archivio versioni permanente (PVS)**

  L'archivio versioni persistente è un nuovo meccanismo del motore di database SQL Server per salvare in modo permanente le versioni di riga generate nel database stesso anziché nell' `tempdb` archivio versioni tradizionale. L'archivio versioni permanente (PVS) abilita l'isolamento delle risorse e migliora la disponibilità delle repliche secondarie leggibili.

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
- Carichi di lavoro in cui si sono verificati periodi prolungati di indisponibilità del database a causa di un ripristino con esecuzione prolungata, ad esempio riavvio imprevisto del servizio o rollback manuale
