<properties
	pageTitle="Panoramica degli avvisi in Microsoft Azure | Microsoft Azure"
	description="Gli avvisi consentono di monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>  

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/24/2016"
	ms.author="robb"/>  

# Panoramica degli avvisi in Microsoft Azure 


Questo articolo descrive cosa sono gli avvisi, quali vantaggi offrono e come iniziare a farne uso.

## Cosa sono gli avvisi?
Gli avvisi sono un metodo per monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.

È possibile ricevere gli avvisi basati su:
    
- Valori metrici: l'avviso si attiva quando il valore di una specifica metrica supera una soglia assegnata per eccesso o difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.
- Eventi del log attività: è possibile attivare un avviso per ogni evento o solo quando si verifica un determinato numero di eventi.

## Avvisi in diversi servizi di Azure
 
Gli avvisi sono disponibili in diversi servizi

- **Application Insights**: consente di generare avvisi per le metriche e i test Web. Vedere [Impostare gli avvisi in Application Insights](../application-insights/app-insights-alerts.md) e [Monitorare la disponibilità e la velocità di risposta dei siti Web](../application-insights/app-insights-monitor-web-app-availability.md).
- **Log Analytics di OMS**: è possibile inviare i log di diagnostica Log Analytics. OMS consente gli avvisi per le metriche, i log e altri tipi di avviso. Per altre informazioni, vedere [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md).
- **Monitoraggio di Azure**: il monitoraggio di Azure consente gli avvisi basati su valori metrici e sugli eventi del log attività. Il monitoraggio di Azure include l'[API REST di Azure Insights](https://msdn.microsoft.com/library/dn931943.aspx). Vedere anche [Uso del portale di Azure, di PowerShell o dell'interfaccia della riga di comando per creare avvisi](insights-alerts-portal.md)

## Azioni di avviso
È possibile configurare un avviso per eseguire le operazioni seguenti quando si attiva:

- inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o altri messaggi di posta elettronica specificati;
- richiamare un webhook, che consente di avviare altre azioni di automazione

 ![Avvisi illustrati.](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)  


## Passaggi successivi

Informazioni sulle regole degli avvisi e sulla loro configurazione usando

- [Il portale di Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Interfaccia della riga di comando (CLI)](insights-alerts-command-line-interface.md)
- [API REST di Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!---HONumber=AcomDC_0928_2016-->