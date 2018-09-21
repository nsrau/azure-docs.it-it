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
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 59b33bcc75890372a7b27b9aca40ad1e22c58281
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364279"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>Integrare i log di Azure AD con Splunk tramite Monitoraggio di Azure (anteprima)

Questo articolo illustra come integrare i log di Azure Active Directory (Azure AD) con Splunk tramite Monitoraggio di Azure. Innanzitutto indirizzare i log a un hub eventi di Azure e quindi integrare l'hub eventi con Splunk.

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, sono necessari:
* Un hub eventi di Azure contenente i log attività di Azure AD. Informazioni su come [trasmettere i log attività a un hub eventi](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Il componente aggiuntivo Monitoraggio di Azure per Splunk. [Scaricare e configurare l'istanza di Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Esercitazione 

1. Aprire l'istanza di Splunk e fare clic su **Data Summary** (Riepilogo dati).

    ![Pulsante per il riepilogo dati](./media/tutorial-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selezionare la scheda **Sourcetypes** e quindi **amal: aadal:audit**

    ![Scheda Sourcetypes nel riepilogo dati](./media/tutorial-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    I log attività di Azure AD vengono visualizzati nella figura seguente:

    ![Log attività](./media/tutorial-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Se non è possibile installare un componente aggiuntivo nell'istanza di Splunk (ad esempio, se si usa un proxy o Splunk Cloud), è possibile inoltrare questi eventi all'agente di raccolta di eventi Splunk HTTP. A tale scopo, usare questa [funzione di Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), che viene attivata dai nuovi messaggi nell'hub eventi. 
>

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema dei log di controllo in Monitoraggio di Azure](reference-azure-monitor-audit-log-schema.md)
* [Interpretare lo schema dei log di accesso in Monitoraggio di Azure](reference-azure-monitor-sign-ins-log-schema.md)
* [Domande frequenti e problemi noti](concept-activity-logs-in-azure-monitor.md#frequently-asked-questions)
