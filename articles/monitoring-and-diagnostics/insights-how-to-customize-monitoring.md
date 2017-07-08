---
title: Panoramica delle metriche in Microsoft Azure | Microsoft Docs
description: Informazioni su come personalizzare i grafici di monitoraggio in Azure.
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c36031eb-4df5-4cd5-9479-311d493a40d2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 5ccb5c0b3c2ffaf8aba855a9dbe3da9691ab149b
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017


---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Panoramica delle metriche in Microsoft Azure
Tutti i servizi di Azure tengono traccia delle metriche chiave che consentono di monitorare l'integrità, le prestazioni, la disponibilità e l'utilizzo dei dispositivi. È possibile visualizzare queste metriche nel portale di Azure e usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) o [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) per accedere al set completo di metriche a livello di codice.

Per alcuni servizi, potrebbe essere necessario attivare la diagnostica per visualizzare le metriche. Per altri, ad esempio le macchine virtuali, si otterrà un set di metriche di base, ma sarà necessario abilitare le metriche ad alta frequenza del set completo. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica](insights-how-to-use-diagnostics.md) .

## <a name="using-monitoring-charts"></a>Uso di grafici di monitoraggio
È possibile rappresentare in un grafico qualsiasi metrica in un periodo di tempo scelto.

1. Nel [portale di Azure](https://portal.azure.com/)fare clic su **Sfoglia**e quindi su una risorsa che si intende monitorare.
2. La sezione **Monitoraggio** include le metriche più importanti per ogni risorsa di Azure. Ad esempio, un'app Web contiene **Richieste ed errori**, mentre una macchina virtuale includerebbe **Percentuale CPU** e **Lettura e scrittura disco**:  ![Sezione Monitoraggio](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Facendo clic su un grafico qualsiasi viene visualizzato il pannello **Metrica** . Nel pannello, oltre al grafico, c'è una tabella che mostra le aggregazioni delle metriche (ad esempio, valore medio, minimo e massimo per l'intervallo di tempo scelto). Sotto si trovano le regole di avviso per la risorsa.
    ![Pannello Metrica](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Per personalizzare le righe visualizzate, fare clic sul pulsante **Modifica** nel grafico o sul comando **Modifica grafico** nel pannello Metrica.
5. Nel pannello Modifica query è possibile effettuare tre tipi di azione:
   
   * Modificare l'intervallo di tempo
   * Passare dal grafico a barre al grafico a linee
   * Scegliere metriche diverse ![Modifica query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Per modificare l'intervallo di tempo è sufficiente scegliere un intervallo diverso (ad esempio **ora precedente**) e fare clic su **Salva** nella parte inferiore del pannello. Si può anche scegliere **Personalizzato**, che consente di scegliere un periodo di tempo qualsiasi nelle ultime due settimane. Ad esempio, è possibile visualizzare le due settimane intere o solo un'ora del giorno precedente. Digitare l'orario diverso nella casella di testo.
    ![Intervallo di tempo personalizzato](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Sotto l'intervallo di tempo è possibile scegliere un numero qualsiasi di metriche da visualizzare nel grafico.
8. Quando si fa clic su Salva, verranno salvate le modifiche per quella specifica risorsa. Ad esempio, se si dispone di due macchine virtuali e si modifica un grafico in una, l'altra non ne risentirà.

## <a name="creating-side-by-side-charts"></a>Creazione di grafici affiancati
Con la personalizzazione avanzata del portale è possibile aggiungere tutti i grafici che si vuole.

1. Nel menu **...** nella parte superiore del pannello fare clic su **Aggiungi riquadri**:  
    ![Menu di aggiunta](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. È quindi possibile selezionare un grafico nella **Raccolta** a destra dello schermo:  ![Raccolta](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Se la metrica desiderata non è visibile, è sempre possibile aggiungere una delle metriche preimpostate e fare cli su **Modifica** per visualizzare nel grafico la metrica necessaria.

## <a name="monitoring-usage-quotas"></a>Monitoraggio delle quote di utilizzo
La maggior parte delle metriche mostrano le tendenze nel tempo, ma alcuni dati, come le quote di utilizzo, sono informazioni temporizzate con una soglia.

È inoltre possibile visualizzare le quote di utilizzo nel pannello per le risorse per cui sono previste quote:

![Utilizzo](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Come per le metriche, è possibile usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) o [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) per accedere a livello di codice al set completo di quote di utilizzo.

## <a name="next-steps"></a>Passaggi successivi
* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che una metrica supera una soglia.
* [Abilitare il monitoraggio e la diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* [Scalare automaticamente il numero di istanze](insights-how-to-scale.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Monitorare le prestazioni dell'applicazione](../application-insights/app-insights-azure-web-apps.md) se si desidera comprendere esattamente il comportamento del codice nel cloud.
* Usare [Application Insights per app JavaScript e pagine Web](../application-insights/app-insights-web-track-usage.md) per ottenere l'analisi client sui browser che visitano una pagina Web.
* [Monitorare la disponibilità e i tempi di risposta di qualsiasi pagina Web](../application-insights/app-insights-monitor-web-app-availability.md) con Application Insights per definire se la pagina è inattiva.


