---
title: Esercitazioni per il ripristino di emergenza del database SQL | Documentazione Microsoft
description: Istruzioni e procedure consigliate relative all'uso del database SQL di Azure per eseguire esercitazioni per il ripristino di emergenza.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 10/20/2016
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 10a3891c51d20a26b946847d47bb712d97fa3c51
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="performing-disaster-recovery-drill"></a>Esercitazione per il ripristino di emergenza
È consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

* Simulazione dell'interruzione del livello dati
* Ripristino
* Convalida dell'integrità dell'applicazione dopo il ripristino

A seconda della modalità di [progettazione dell’applicazione per la continuità aziendale](sql-database-business-continuity.md), il flusso di lavoro dell'esercitazione può variare. Questo articolo descrive le procedure consigliate per eseguire un test per il ripristino di emergenza nel contesto del database SQL di Azure.

## <a name="geo-restore"></a>Ripristino geografico
Per evitare il rischio di perdita di dati durante l'esecuzione di un test per il ripristino di emergenza, è consigliabile usare un ambiente di test creando una copia dell'ambiente di produzione e usandola per verificare il flusso di lavoro di failover dell'applicazione.

#### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio
Per simulare l'interruzione è possibile rinominare il database di origine. Ciò causa un errore di connettività dell'applicazione.

#### <a name="recovery"></a>Ripristino
* Eseguire il ripristino geografico del database in un server diverso, come descritto [qui](sql-database-disaster-recovery.md).
* Modificare la configurazione dell'applicazione per connettersi ai database ripristinati e seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

#### <a name="validation"></a>Convalida
* Completare il test verificando l'integrità dell'applicazione dopo il ripristino, includendo stringhe di connessione, accessi, test di funzionalità di base o altre verifiche correlate alle procedure standard di signoff delle applicazioni.

## <a name="failover-groups"></a>Gruppi di failover
Per un database protetto mediante i gruppi di failover l'esercitazione comporta un failover pianificato per il server secondario. Il failover pianificato assicura che i database primario e secondario nel gruppo di failover restino sincronizzati quando si invertono i ruoli. A differenza del failover non pianificato, questa operazione non comporta la perdita di dati, quindi l'esercitazione può essere realizzata nell'ambiente di produzione.

#### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio
Per simulare l'interruzione è possibile disabilitare l'applicazione web o la macchina virtuale connessa al database. Ciò genera errori di connessione per i client web.

#### <a name="recovery"></a>Ripristino
* Assicurarsi che la configurazione dell'applicazione nell'area DR punti al database secondario precedente, che diventa il nuovo database primario completamente accessibile.
* Attivare il [failover pianificato](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) del gruppo di failover dal server secondario.
* Seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

#### <a name="validation"></a>Convalida
Completare il test verificando l'integrità dell'applicazione dopo il ripristino, includendo connettività, test di funzionalità di base o altre verifiche necessarie per i signoff del test.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sugli scenari di continuità aziendale, vedere l'articolo relativo agli [scenari di continuità](sql-database-business-continuity.md).
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Panoramica: Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
* Per altre informazioni su opzioni di ripristino più veloci, consultare l'articolo sui [gruppi di failover e la replica geografica attiva](sql-database-geo-replication-overview.md).  
