---
title: Panoramica degli avvisi in Microsoft Azure e Monitoraggio di Azure | Microsoft Docs
description: Gli avvisi consentono di monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: c9065c3346c924ea21060c9e7e5b297a3cb26941
ms.lasthandoff: 03/09/2017


---
# <a name="what-are-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi in Microsoft Azure?
Questo articolo descrive cosa sono gli avvisi, quali vantaggi offrono e come iniziare a farne uso. In particolare si applica a Monitoraggio di Azure, ma vengono forniti indicazioni per altri servizi.  

Gli avvisi sono un metodo per monitorare le metriche, gli eventi e i log delle risorse di Azure e ricevere una notifica quando una condizione specificata viene soddisfatta.  

## <a name="alerts-in-different-azure-services"></a>Avvisi in diversi servizi di Azure
Gli avvisi sono disponibili in diversi servizi, tra cui:

* **Application Insights**: abilita gli avvisi per le metriche e i test Web. Vedere [Impostare gli avvisi in Application Insights](../application-insights/app-insights-alerts.md) e [Monitorare la disponibilità e la velocità di risposta dei siti Web](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)**: consente il routing dei log di diagnostica e attività a Log Analytics. Operations Management Suite consente gli avvisi relativi alla metrica, ai log e altri tipi di avviso. Per altre informazioni, vedere [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md).  
* **Monitoraggio di Azure**: abilita gli avvisi basati su valori della metrica e sugli eventi del log attività. È possibile usare l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/dn931943.aspx) per gestire gli avvisi.  Per altre informazioni, vedere [Uso del portale di Azure, di PowerShell o dell'interfaccia della riga di comando per creare avvisi](insights-alerts-portal.md).

## <a name="visual-summary"></a>Riepilogo visivo
Nel diagramma seguente sono riepilogati gli avvisi e le operazioni che è possibile eseguire in particolare in "Monitoraggio di Azure". Altre azioni potrebbero essere disponibili per i servizi elencati in precedenza. Ad esempio, attualmente gli avvisi nei log di diagnostica sono disponibili solo in Log Analytics.

![Avvisi illustrati](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="what-can-trigger-alerts-in-azure-monitor"></a>Cosa possono attivare gli avvisi in Monitoraggio di Azure

È possibile ricevere gli avvisi basati su:

* **Valori della metrica**: l'avviso si attiva quando il valore di una specifica metrica supera una soglia assegnata per eccesso o difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta. Per un elenco in continua crescita delle metriche disponibili supportate dal monitoraggio di Azure, vedere [l'elenco delle metriche supportate in Monitoraggio di Azure](monitoring-supported-metrics.md).
* **Eventi del log attività**: è possibile attivare questo avviso per ogni evento o solo quando si verifica un determinato numero di eventi.

## <a name="what-can-alerts-do"></a>Quali operazioni si possono eseguire con gli avvisi?
È possibile configurare un avviso per eseguire le azioni seguenti:

* Inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o ad altri indirizzi di posta elettronica specificati.
* Richiamare un webhook, che consente di avviare altre azioni di automazione. Tra gli esempi sono incluse le chiamate:
    - Runbook di Automazione di Azure
    - Funzione di Azure
    - App per la logica di Azure
    - un servizio di terze parti


## <a name="next-steps"></a>Passaggi successivi
Ottenere informazioni sulle regole degli avvisi e sulla relativa configurazione usando:

* [Portale di Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaccia della riga di comando (CLI)](insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

