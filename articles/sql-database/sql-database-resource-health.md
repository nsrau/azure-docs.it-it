---
title: Usare Integrità risorse di Azure per monitorare l'integrità dei database SQL | Microsoft Docs
description: Usare Integrità risorse di Azure per monitorare l'integrità dei database SQL, diagnosticare gli eventuali problemi di Azure che possono influire negativamente sulle risorse e ottenere il supporto necessario.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: c3b9fecd3ad404385732e55a9cf3aa65a6e388b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483026"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Usare Integrità risorse di Azure per risolvere i problemi di connettività del database SQL di Azure

## <a name="overview"></a>Panoramica

[Integrità risorse](../service-health/resource-health-overview.md#getting-started) per il database SQL aiuta a diagnosticare gli eventuali problemi di Azure che possono influire negativamente sulle risorse SQL e a ottenere il supporto necessario. Informa sull'integrità corrente e passata delle risorse e consente di attenuare i problemi. Integrità risorse offre supporto tecnico quando è necessaria assistenza per problemi con i servizi di Azure.

![Panoramica](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Controlli di integrità

Integrità risorse determina l'integrità della risorsa SQL esaminando gli accessi riusciti e non riusciti alla risorsa stessa. Attualmente Integrità risorse per il database SQL esamina solo gli accessi non riusciti a causa di un errore del sistema, non dell'utente. Lo stato di Integrità risorse viene aggiornato ogni 1-2 minuti.

## <a name="health-states"></a>Stati di integrità

### <a name="available"></a>Disponibile

Lo stato **Disponibile** indica che Integrità risorse non ha rilevato problemi di accesso dovuti a errori di sistema nella risorsa SQL.

![Disponibile](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degraded

Lo stato **Danneggiato** indica che Integrità risorse ha rilevato una maggioranza di accessi riusciti, ma anche alcuni accessi non riusciti. Si tratta con ogni probabilità di errori di accesso temporanei. Per ridurre l'impatto dei problemi di connessione causati dagli errori di accesso temporanei, implementare la [logica di ripetizione dei tentativi](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) nel codice.

![Degraded](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Non disponibile

Lo stato **Non disponibile** indica che Integrità risorse ha rilevato errori di accesso costanti alla risorsa SQL. Se la risorsa rimane in questo stato per un periodo di tempo prolungato, contattare il supporto.

![Non disponibile](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Sconosciuto

Lo stato **Sconosciuto** indica che Integrità risorse non ha ricevuto informazioni sulla risorsa per più di 10 minuti. Sebbene questo stato non sia un'indicazione definitiva dello stato della risorsa, è un punto dati importante nel processo di risoluzione dei problemi. Se la risorsa viene eseguita come previsto, il suo stato viene aggiornato in Disponibile dopo alcuni minuti. Se si verificano problemi con la risorsa, lo stato di integrità Sconosciuto può indicare che la risorsa è stata interessata da un evento nella piattaforma.

![Sconosciuto](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informazioni cronologiche

È possibile accedere a un massimo di 14 giorni di cronologia di informazioni sull'integrità nella sezione relativa alla cronologia integrità in Integrità risorse. Questa sezione contiene anche il motivo (quando disponibile) dei tempi di inattività segnalati da Integrità risorse. Attualmente Azure mostra il tempo di inattività della risorsa del database SQL con una granularità di due minuti. Il tempo di inattività effettivo è probabilmente inferiore a un minuto. La media è di 8 secondi.

### <a name="downtime-reasons"></a>Motivi del tempo di inattività

Quando si registra un tempo di inattività per il database SQL, viene eseguita un'analisi per determinarne il motivo. Quando disponibile, il motivo del tempo di inattività viene indicato nella sezione della cronologia integrità di Integrità risorse. I motivi dei tempi di inattività vengono in genere pubblicati 30 minuti dopo un evento.

#### <a name="planned-maintenance"></a>Manutenzione pianificata

L'infrastruttura di Azure esegue periodicamente una manutenzione pianificata, ossia un aggiornamento dei componenti hardware o software nel data center. Mentre il database viene sottoposto a manutenzione, SQL può terminare alcune connessioni esistenti e rifiutare nuove connessioni. Gli errori di accesso che si verificano durante una manutenzione pianificata sono in genere temporanei e la [logica di ripetizione dei tentativi](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) contribuisce a ridurne l'impatto. Se gli errori di accesso persistono, contattare il supporto.

#### <a name="reconfiguration"></a>Riconfigurazione

Le riconfigurazioni sono considerate condizioni transitorie, che si verificano di tanto in tanto. Questi eventi possono essere attivati da errori di bilanciamento del carico o errori software/hardware. Qualsiasi applicazione di produzione client che si connette a un database cloud dovrebbe implementare un'efficace [logica di ripetizione dei tentativi](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) di connessione, perché contribuirebbe ad attenuare queste situazioni e generalmente renderebbe gli errori trasparenti all'utente finale.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [logica di ripetizione dei tentativi per errori temporanei](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Risolvere, diagnosticare ed evitare gli errori di connessione SQL e gli errori temporanei per il database SQL](./sql-database-connectivity-issues.md)
- Altre informazioni su come [configurare gli avvisi di Integrità risorse](../service-health/resource-health-alert-arm-template-guide.md)
- Panoramica di [Integrità risorse](../service-health/resource-health-overview.md)
- [Domande frequenti su Integrità risorse](../service-health/resource-health-faq.md)
