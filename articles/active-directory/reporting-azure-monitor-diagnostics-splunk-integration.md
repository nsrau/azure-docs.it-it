---
title: Come integrare i log di Azure Active Directory con Splunk usando Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Informazioni su come integrare i log di Azure Active Directory con Splunk usando Monitoraggio di Azure (anteprima)
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
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240448"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Integrazione dei log di Azure Active Directory con Splunk usando Monitoraggio di Azure (anteprima)

Questo articolo illustra come integrare i log di Azure Active Directory con Splunk usando Monitoraggio di Azure (anteprima). In primo luogo, è necessario indirizzare i log a un hub eventi di Azure e quindi integrarlo con Splunk.

## <a name="prerequisites"></a>Prerequisiti

1. Hub eventi di Azure contenente i log attività di Azure AD. Informazioni su come [trasmettere i log attività a Hub eventi](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
2. Usare il seguente comando [istruzioni](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) per scaricare il componente aggiuntivo Monitoraggio di Azure per Splunk e configurare l'istanza di Splunk.

## <a name="tutorial"></a>Esercitazione 

1. Aprire l'istanza di Splunk e fare clic su **Riepilogo dati**.
    ![Riepilogo dati](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "Riepilogo dati")

2. Passare alla scheda **Sourcetypes** e selezionare **amal: aadal:audit** ![scheda Sourcetypes](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "scheda Sourcetypes")

3. Verranno visualizzati i log attività di Azure AD come illustrato nella figura seguente.
    ![Log attività](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "Log attività")

> [!NOTE]
> Se non è possibile installare un componente aggiuntivo sull'istanza Splunk (ad esempio, se si usa un proxy o Splunk Cloud), è possibile inoltrare questi eventi all'agente di raccolta di eventi Splunk HTTP tramite questa [funzione di Azure, che viene attivata da nuovi messaggi nell'hub eventi](https://github.com/Microsoft/AzureFunctionforSplunkVS)." 
>

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema del log di controllo in Monitoraggio di Azure](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretare lo schema dei log di accesso in Monitoraggio di Azure](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Domande frequenti e problemi noti](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)