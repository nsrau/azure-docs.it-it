<properties 
	pageTitle="Monitorare le metriche del servizio" 
	description="Informazioni su come personalizzare i grafici di monitoraggio in Azure." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal"
documentationCenter=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="stepsic"/>

# Monitorare le metriche del servizio

Tutti i servizi di Azure tengono traccia delle metriche chiave che consentono di monitorare l'integrità, le prestazioni, la disponibilità e l'utilizzo dei dispositivi. È possibile visualizzare queste metriche nel portale di Azure e usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per accedere al set completo di metriche a livello di codice.

Per alcuni servizi, potrebbe essere necessario attivare la diagnostica per visualizzare le metriche. Per altri, ad esempio le macchine virtuali, si otterrà un set di metriche di base, ma sarà necessario abilitare le metriche ad alta frequenza del set completo. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica](insights-how-to-use-diagnostics.md).

## Uso di grafici di monitoraggio 

È possibile rappresentare in un grafico qualsiasi metrica in un periodo di tempo scelto.

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Sfoglia** e quindi su una risorsa che si intende monitorare.

2. La sezione **Monitoraggio** include le metriche più importanti per ogni risorsa di Azure. Ad esempio, un'app Web include **Richieste ed errori**, mentre una macchina virtuale includerebbe **Percentuale CPU** e **Lettura e scrittura disco**: ![Filtro di monitoraggio](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Facendo clic su un grafico qualsiasi viene visualizzato il pannello **Metrica**. Nel pannello, oltre al grafico, c'è una tabella che mostra le aggregazioni delle metriche (ad esempio, valore medio, minimo e massimo per l'intervallo di tempo scelto). Sotto si trovano le regole di avviso per la risorsa. ![Blade delle metriche](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Per personalizzare le righe visualizzate, fare clic sul pulsante **Modifica** nel grafico o sul comando **Modifica grafico** nel pannello Metrica.

5. Nel pannello Modifica query è possibile effettuare tre tipi di azione:
    - Modificare l'intervallo di tempo
    - Passare dal grafico a barre al grafico a linee
    - Scegliere metriche diverse ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Per apportare cambiamenti all'intervallo di tempo è sufficiente scegliere un intervallo diverso (come **Past Hour**) e fare clic su **Save** nella parte inferiore del blade. Si può anche scegliere **Personalizzato**, che consente di scegliere un periodo di tempo qualsiasi nelle ultime due settimane. Ad esempio, è possibile visualizzare le due settimane intere o solo un'ora del giorno precedente. Digitare l'orario diverso nella casella di testo. ![Intervallo di tempo personalizzato](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Sotto l'intervallo di tempo è possibile scegliere un numero qualsiasi di metriche da visualizzare nel grafico.

8. Quando si fa clic su Salva, verranno salvate le modifiche per quella specifica risorsa. Ad esempio, se si dispone di due macchine virtuali e si modifica un grafico in una, l'altra non ne risentirà.

## Creazione di grafici affiancati

Con la personalizzazione avanzata del portale è possibile aggiungere tutti i grafici che si vuole.

1. Nel menu **...** nella parte superiore del pannello fare clic su **Aggiungi riquadri**: ![Menu Aggiungi](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Quindi è possibile selezionare un grafico nella **Raccolta** a destra dello schermo: ![Raccolta](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Se la metrica desiderata non è visibile, è sempre possibile aggiungere una delle metriche preimpostate e fare cli su **Modifica** per visualizzare nel grafico la metrica necessaria. 

## Monitoraggio delle quote di utilizzo

La maggior parte delle metriche mostrano le tendenze nel tempo, ma alcuni dati, come le quote di utilizzo, sono informazioni temporizzate con una soglia.

È inoltre possibile visualizzare le quote di utilizzo nel pannello per le risorse per cui sono previste quote:

![Utilizzo](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Come per le metriche, è possibile usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per accedere a livello di codice al set completo di quote di utilizzo.

## Passaggi successivi

* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che una metrica supera una soglia.
* [Abilitare il monitoraggio e la diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* [Scalare automaticamente il numero di istanze](insights-how-to-scale.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Monitorare le prestazioni dell'applicazione](insights-perf-analytics.md) se si desidera comprendere esattamente il comportamento del codice nel cloud.
* Usare [Application Insights per app JavaScript e pagine Web](../app-insights-web-track-usage.md) per ottenere l'analisi client sui browser che visitano una pagina Web.
* [Monitorare la disponibilità e i tempi di risposta di qualsiasi pagina Web](../app-insights-monitor-web-app-availability.md) con Application Insights per definire se la pagina è inattiva.
 

<!---HONumber=Sept15_HO2-->