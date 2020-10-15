---
title: Esercitazioni sul ripristino di emergenza
description: Istruzioni e procedure consigliate relative all'uso del database SQL di Azure per eseguire esercitazioni per il ripristino di emergenza.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: f53a08a12c5afda8dbc3f25d9102f52b870ceea4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321663"
---
# <a name="performing-disaster-recovery-drills"></a>Esecuzione di esercitazioni sul ripristino di emergenza
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

È consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

* Simulazione dell'interruzione del livello dati
* Ripristino
* Convalida dell'integrità dell'applicazione dopo il ripristino

A seconda della modalità di [progettazione dell’applicazione per la continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md), il flusso di lavoro dell'esercitazione può variare. Questo articolo descrive le procedure consigliate per eseguire un test per il ripristino di emergenza nel contesto del database SQL di Azure.

## <a name="geo-restore"></a>Ripristino geografico

Per evitare il rischio di perdita di dati durante l'esecuzione di un test per il ripristino di emergenza, è consigliabile usare un ambiente di test creando una copia dell'ambiente di produzione e usandola per verificare il flusso di lavoro di failover dell'applicazione.

### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio

Per simulare l'interruzione è possibile rinominare il database di origine. Questa modifica del nome causa errori di connettività dell'applicazione.

### <a name="recovery"></a>Ripristino

* Eseguire il ripristino geografico del database in un server diverso, come descritto [qui](disaster-recovery-guidance.md).
* Modificare la configurazione dell'applicazione per connettersi ai database ripristinati e seguire la guida [Configurare un database dopo il ripristino](disaster-recovery-guidance.md) per completare il ripristino.

### <a name="validation"></a>Convalida

Completare il test verificando l'integrità dell'applicazione dopo il ripristino, includendo stringhe di connessione, accessi, test di funzionalità di base o altre verifiche correlate alle procedure standard di signoff delle applicazioni.

## <a name="failover-groups"></a>Gruppi di failover

Per un database protetto mediante i gruppi di failover l'esercitazione comporta un failover pianificato per il server secondario. Il failover pianificato assicura che i database primario e secondario nel gruppo di failover restino sincronizzati quando si invertono i ruoli. A differenza del failover non pianificato, questa operazione non comporta la perdita di dati, quindi l'esercitazione può essere realizzata nell'ambiente di produzione.

### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio

Per simulare l'interruzione è possibile disabilitare l'applicazione web o la macchina virtuale connessa al database. Questa simulazione di interruzione determina errori di connettività per i client Web.

### <a name="recovery"></a>Ripristino

* Assicurarsi che la configurazione dell'applicazione nell'area DR punti al database secondario precedente, che diventa il nuovo database primario completamente accessibile.
* Attivare il [failover pianificato](scripts/setup-geodr-and-failover-database-powershell.md) del gruppo di failover dal server secondario.
* Seguire la guida [Configurare un database dopo il ripristino](disaster-recovery-guidance.md) per completare il ripristino.

### <a name="validation"></a>Convalida

Completare il test verificando l'integrità dell'applicazione dopo il ripristino, includendo connettività, test di funzionalità di base o altre verifiche necessarie per i signoff del test.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sugli scenari di continuità aziendale, vedere l'articolo relativo agli [scenari di continuità](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Per informazioni sui backup automatici del database SQL di Azure, vedere [backup automatici del database SQL](automated-backups-overview.md)
* Per informazioni sull'uso dei backup automatici per il ripristino, vedere [ripristinare un database dai backup avviati dal servizio](recovery-using-backups.md).
* Per altre informazioni su opzioni di ripristino più veloci, vedere [Replica geografica attiva](active-geo-replication-overview.md) e [Gruppi di failover automatico](auto-failover-group-overview.md).
