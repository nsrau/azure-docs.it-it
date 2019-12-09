---
title: Visualizza attività del provider di servizi
description: I clienti possono visualizzare le attività registrate per visualizzare le azioni eseguite dai provider di servizi tramite la gestione delle risorse delegate di Azure.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 69517e942aa9f82be16fa3d0e7d6f9252de44d4c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932684"
---
# <a name="view-service-provider-activity"></a>Visualizza attività del provider di servizi

I clienti che hanno sottoscrizioni delegate per la gestione delle risorse delegate di Azure possono visualizzare i dati del [log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) per visualizzare tutte le azioni eseguite. In questo modo i clienti possono visualizzare in modo completo le operazioni eseguite dai provider di servizi attraverso la gestione delle risorse delegate di Azure, insieme a quelle eseguite dagli utenti all'interno del tenant Azure Active Directory (Azure AD) del cliente.

## <a name="view-activity-log-data"></a>Visualizzare i dati del log attività

È possibile [visualizzare il log attività](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview#view-the-activity-log) dal menu **monitoraggio** nel portale di Azure. Per limitare i risultati a una sottoscrizione specifica, è possibile usare i filtri per selezionare una sottoscrizione specifica. È anche possibile [visualizzare e recuperare gli eventi del log attività](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) a livello di codice.

> [!NOTE]
> Gli utenti del tenant di un provider di servizi possono visualizzare i risultati del log attività per una sottoscrizione delegata in un tenant del cliente se sono stati concessi il ruolo [lettore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (o un altro ruolo predefinito che include l'accesso in lettura) quando tale sottoscrizione è stata caricata per la gestione delle risorse delegate di Azure.

Nel log attività verranno visualizzati il nome dell'operazione e il relativo stato, insieme alla data e all'ora in cui è stato eseguito. L' **evento avviato da** colonna indica l'utente che ha eseguito l'operazione, indipendentemente dal fatto che sia un utente del tenant di un provider di servizi che agisce attraverso la gestione delle risorse delegata di Azure o un utente nel tenant del cliente. Si noti che viene visualizzato il nome dell'utente, anziché il tenant o il ruolo assegnato dall'utente per la sottoscrizione.

L'attività registrata è disponibile nel portale di Azure per gli ultimi 90 giorni. Per informazioni su come archiviare i dati per più di 90 giorni, vedere [raccogliere e analizzare i log attività di Azure nell'area di lavoro log Analytics in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

## <a name="set-alerts-for-critical-operations"></a>Impostare gli avvisi per le operazioni critiche

Per tenere conto delle operazioni critiche che i provider di servizi (o gli utenti nel tenant) eseguono, è consigliabile creare gli [avvisi del log attività](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). Ad esempio, è possibile tenere traccia di tutte le azioni amministrative per una sottoscrizione o ricevere una notifica quando viene eliminata una macchina virtuale in un determinato gruppo di risorse. Quando si creano gli avvisi, verranno incluse le azioni eseguite dagli utenti nel tenant del cliente, oltre che in qualsiasi tenant di gestione.

Per altre informazioni, vedere [creare e gestire gli avvisi del log attività](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

## <a name="create-log-queries"></a>Creazione di query di log

È possibile creare query per analizzare l'attività registrata o concentrarsi su elementi specifici. Ad esempio, per un controllo è necessario creare un report su tutte le azioni a livello di amministrazione eseguite in una sottoscrizione. È possibile creare una query per filtrare solo le azioni e ordinare i risultati in base all'utente, alla data o a un altro valore.

Per altre informazioni, vedere [Panoramica delle query di log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/).
- Informazioni su come [visualizzare e gestire le offerte del provider di servizi](view-manage-service-providers.md) nel portale di Azure.