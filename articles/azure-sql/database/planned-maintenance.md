---
title: Pianificazione degli eventi di manutenzione di Azure
description: Informazioni su come prepararsi per gli eventi di manutenzione pianificata nel database SQL di Azure e in Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 5bdc3eb8c118c19f90ce1fd92ac5ee156719dacd
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987209"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Pianificare gli eventi di manutenzione di Azure nel database SQL di Azure e in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Informazioni su come prepararsi per gli eventi di manutenzione pianificata nel database nel database SQL di Azure e in Azure SQL Istanza gestita.

## <a name="what-is-a-planned-maintenance-event"></a>Che cos'è un evento di manutenzione pianificata?

Per ogni database, il database SQL di Azure e Azure SQL Istanza gestita gestiscono un quorum di repliche di database in cui una replica è la replica primaria. In ogni momento, una replica primaria deve essere la manutenzione in linea e almeno una replica secondaria deve essere integra. Durante la manutenzione pianificata, i membri del quorum del database passeranno offline uno alla volta, in quanto sono presenti una replica primaria pronta a rispondere e almeno una replica secondaria online per evitare tempi di inattività del client. Quando la replica primaria deve essere portata offline, si verifica un processo di riconfigurazione/failover in cui una replica secondaria diventa la nuova replica primaria.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Esecuzione di un evento di manutenzione pianificata

Le riconfigurazioni e i failover vengono in genere completati entro 30 secondi. La media è di 8 secondi. Se è già connessa, l'applicazione deve riconnettersi alla copia integra della nuova replica primaria del database. Se viene tentata una nuova connessione mentre il database è in fase di riconfigurazione prima che la nuova replica primaria sia online, viene ricevuto l'errore 40613 (database non disponibile): "il database ' {DatabaseName}' nel server ' {ServerName}' non è attualmente disponibile. Eseguire nuovamente la connessione in un secondo momento. Se nel database è presente una query con esecuzione prolungata, la query verrà interrotta durante una riconfigurazione e sarà necessario riavviarla.

## <a name="retry-logic"></a>Logica di retry

Qualsiasi applicazione di produzione client che si connette a un servizio di database cloud dovrebbe implementare un'efficace [logica di ripetizione dei tentativi](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) di connessione. Ciò consente di ridurre queste situazioni e in genere dovrebbe rendere gli errori trasparenti per l'utente finale.

## <a name="frequency"></a>Frequenza

In media si verificano 1,7 eventi di manutenzione pianificata al mese.

## <a name="resource-health"></a>Resource Health

Se nel database si verificano errori di accesso, controllare la finestra di [integrità risorse](../../service-health/resource-health-overview.md#get-started) nel [portale di Azure](https://portal.azure.com) per lo stato corrente. La sezione Cronologia integrità indica il motivo dei tempi di inattività per ogni evento (se disponibile).

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più su [integrità risorse](resource-health-to-troubleshoot-connectivity.md) per il database SQL di azure e istanza gestita SQL di Azure.
- Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [logica di ripetizione dei tentativi per gli errori temporanei](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
