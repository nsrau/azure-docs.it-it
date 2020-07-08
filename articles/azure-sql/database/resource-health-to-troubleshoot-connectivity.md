---
title: Utilizzare Integrità risorse di Azure per monitorare l'integrità del database
description: Usare Integrità risorse di Azure per monitorare il database SQL di Azure e l'integrità di Istanza gestita SQL di Azure, consente di diagnosticare e ottenere supporto quando un problema di Azure ha effetto sulle risorse SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 27865afd356be9eac64083c1ebdeb6ced43dbd18
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986943"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Usare Integrità risorse per risolvere i problemi di connettività per il database SQL di Azure e Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Integrità risorse](../../service-health/resource-health-overview.md#get-started) per database SQL di Azure e azure SQL istanza gestita consente di diagnosticare e ottenere supporto quando un problema di Azure ha effetto sulle risorse SQL. Informa sull'integrità corrente e passata delle risorse e consente di attenuare i problemi. Integrità risorse offre supporto tecnico quando è necessaria assistenza per i problemi relativi ai servizi di Azure.

![Panoramica](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Controlli di integrità

Integrità risorse determina l'integrità della risorsa SQL esaminando gli accessi riusciti e non riusciti alla risorsa stessa. Attualmente, Integrità risorse per la risorsa del database SQL esamina solo gli errori di accesso a causa di un errore di sistema e non di un errore dell'utente. Lo stato del Integrità risorse viene aggiornato ogni 1-2 minuti.

## <a name="health-states"></a>Stati di integrità

### <a name="available"></a>Disponibile

Lo stato **disponibile** indica che integrità risorse non ha rilevato errori di accesso a causa di errori di sistema per la risorsa SQL.

![Disponibile](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degraded

Uno stato ridotto **indica che** integrità risorse ha rilevato la maggior parte degli accessi riusciti, ma anche alcuni errori. Si tratta con ogni probabilità di errori di accesso temporanei. Per ridurre l'effetto dei problemi di connessione causati da errori di accesso temporanei, implementare la [logica di ripetizione dei tentativi](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) nel codice.

![Degraded](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Non disponibile

Lo stato **Non disponibile** indica che Integrità risorse ha rilevato errori di accesso costanti alla risorsa SQL. Se la risorsa rimane in questo stato per un periodo di tempo prolungato, contattare il supporto tecnico.

![Non disponibile](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Sconosciuto

Lo stato **Sconosciuto** indica che Integrità risorse non ha ricevuto informazioni sulla risorsa per più di 10 minuti. Sebbene questo stato non sia un'indicazione definitiva dello stato della risorsa, è un punto dati importante nel processo di risoluzione dei problemi. Se la risorsa viene eseguita come previsto, il suo stato viene aggiornato in Disponibile dopo alcuni minuti. Se si verificano problemi con la risorsa, lo stato di integrità Sconosciuto può indicare che la risorsa è stata interessata da un evento nella piattaforma.

![Sconosciuto](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informazioni cronologiche

È possibile accedere a un massimo di 14 giorni di cronologia dell'integrità nella sezione cronologia integrità di Integrità risorse. Questa sezione contiene anche il motivo (quando disponibile) dei tempi di inattività segnalati da Integrità risorse. Attualmente, Azure Mostra il tempo di inattività per la risorsa del database a una granularità di due minuti. Il tempo di inattività effettivo è probabilmente inferiore a un minuto. La media è di 8 secondi.

### <a name="downtime-reasons"></a>Motivi del tempo di inattività

Quando si verifica un tempo di inattività del database, viene eseguita l'analisi per determinare un motivo. Quando disponibile, il motivo del tempo di inattività viene indicato nella sezione della cronologia integrità di Integrità risorse. I motivi dei tempi di inattività vengono in genere pubblicati 30 minuti dopo un evento.

#### <a name="planned-maintenance"></a>Manutenzione pianificata

L'infrastruttura di Azure esegue periodicamente la manutenzione pianificata, ovvero l'aggiornamento dei componenti hardware o software nel Data Center. Mentre il database viene sottoposto a manutenzione, Azure SQL potrebbe terminare alcune connessioni esistenti e rifiutarne di nuove. Gli errori di accesso riscontrati durante la manutenzione pianificata sono in genere temporanei e la [logica di ripetizione dei tentativi](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) contribuisce a ridurre l'effetto Se continuano a verificarsi errori di accesso, contattare il supporto tecnico.

#### <a name="reconfiguration"></a>Riconfigurazione

Le riconfigurazioni sono considerate condizioni temporanee e sono previste di tanto in tanto. Questi eventi possono essere attivati da errori di bilanciamento del carico o errori software/hardware. Qualsiasi applicazione di produzione client che si connette a un database cloud dovrebbe implementare un'efficace [logica di ripetizione dei tentativi](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) di connessione, perché contribuirebbe ad attenuare queste situazioni e generalmente renderebbe gli errori trasparenti all'utente finale.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [logica di ripetizione dei tentativi per gli errori temporanei](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- [Risolvere i problemi, diagnosticare ed evitare errori di connessione SQL](troubleshoot-common-connectivity-issues.md).
- Altre informazioni sulla [configurazione degli avvisi integrità risorse](../../service-health/resource-health-alert-arm-template-guide.md).
- Ottenere una panoramica del [integrità risorse](../../application-gateway/resource-health-overview.md).
- Esaminare [integrità risorse domande frequenti](../../service-health/resource-health-faq.md).
