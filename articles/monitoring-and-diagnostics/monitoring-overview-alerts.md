---
title: Panoramica degli avvisi in Microsoft Azure | Microsoft Docs
description: Gli avvisi consentono di monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb

---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Panoramica degli avvisi in Microsoft Azure
Questo articolo descrive cosa sono gli avvisi, quali vantaggi offrono e come iniziare a farne uso.  

## <a name="what-are-alerts?"></a>Cosa sono gli avvisi?
Gli avvisi sono un metodo per monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.

È possibile ricevere gli avvisi basati su:

* **Valori della metrica**: l'avviso si attiva quando il valore di una specifica metrica supera una soglia assegnata per eccesso o difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.
* **Eventi del log attività**: è possibile attivare questo avviso per ogni evento o solo quando si verifica un determinato numero di eventi.

## <a name="alerts-in-different-azure-services"></a>Avvisi in diversi servizi di Azure
Gli avvisi sono disponibili in diversi servizi, tra cui:

* **Application Insights**: abilita gli avvisi per le metriche e i test Web. Vedere [Impostare gli avvisi in Application Insights](../application-insights/app-insights-alerts.md) e [Monitorare la disponibilità e la velocità di risposta dei siti Web](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)**: consente il routing dei log di diagnostica a Log Analytics. Operations Management Suite consente gli avvisi relativi alla metrica, ai log e altri tipi di avviso. Per altre informazioni, vedere [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md).   
* **Monitoraggio di Azure**: abilita gli avvisi basati su valori della metrica e sugli eventi del log attività. Il monitoraggio di Azure include l' [API REST di Azure Insights](https://msdn.microsoft.com/library/dn931943.aspx).  Per altre informazioni, vedere [Uso del portale di Azure, di PowerShell o dell'interfaccia della riga di comando per creare avvisi](insights-alerts-portal.md).

## <a name="alert-actions"></a>Azioni di avviso
È possibile configurare un avviso per eseguire le operazioni seguenti:

* Inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o ad altri indirizzi di posta elettronica specificati.
* Richiamare un webhook, che consente di avviare altre azioni di automazione.
  
  ![Avvisi illustrati](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## <a name="next-steps"></a>Passaggi successivi
Ottenere informazioni sulle regole degli avvisi e sulla relativa configurazione usando:

* [Portale di Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaccia della riga di comando (CLI)](../azure-portal/insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!--HONumber=Oct16_HO2-->


