---
title: Integrare Splunk con monitoraggio di Azure | Microsoft Docs
description: Informazioni su come integrare Azure Active Directory log con Splunk usando monitoraggio di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 351669453a5ce6930d3eb912e95e530d14febf61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335851"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Procedura: integrare log di Azure Active Directory con Splunk usando monitoraggio di Azure

Questo articolo illustra come integrare i log di Azure Active Directory (Azure AD) con Splunk tramite Monitoraggio di Azure. Innanzitutto indirizzare i log a un hub eventi di Azure e quindi integrare l'hub eventi con Splunk.

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, sono necessari:

- Un hub eventi di Azure contenente i log attività di Azure AD. Informazioni su come [trasmettere i log attività a un hub eventi](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 

-  Il [Microsoft Azure aggiungere per Splunk](https://splunkbase.splunk.com/app/3757/). 

## <a name="integrate-azure-active-directory-logs"></a>Integrare i log di Azure Active Directory 

1. Aprire l'istanza di Splunk e fare clic su **Data Summary** (Riepilogo dati).

    ![Pulsante per il riepilogo dati](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selezionare la scheda **Sourcetypes** e quindi **amal: aadal:audit**

    ![Scheda Sourcetypes nel riepilogo dati](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    I log attività di Azure AD vengono visualizzati nella figura seguente:

    ![Log attività](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Se non è possibile installare un componente aggiuntivo nell'istanza di Splunk (ad esempio, se si usa un proxy o Splunk Cloud), è possibile inoltrare questi eventi all'agente di raccolta di eventi Splunk HTTP. A tale scopo, usare questa [funzione di Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), che viene attivata dai nuovi messaggi nell'hub eventi. 
>

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema dei log di controllo in Monitoraggio di Azure](reference-azure-monitor-audit-log-schema.md)
* [Interpretare lo schema dei log di accesso in Monitoraggio di Azure](reference-azure-monitor-sign-ins-log-schema.md)
* [Domande frequenti e problemi noti](concept-activity-logs-azure-monitor.md#frequently-asked-questions)