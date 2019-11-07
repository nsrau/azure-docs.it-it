---
title: Pianificazione per gli eventi di manutenzione di Azure-database SQL di Azure
description: Informazioni sulle attività preliminari per gli eventi di manutenzione pianificata per il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 5598d5a465645ce20d9244011de6d9cef47ac3c4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687623"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Pianificazione di eventi di manutenzione di Azure nel database SQL di Azure

Informazioni sulle attività preliminari per gli eventi di manutenzione pianificata per il database SQL di Azure.

## <a name="what-is-a-planned-maintenance-event"></a>Descrizione di un evento di manutenzione pianificata

Per ogni database, il database SQL di Azure gestisce un quorum di repliche di database in cui è presente una replica primaria. In qualsiasi momento, una replica primaria deve essere online e almeno una replica secondaria deve essere integra. Durante la manutenzione pianificata, i membri del quorum del database passeranno offline uno alla volta, in quanto sono presenti una replica primaria pronta a rispondere e almeno una replica secondaria online per evitare tempi di inattività del client. Quando la replica primaria deve essere portata offline, si verifica un processo di riconfigurazione/failover in cui una replica secondaria diventa la nuova replica primaria.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Esecuzione di un evento di manutenzione pianificata

Le riconfigurazioni/failover terminano in genere entro 30 secondi, la media è di 8 secondi. Se è già connessa, l'applicazione deve riconnettersi alla copia integra della nuova replica primaria del database. Se viene tentata una nuova connessione mentre il database è in fase di riconfigurazione prima che la nuova replica primaria sia online, viene ricevuto l'errore 40613 (database non disponibile): "il database ' {DatabaseName}' nel server ' {ServerName}' non è attualmente disponibile. Eseguire nuovamente la connessione in un secondo momento." Se il database ha una query con esecuzione prolungata, questa verrà interrotta durante una riconfigurazione e dovrà essere riavviata.

## <a name="retry-logic"></a>Logica di ripetizione dei tentativi

Qualsiasi applicazione di produzione client che si connette a un servizio di database cloud dovrebbe implementare un'efficace [logica di ripetizione dei tentativi](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) di connessione. Ciò consente di ridurre queste situazioni e in genere dovrebbe rendere gli errori trasparenti per l'utente finale.

## <a name="frequency"></a>Frequenza

In media si verificano 1,7 eventi di manutenzione pianificata al mese.

## <a name="resource-health"></a>Integrità delle risorse

Se nel database SQL si verificano errori di accesso, vedere la finestra [Integrità risorsa](../service-health/resource-health-overview.md#get-started) nel [portale di Azure](https://portal.azure.com) per lo stato corrente. La sezione Cronologia integrità indica il motivo dei tempi di inattività per ogni evento (se disponibile).


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Integrità risorsa](sql-database-resource-health.md) per il database SQL
- Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [Logica di ripetizione dei tentativi per errori temporanei](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
