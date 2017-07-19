---
title: "Introduzione alla scalabilità automatica in base a una metrica personalizzata in Azure | Microsoft Docs"
description: Informazioni su come ridimensionare la risorsa in base a una metrica personalizzata in Azure.
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: rajram
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 428cfb29fe5d436073744a369da8ca1c2784044b
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017


---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Introduzione alla scalabilità automatica in base a una metrica personalizzata in Azure
Questo articolo descrive come ridimensionare la risorsa in base a una metrica personalizzata nel portale di Azure.

La scalabilità automatica di Monitoraggio di Azure si applica solo a set di scalabilità di macchine virtuali (VMSS), servizi cloud, piani di servizio app e ambienti di servizio app. 

# <a name="lets-get-started"></a>Introduzione
In questo articolo si presuppone che l'utente abbia un'app Web con Application Insights configurato. Se non è già stato fatto, è possibile [configurare Application Insights per il sito Web ASP.NET][1].

- Aprire il [portale di Azure][2].
- Fare clic sull'icona di Monitoraggio di Azure nel riquadro di spostamento a sinistra.
  ![Avviare Monitoraggio di Azure][3]
- Fare clic sull'impostazione Scalabilità automatica per visualizzare tutte le risorse per le quali è applicabile la scalabilità automatica, insieme allo stato corrente ![Individuare l'impostazione Scalabilità automatica in Monitoraggio di Azure][4]
- Aprire il pannello 'Scalabilità automatica' in Monitoraggio di Azure e selezionare una risorsa da ridimensionare
> Nota: la procedura seguente usa un piano di servizio app associato a un'app Web con Application Insights configurato.
- Nel pannello delle impostazioni di scalabilità automatica per la risorsa si noti che il numero di istanze corrente è 1. Fare clic su 'Abilita scalabilità automatica'.
  ![Impostazione di scalabilità per la nuova app Web][5]
- Specificare un nome per l'impostazione di scalabilità, quindi scegliere "Add a rule" (Aggiungi una regola). Si notino le opzioni per le regole di scalabilità visualizzate come riquadro contesto sul lato destro. Per impostazione predefinita viene applicata l'opzione per aumentare il numero di istanze di 1 se la percentuale CPU della risorsa supera il 70%. Impostare l'origine della metrica nella parte superiore su "Application Insights", selezionare la risorsa Application Insights nell'elenco a discesa 'Risorsa' e quindi selezionare la metrica personalizzata in base a ciò che si vuole ridimensionare.
  ![Ridimensionare in base a una metrica personalizzata][6]
- Analogamente al passaggio precedente, aggiungere una regola di scalabilità che ridurrà il numero di istanze di 1 se la metrica personalizzata è al di sotto di una determinata soglia.
  ![Scalabilità in base alla CPU][7]
- Impostare i limiti per le istanze. Se ad esempio si vogliono ridimensionare da 2 a 5 istanze a seconda delle fluttuazioni della metrica personalizzata, impostare il valore minimo su 2, quello massimo su 5 e quello predefinito su 2
> Nota: se si verifica un problema relativo alla metrica delle risorse e la capacità corrente è inferiore alla capacità predefinita, per assicurare la disponibilità della risorsa, la funzionalità di scalabilità automatica aumenterà il numero di istanze fino al valore predefinito. Se la capacità corrente è già superiore alla capacità predefinita, la funzionalità di scalabilità automatica non ridurrà il numero di istanze.
- Fare clic su 'Salva'

A questo punto A questo punto è stata creata l'impostazione di scalabilità automatica per l'app Web in base a una metrica personalizzata.

> Nota: gli stessi passaggi sono applicabili ai set di scalabilità di macchine virtuali e al ruolo del servizio cloud.

<!--Reference-->
[1]: https://docs.microsoft.com/en-us/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png

