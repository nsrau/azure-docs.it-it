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
ms.date: 10/20/2016
ms.workload: Inactive
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 73c2cbe978c980cbe1269b34cdb9f5ff86113e61
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="performing-disaster-recovery-drill"></a>Esercitazione per il ripristino di emergenza
È consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

* Simulazione dell'interruzione del livello dati
* Ripristino
* Convalida dell'integrità dell'applicazione dopo il ripristino

A seconda della modalità di [progettazione dell’applicazione per la continuità aziendale](sql-database-business-continuity.md), il flusso di lavoro dell'esercitazione può variare. Questo articolo descrive le procedure consigliate per l'esecuzione di un'analisi di ripristino di emergenza nel contesto di Database SQL di Azure.

## <a name="geo-restore"></a>Ripristino geografico
Per evitare la perdita di dati durante l'esecuzione di un'analisi di ripristino di emergenza, eseguire l'analisi di utilizzo di un ambiente di test creando una copia dell'ambiente di produzione e utilizzarlo per verificare il flusso di failover dell'applicazione.

#### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio
Per simulare l'interruzione, è possibile rinominare il database di origine. Ciò causa un errore di connettività dell'applicazione.

#### <a name="recovery"></a>Ripristino
* Eseguire il ripristino geografico del database in un server diverso, come descritto [qui](sql-database-disaster-recovery.md).
* Modificare la configurazione dell'applicazione per connettersi ai database ripristinati e seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

#### <a name="validation"></a>Convalida
* Completare l'analisi verificando il ripristino di post di integrità dell'applicazione (incluse le stringhe di connessione, gli account di accesso, il test delle funzionalità di base o altra parte le convalide delle procedure conclusioni standard dell'applicazione).

## <a name="failover-groups"></a>Gruppi di failover
Per un database protetto con gruppi di failover, l'esercizio drill comporta il failover pianificato sul server secondario. Il failover pianificato assicura che il database primario e secondari nel gruppo di failover rimangano sincronizzati quando i ruoli vengono scambiati. A differenza del failover non pianificato, questa operazione non comporta la perdita di dati, quindi l'esercitazione può essere realizzata nell'ambiente di produzione.

#### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio
Per simulare l'interruzione è possibile disabilitare l'applicazione web o la macchina virtuale connessa al database. Ciò genera errori di connessione per i client web.

#### <a name="recovery"></a>Ripristino
* Verificare che la configurazione dell'applicazione nei punti di ripristino di emergenza area per il primo secondario, che diventa il nuovo database primario completamente accessibile.
* Avviare [failover pianificato](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) del gruppo di failover dal server secondario.
* Seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

#### <a name="validation"></a>Convalida
Completare l'analisi verificando il ripristino di post di integrità dell'applicazione (inclusi connettività e funzionalità di base o altre convalide necessarie per le conclusioni drill).

## <a name="next-steps"></a>Passaggi successivi
* Per ulteriori informazioni sugli scenari di continuità aziendale, vedere [gli scenari di continuità](sql-database-business-continuity.md).
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Panoramica: Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
* Per informazioni sulle opzioni di ripristino più veloce, vedere [gruppi di replica geografica e failover active](sql-database-geo-replication-overview.md).  
