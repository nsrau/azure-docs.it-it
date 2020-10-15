---
title: Pianificare gli eventi di manutenzione di Azure
description: Informazioni su come prepararsi per gli eventi di manutenzione pianificata nel database SQL di Azure e in Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: sstein
ms.date: 08/25/2020
ms.openlocfilehash: 3f87f47f652f71a57796d1cacd047b0448b49b7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333036"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Pianificare gli eventi di manutenzione di Azure nel database SQL di Azure e in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Informazioni su come prepararsi per gli eventi di manutenzione pianificata nel database nel database SQL di Azure e in Azure SQL Istanza gestita.

## <a name="what-is-a-planned-maintenance-event"></a>Che cos'è un evento di manutenzione pianificata?

Per garantire la sicurezza, la conformità, la stabilità e l'esecuzione degli aggiornamenti del database SQL di Azure e di Azure SQL Istanza gestita Services, i componenti del servizio vengono eseguiti quasi continuamente. Grazie all'architettura del servizio moderna e affidabile e a tecnologie innovative come l'applicazione di [patch a caldo](https://aka.ms/azuresqlhotpatching), la maggior parte degli aggiornamenti è completamente trasparente e non influisca in termini di disponibilità dei servizi. Tuttavia, pochi tipi di aggiornamenti causano brevi interruzioni del servizio e richiedono un trattamento speciale. 

Per ogni database, il database SQL di Azure e Azure SQL Istanza gestita gestiscono un quorum di repliche di database in cui una replica è la replica primaria. In ogni momento, una replica primaria deve essere la manutenzione in linea e almeno una replica secondaria deve essere integra. Durante la manutenzione pianificata, i membri del quorum del database passeranno offline uno alla volta, in quanto sono presenti una replica primaria pronta a rispondere e almeno una replica secondaria online per evitare tempi di inattività del client. Quando la replica primaria deve essere portata offline, si verifica un processo di riconfigurazione/failover in cui una replica secondaria diventa la nuova replica primaria.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Esecuzione di un evento di manutenzione pianificata

L'evento di manutenzione può produrre failover singoli o multipli, a seconda della costellazione delle repliche primarie e secondarie all'inizio dell'evento di manutenzione. In media, si verificano i failover 1,7 per ogni evento di manutenzione pianificata. Le riconfigurazioni e i failover vengono in genere completati entro 30 secondi. La media è di 8 secondi. Se è già connesso, l'applicazione deve riconnettersi alla nuova replica primaria del database. Se viene tentata una nuova connessione mentre il database è in fase di riconfigurazione prima che la nuova replica primaria sia online, viene ricevuto l'errore 40613 (database non disponibile): *"il database ' {DatabaseName}' nel server ' {ServerName}' non è attualmente disponibile. Ripetere la connessione in un secondo momento. "* Se nel database è presente una query con esecuzione prolungata, la query verrà interrotta durante una riconfigurazione e sarà necessario riavviarla.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Come simulare un evento di manutenzione pianificata

Assicurarsi che l'applicazione client sia resiliente agli eventi di manutenzione prima della distribuzione nell'ambiente di produzione contribuirà a ridurre il rischio di errori dell'applicazione e contribuirà alla disponibilità dell'applicazione per gli utenti finali. È possibile testare il comportamento dell'applicazione client durante gli eventi di manutenzione pianificata [avviando il failover manuale](https://aka.ms/mifailover-techblog) tramite PowerShell, l'interfaccia della riga di comando o l'API REST. Genererà un comportamento identico all'evento di manutenzione che porta la replica primaria offline.

## <a name="retry-logic"></a>Logica di retry

Qualsiasi applicazione di produzione client che si connette a un servizio di database cloud dovrebbe implementare un'efficace [logica di ripetizione dei tentativi](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) di connessione. Ciò consentirà di rendere i failover trasparenti agli utenti finali o almeno ridurre al minimo gli effetti negativi.

## <a name="resource-health"></a>Resource Health

Se nel database si verificano errori di accesso, controllare la finestra di [integrità risorse](../../service-health/resource-health-overview.md#get-started) nel [portale di Azure](https://portal.azure.com) per lo stato corrente. La sezione Cronologia integrità indica il motivo dei tempi di inattività per ogni evento (se disponibile).

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più su [integrità risorse](resource-health-to-troubleshoot-connectivity.md) per il database SQL di azure e istanza gestita SQL di Azure.
- Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [logica di ripetizione dei tentativi per gli errori temporanei](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
