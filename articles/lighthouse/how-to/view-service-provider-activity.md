---
title: Visualizzare l'attività dei provider di servizi
description: I clienti possono visualizzare le attività registrate per visualizzare le azioni eseguite dai provider di servizi tramite la gestione delle risorse delegate di Azure.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: a6ae5668601e3587b7306ba1652f6b107800fcb3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75527825"
---
# <a name="view-service-provider-activity"></a>Visualizzare l'attività dei provider di servizi

I clienti che hanno sottoscrizioni delegate per la gestione delle risorse delegate di Azure possono visualizzare i dati del [log attività di Azure](../../azure-monitor/platform/activity-logs-overview.md) per visualizzare tutte le azioni eseguite. In questo modo i clienti possono visualizzare in modo completo le operazioni eseguite dai provider di servizi attraverso la gestione delle risorse delegate di Azure, insieme a quelle eseguite dagli utenti all'interno del tenant Azure Active Directory (Azure AD) del cliente.

## <a name="view-activity-log-data"></a>Visualizzare i dati del log attività

È possibile [visualizzare il log attività](../../azure-monitor/platform/activity-log-view.md) dal menu **monitoraggio** nel portale di Azure. Per limitare i risultati a una sottoscrizione specifica, è possibile usare i filtri per selezionare una sottoscrizione specifica. È anche possibile [visualizzare e recuperare gli eventi del log attività](../../azure-monitor/platform/activity-log-view.md) a livello di codice.

> [!NOTE]
> Gli utenti del tenant di un provider di servizi possono visualizzare i risultati del log attività per una sottoscrizione delegata in un tenant del cliente se sono stati concessi il ruolo [lettore](../../role-based-access-control/built-in-roles.md#reader) (o un altro ruolo predefinito che include l'accesso in lettura) quando tale sottoscrizione è stata caricata per la gestione delle risorse delegate di Azure.

Nel log attività verranno visualizzati il nome dell'operazione e il relativo stato, insieme alla data e all'ora in cui è stato eseguito. L' **evento avviato da** colonna indica l'utente che ha eseguito l'operazione, indipendentemente dal fatto che sia un utente del tenant di un provider di servizi che agisce attraverso la gestione delle risorse delegata di Azure o un utente nel tenant del cliente. Si noti che viene visualizzato il nome dell'utente, anziché il tenant o il ruolo assegnato dall'utente per la sottoscrizione.

L'attività registrata è disponibile nel portale di Azure per gli ultimi 90 giorni. Per informazioni su come archiviare i dati per più di 90 giorni, vedere [raccogliere e analizzare i log attività di Azure nell'area di lavoro log Analytics in monitoraggio di Azure](../../azure-monitor/platform/activity-log-collect.md)

## <a name="set-alerts-for-critical-operations"></a>Impostare gli avvisi per le operazioni critiche

Per tenere conto delle operazioni critiche che i provider di servizi (o gli utenti nel tenant) eseguono, è consigliabile creare gli [avvisi del log attività](../../azure-monitor/platform/activity-log-alerts.md). Ad esempio, è possibile tenere traccia di tutte le azioni amministrative per una sottoscrizione o ricevere una notifica quando viene eliminata una macchina virtuale in un determinato gruppo di risorse. Quando si creano gli avvisi, verranno incluse le azioni eseguite dagli utenti nel tenant del cliente, oltre che in qualsiasi tenant di gestione.

Per altre informazioni, vedere [creare e gestire gli avvisi del log attività](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Creazione di query di log

È possibile creare query per analizzare l'attività registrata o concentrarsi su elementi specifici. Ad esempio, per un controllo è necessario creare un report su tutte le azioni a livello di amministrazione eseguite in una sottoscrizione. È possibile creare una query per filtrare solo le azioni e ordinare i risultati in base all'utente, alla data o a un altro valore.

Per altre informazioni, vedere [Panoramica delle query di log in monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Monitoraggio di Azure](../../azure-monitor/index.yml).
- Informazioni su come [visualizzare e gestire le offerte del provider di servizi](view-manage-service-providers.md) nel portale di Azure.