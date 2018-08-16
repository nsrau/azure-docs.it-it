---
title: Come integrare i log di Azure Active Directory con Splunk tramite Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Informazioni su come integrare i log di Azure Active Directory con Splunk tramite Monitoraggio di Azure (anteprima)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 42dbb8c7e74bd3acb99028477f34f99f1334d577
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503843"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>Integrare i log di Azure AD con Splunk tramite Monitoraggio di Azure (anteprima)

Questo articolo illustra come integrare i log di Azure Active Directory (Azure AD) con Splunk tramite Monitoraggio di Azure. Innanzitutto indirizzare i log a un hub eventi di Azure e quindi integrare l'hub eventi con Splunk.

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, sono necessari:
* Un hub eventi di Azure contenente i log attività di Azure AD. Informazioni su come [trasmettere i log attività a un hub eventi](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
* Il componente aggiuntivo Monitoraggio di Azure per Splunk. [Scaricare e configurare l'istanza di Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Esercitazione 

1. Aprire l'istanza di Splunk e fare clic su **Data Summary** (Riepilogo dati).

    ![Pulsante per il riepilogo dati](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png)

2. Selezionare la scheda **Sourcetypes** e quindi **amal: aadal:audit**

    ![Scheda Sourcetypes nel riepilogo dati](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png)

    I log attività di Azure AD vengono visualizzati nella figura seguente:

    ![Log attività](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png)

> [!NOTE]
> Se non è possibile installare un componente aggiuntivo nell'istanza di Splunk (ad esempio, se si usa un proxy o Splunk Cloud), è possibile inoltrare questi eventi all'agente di raccolta di eventi Splunk HTTP. A tale scopo, usare questa [funzione di Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), che viene attivata dai nuovi messaggi nell'hub eventi. 
>

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema del log di controllo in Monitoraggio di Azure](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretare lo schema dei log di accesso in Monitoraggio di Azure](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Domande frequenti e problemi noti](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
