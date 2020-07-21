---
title: Modifica della notifica di rilevamento intelligente-applicazione Azure Insights
description: Modificare i destinatari di notifiche predefiniti dal rilevamento intelligente. Il rilevamento intelligente consente di monitorare le tracce dell'applicazione con applicazione Azure Insights per modelli insoliti nella telemetria delle tracce.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 41298be1b0288f429647d8b38a1cc75dd2c40b0a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516754"
---
# <a name="smart-detection-e-mail-notification-change"></a>Modifica delle notifiche tramite posta elettronica della funzionalità di rilevamento intelligente

In base al feedback dei clienti, il 1° aprile 2019 verranno modificati i ruoli predefiniti che ricevono le notifiche tramite posta elettronica dalla funzionalità di rilevamento intelligente.

## <a name="what-is-changing"></a>Cosa cambierà

Attualmente, le notifiche tramite posta elettronica della funzionalità di rilevamento intelligente vengono inviate per impostazione predefinita ai ruoli di _proprietario della sottoscrizione_ e _collaboratore della sottoscrizione_ e al _ruolo con autorizzazioni di lettura per la sottoscrizione_. Questi ruoli includono spesso utenti che non sono attivamente coinvolti nel monitoraggio e per questo molti utenti ricevono notifiche non necessarie. Per migliorare questa esperienza, verrà apportata una modifica in modo che le notifiche tramite posta elettronica vengano inviate per impostazione predefinita solo al [ruolo con autorizzazioni di lettura dei dati di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) e al ruolo di [collaboratore per il monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor).

## <a name="scope-of-this-change"></a>Ambito della modifica

Questa modifica interesserà tutte le regole di rilevamento intelligente, ad eccezione delle seguenti:

* Regole di rilevamento intelligente contrassegnate come anteprima. Queste regole di rilevamento intelligente attualmente non supportano le notifiche tramite posta elettronica.

* Regola Anomalie errori. Questa regola inizierà a usare come destinazione i nuovi ruoli predefiniti una volta completata la migrazione da avviso classico alla piattaforma di avvisi unificati (per altre informazioni, vedere[qui](../platform/monitoring-classic-retirement.md)).

## <a name="how-to-prepare-for-this-change"></a>Come è possibile prepararsi per questa modifica?

Per assicurarsi che le notifiche tramite posta elettronica dal rilevamento intelligente vengano inviate agli utenti rilevanti, questi utenti devono essere assegnati ai ruoli di [lettore monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) o [collaboratore monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor) della sottoscrizione.

Per assegnare gli utenti al ruolo con autorizzazioni di lettura dei dati di monitoraggio o al ruolo di collaboratore per il monitoraggio tramite il portale di Azure, seguire i passaggi descritti nell'articolo [Aggiungere un'assegnazione di ruolo](../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment). Assicurarsi di selezionare _Ruolo con autorizzazioni di lettura dei dati di monitoraggio_ o _Collaboratore per il monitoraggio_ come ruolo a cui sono assegnati gli utenti.

> [!NOTE]
> I destinatari specifici delle notifiche della funzionalità di rilevamento intelligente configurati usando l'opzione _Altri destinatari di posta elettronica_ nelle impostazioni delle regole non saranno interessati da questa modifica. Questi destinatari continueranno a ricevere le notifiche tramite posta elettronica.

Per domande o dubbi in merito a questa modifica, [contattare il supporto](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul rilevamento intelligente:

- [Anomalie degli errori](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Perdite di memoria](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie delle prestazioni](../../azure-monitor/app/proactive-performance-diagnostics.md)
