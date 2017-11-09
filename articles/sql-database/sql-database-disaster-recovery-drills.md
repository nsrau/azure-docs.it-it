---
title: Esercitazioni per il ripristino di emergenza del database SQL | Documentazione Microsoft
description: Istruzioni e procedure consigliate relative all'uso del database SQL di Azure per eseguire esercitazioni per il ripristino di emergenza.
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 07/31/2016
ms.author: sashan
ms.openlocfilehash: 8e395153fc9907107156c3412e5e0de554c83750
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="performing-disaster-recovery-drill"></a>Esercitazione per il ripristino di emergenza
È consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

* Simulazione dell'interruzione del livello dati
* Ripristino
* Convalida dell'integrità dell'applicazione dopo il ripristino

A seconda della modalità di [progettazione dell’applicazione per la continuità aziendale](sql-database-business-continuity.md), il flusso di lavoro dell'esercitazione può variare. Di seguito sono descritte le procedure consigliate per eseguire un'esercitazione per il ripristino di emergenza nel contesto del database SQL di Azure.

## <a name="geo-restore"></a>Ripristino geografico
Per evitare il rischio di perdita di dati durante l'esecuzione di un'esercitazione per il ripristino di emergenza, è consigliabile usare un ambiente di test creando una copia dell'ambiente di produzione e usandolo per verificare il flusso di lavoro di failover dell'applicazione.

#### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio
Per simulare l'interruzione è possibile eliminare o rinominare il database di origine. Ciò causa un errore di connettività dell'applicazione.

#### <a name="recovery"></a>Ripristino
* Eseguire il ripristino geografico del database in un server diverso, come descritto [qui](sql-database-disaster-recovery.md).
* Modificare la configurazione dell'applicazione per connettersi ai database ripristinati e seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

#### <a name="validation"></a>Convalida
* Completare l'esercitazione verificando l'integrità dell'applicazione dopo il ripristino (inclusi stringhe di connessione, account di accesso, test di funzionalità di base o altre verifiche correlate alle procedure standard di convalida delle applicazioni).

## <a name="geo-replication"></a>Replica geografica
Per un database protetto mediante la replica geografica l'esercitazione comporta un failover pianificato per il database secondario. Il failover pianificato assicura che i database primario e secondario restino sincronizzati quando si invertono i ruoli. A differenza del failover non pianificato, questa operazione non comporta la perdita di dati, quindi l'esercitazione può essere realizzata nell'ambiente di produzione.

#### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio
Per simulare l'interruzione è possibile disabilitare l'applicazione web o la macchina virtuale connessa al database. Ciò genera errori di connessione per i client web.

#### <a name="recovery"></a>Ripristino
* Assicurarsi che la configurazione dell'applicazione nell'area DR punti al database secondario precedente, che diventa un nuovo database primario completamente accessibile.
* Eseguire [failover pianificato](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) per rendere il database secondario un nuovo database primario
* Seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

#### <a name="validation"></a>Convalida
* Completare l'esercitazione verificando l'integrità dell'applicazione dopo il ripristino (inclusi stringhe di connessione, account di accesso, test di funzionalità di base o altre verifiche correlate alle procedure standard di convalida delle applicazioni).

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sugli scenari di continuità aziendale, vedere l'articolo relativo agli [scenari di continuità](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md).  
