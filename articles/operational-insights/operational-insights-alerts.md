<properties
   pageTitle="Visualizzare avvisi da Operations Manager"
   description="Informazioni sulla gestione degli avvisi da Operations Manager per i server monitorati nella propria infrastruttura"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2015"
   ms.author="banders" />



# Visualizzare gli avvisi di Operations Manager

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Prima di poter usare Alert Management in Microsoft Azure Operational Insights, è necessario disporre della soluzione installata. Per altre informazioni sull'installazione di soluzioni, vedere [Usare la raccolta di soluzioni per aggiungere o rimuovere soluzioni](operational-insights-add-solution.md). La soluzione funziona solo quando si usano server monitorati dagli agenti di Operations Manager. Per altre informazioni sull'uso di Operations Manager con Operational Insights, vedere [Connettersi a Operational Insights da System Center Operations Manager](operational-insights-connect-scom.md).

Dopo l'installazione della soluzione, è possibile visualizzare gli avvisi per i server monitorati usando il riquadro **Gestione avvisi** del dashboard **Panoramica** in Operational Insights.

![image of Alert Management tile](./media/operational-insights-alerts/overview-alert.png)


È possibile visualizzare e gestire gli avvisi di Microsoft Azure Operational Insights dal dashboard **Avvisi**. Nel dashboard vengono visualizzate le informazioni nelle categorie seguenti:

- Active Alerts
	- Critical Alerts
	- Warning Alerts
	- Alert Sources
- All Active Alerts
	- Mostra la percentuale di avvisi critici, informativi e di avvertenze.
- Common Alert Queries
	- Quest'area contiene query predefinite che il team di sviluppo del software Operational Insights ha creato per aiutare l'utente a iniziare a usare gli avvisi.


Gli avvisi indicano che è stato rilevato un problema, il server interessato dall'avviso, la priorità e il nome dell'avviso.

![image of Alert dashboard](./media/operational-insights-alerts/alert-drilldown1.png)

![image of Alert dashboard](./media/operational-insights-alerts/alert-drilldown2.png)


Nel dashboard **Alert Management** è possibile visualizzare tutti gli avvisi trovati da Microsoft Azure Operational Insights.

## Per visualizzare gli avvisi di Operational Insights

1. Nella pagina **Overview** fare clic sul riquadro **Alert Management**.
2. Nel dashboard **Alert Management** visualizzare le categorie di avviso e scegliere quella da usare.
3. Fare clic su un riquadro o qualsiasi elemento per visualizzarne le informazioni dettagliate nella pagina **Search**.
4. Usando le informazioni trovate, è possibile esaminare l'avviso e determinare le azioni aggiuntive che potrebbe essere necessario intraprendere per risolvere il problema.

[AZURE.INCLUDE [esportazione di operational insights](../../includes/operational-insights-export.md)]

<!---HONumber=July15_HO4-->