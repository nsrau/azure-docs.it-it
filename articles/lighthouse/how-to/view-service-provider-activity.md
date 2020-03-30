---
title: Visualizzare l'attività dei provider di servizi
description: I clienti possono visualizzare l'attività registrata per visualizzare le azioni eseguite dai provider di servizi tramite la gestione delle risorse delegate di Azure.Customers can view logged activity to see actions performed by service providers through Azure delegated resource management.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649637"
---
# <a name="view-service-provider-activity"></a>Visualizzare l'attività dei provider di servizi

I clienti che hanno delegato le sottoscrizioni per la gestione delle risorse delegate di Azure possono visualizzare i dati del [log attività](../../azure-monitor/platform/platform-logs-overview.md) di Azure per visualizzare tutte le azioni eseguite. In questo modo i clienti hanno una visibilità completa sulle operazioni eseguite dai provider di servizi tramite la gestione delle risorse delegate di Azure, insieme alle operazioni eseguite dagli utenti all'interno del tenant di Azure Active Directory (Azure AD) del cliente.

> [!TIP]
> Viene inoltre fornita una definizione dei criteri predefinita di Criteri di Azure per controllare la delega degli ambiti a un tenant di gestione. Per ulteriori informazioni, vedere [Controllare le deleghe nell'ambiente.](view-manage-service-providers.md#audit-delegations-in-your-environment)

## <a name="view-activity-log-data"></a>Visualizzare i dati del log attività

È possibile [visualizzare il log attività](../../azure-monitor/platform/activity-log-view.md) dal menu Monitoraggio nel portale di Azure.You can view the activity log from the **Monitor** menu in the Azure portal. Per limitare i risultati a una sottoscrizione specifica, usare i filtri per selezionare una sottoscrizione specifica. È inoltre possibile [visualizzare e recuperare](../../azure-monitor/platform/activity-log-view.md) gli eventi del log attività a livello di codice.

> [!NOTE]
> Gli utenti nel tenant di un provider di servizi possono visualizzare i risultati del log attività per una sottoscrizione delegata in un tenant del cliente se gli è stato concesso il ruolo Lettore (o un altro ruolo predefinito che include l'accesso in lettura) quando la sottoscrizione è stata integrata per la gestione delle risorse delegate di Azure.Users in a service provider's tenant can view activity log results for a delegated subscription in a customer tenant if they were granted the [Reader](../../role-based-access-control/built-in-roles.md#reader) role (or another built-in role that includes Reader access) when that subscription was onboarded for Azure delegated resource management.

Nel registro attività vengono visualizzati il nome dell'operazione e il relativo stato, insieme alla data e all'ora in cui è stata eseguita. La colonna **Evento avviato da** mostra quale utente ha eseguito l'operazione, se si trattava di un utente nel tenant di un provider di servizi che agisce tramite la gestione delle risorse delegate di Azure o di un utente nel tenant del cliente. Si noti che viene visualizzato il nome dell'utente, anziché il tenant o il ruolo assegnato all'utente per la sottoscrizione.

L'attività registrata è disponibile nel portale di Azure negli ultimi 90 giorni. Per informazioni su come archiviare questi dati per più di 90 giorni, vedere Raccogliere e analizzare i log attività di [Azure nell'area](../../azure-monitor/platform/activity-log-collect.md)di lavoro di Log Analytics.

> [!NOTE]
> Gli utenti del provider di servizi vengono visualizzati nel registro attività, ma questi utenti e le relative assegnazioni di ruolo non vengono visualizzati in **Controllo di accesso (IAM)** o quando recuperano informazioni sulle assegnazioni di ruolo tramite le API.

## <a name="set-alerts-for-critical-operations"></a>Impostare avvisi per le operazioni critiche

Per conoscere le operazioni critiche eseguite dai provider di servizi (o dagli utenti nel proprio tenant), è consigliabile creare [avvisi del log](../../azure-monitor/platform/activity-log-alerts.md)attività. Ad esempio, è possibile tenere traccia di tutte le azioni amministrative per una sottoscrizione o ricevere una notifica quando viene eliminata una macchina virtuale in un determinato gruppo di risorse. Quando si creano avvisi, queste includeranno le azioni eseguite dagli utenti nel tenant del cliente e in qualsiasi tenant di gestione.

Per ulteriori informazioni, vedere [Creare e gestire avvisi del log attività](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Creare query di log

È possibile creare query per analizzare l'attività registrata o concentrarsi su elementi specifici. Ad esempio, è possibile che un controllo richieda la creazione di report su tutte le azioni a livello amministrativo eseguite in una sottoscrizione. È possibile creare una query per filtrare solo queste azioni e ordinare i risultati in base all'utente, alla data o a un altro valore.

Per altre informazioni, vedere [Panoramica delle query di log in Monitoraggio di Azure.For](../../azure-monitor/log-query/log-query-overview.md)more information, see Overview of log queries in Azure Monitor .

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su Monitoraggio di Azure .Learn [.Learn more](../../azure-monitor/index.yml)about Azure Monitor .
- Informazioni su come visualizzare e gestire le offerte dei provider di servizi nel portale di Azure.Learn how to view and [manage service provider offers](view-manage-service-providers.md) in the Azure portal.