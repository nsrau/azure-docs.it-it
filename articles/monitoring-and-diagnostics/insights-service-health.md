---
title: "Tenere traccia dell&quot;integrità del servizio di Azure usando i log attività di Monitoraggio di Azure | Microsoft Docs"
description: 'Scoprire quando Azure ha subito un calo delle prestazioni o interruzioni del servizio. '
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Tenere traccia dell'integrità del servizio di Azure usando i log attività di Monitoraggio di Azure
Azure viene pubblicizzato ogni volta che si verifica un’interruzione del servizio o un calo delle prestazioni. È possibile esplorare questi eventi nel portale di Azure e usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per accedere al set completo di eventi a livello di codice.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Esplorare i log di integrità del servizio per la propria sottoscrizione
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. In **Home** dovrebbe essere visualizzato un riquadro denominato **Integrità del servizio**. Fare clic su di esso.
   
    ![Home](./media/insights-service-health/Insights_Home.png)
3. Verrà visualizzato un elenco di tutte le aree di Azure. Fare clic su un'area per visualizzare la query del log attività che mostra gli eventi imprevisti del servizio che hanno interessato le proprie sottoscrizioni nelle ultime 24 ore.
   
    ![Integrità del servizio di sottoscrizione del log attività](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. È possibile visualizzare i dettagli di un singolo evento imprevisto selezionandolo nella tabella.
5. Modificare l'**intervallo di tempo** per visualizzarne uno più esteso.

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare la disponibilità e i tempi di risposta di qualsiasi pagina Web](../application-insights/app-insights-monitor-web-app-availability.md) con Application Insights per definire se la pagina è inattiva.




<!--HONumber=Nov16_HO3-->


