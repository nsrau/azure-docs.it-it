<properties 
	pageTitle="Tenere traccia dell’integrità del servizio" 
	description="Scoprire quando Azure ha subito un calo delle prestazioni o interruzioni del servizio." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# Tenere traccia dell’integrità del servizio

Azure viene pubblicizzato ogni volta che si verifica un’interruzione del servizio o un calo delle prestazioni. È possibile esplorare questi eventi nel portale di Azure e usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per accedere al set completo di eventi a livello di codice.

## Visualizzare l'integrità del servizio per regione

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. In **Home** dovrebbe essere visualizzato un riquadro denominato **Integrità del servizio** ![Home](./media/insights-service-health/Insights_Home.png)

3. Selezionando questo riquadro viene visualizzato un elenco di tutte le aree di Azure. È possibile fare clic su un’area per visualizzare la cronologia di integrità del servizio per tale area. ![Home](./media/insights-service-health/Insights_Regions.png)

4. È inoltre possibile visualizzare i dettagli di un singolo evento imprevisto selezionandolo nella tabella.

## Esplorare i log di integrità del servizio completo

2. Fare clic su **Sfoglia** e selezionare **Log di controllo**. ![Hub di esplorazione](./media/insights-service-health/Insights_Browse.png)

3. Si aprirà un pannello contenente tutti gli eventi che hanno avuto effetto sulle sottoscrizioni negli ultimi 7 giorni. Le voci di integrità del servizio vengono visualizzate nell'elenco, ma potrebbe essere difficile individuarle in quanto l'elenco potrebbe essere di grandi dimensioni.

4. Fare clic sul comando **Filtro**.

5. Selezionare **Categoria di eventi** e scegliere **Integrità del servizio**: ![Tutti gli eventi](./media/insights-service-health/Insights_Filter.png)

6. Fare clic su **Aggiornato**.

7. Verranno ora visualizzati tutti gli eventi di integrità del servizio che hanno influito sulla sottoscrizione: ![Gruppi di risorse](./media/insights-service-health/Insights_HealthEvent.png)

8. Da qui è possibile passare al pannello dei dettagli per vedere le specifiche dell'evento.
   
## Passaggi successivi

* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che si verifica un evento.
* [Monitorare le metriche del servizio](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Monitorare la disponibilità e i tempi di risposta di qualsiasi pagina Web](../app-insights-monitor-web-app-availability.md) con Application Insights per definire se la pagina è inattiva.
 

<!---HONumber=August15_HO6-->