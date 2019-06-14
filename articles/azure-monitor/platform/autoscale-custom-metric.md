---
title: Scalabilità automatica in Azure con metriche personalizzate
description: Informazioni su come ridimensionare la risorsa in base a una metrica personalizzata in Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: e6423f2ce3659fd3dd738dcc8a990261bc7bf60c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334338"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Introduzione alla scalabilità automatica in base a una metrica personalizzata in Azure
Questo articolo descrive come ridimensionare la risorsa in base a una metrica personalizzata nel portale di Azure.

La scalabilità automatica di Monitoraggio di Azure si applica solo a [set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Servizi cloud](https://azure.microsoft.com/services/cloud-services/), [app Web del servizio app](https://azure.microsoft.com/services/app-service/web/) e [servizi di gestione API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Introduzione
In questo articolo si presuppone che l'utente abbia un'app Web con Application Insights configurato. Se non è già stato fatto, è possibile [configurare Application Insights per il sito Web ASP.NET][1].

- Aprire il [portale di Azure][2].
- Fare clic sull'icona di Monitoraggio di Azure nel riquadro di spostamento a sinistra.
  ![Avviare Monitoraggio di Azure][3]
- Fare clic sull'impostazione Scalabilità automatica per visualizzare tutte le risorse per le quali è applicabile la scalabilità automatica, insieme allo stato corrente ![Individuare l'impostazione Scalabilità automatica in Monitoraggio di Azure][4]
- Aprire il pannello 'Scalabilità automatica' in Monitoraggio di Azure e selezionare una risorsa da ridimensionare
  > Note: la procedura seguente usa un piano di servizio app associato a un'app Web con Application Insights configurato.
- Nel pannello delle impostazioni di scalabilità automatica per la risorsa si noti che il numero di istanze corrente è 1. Fare clic su 'Abilita scalabilità automatica'.
  ![Impostazione di scalabilità per la nuova app Web][5]
- Specificare un nome per l'impostazione di scalabilità, quindi scegliere "Add a rule" (Aggiungi una regola). Si notino le opzioni per le regole di scalabilità visualizzate come riquadro contesto sul lato destro. Per impostazione predefinita viene applicata l'opzione per aumentare il numero di istanze di 1 se la percentuale CPU della risorsa supera il 70%. Impostare l'origine della metrica nella parte superiore su "Application Insights", selezionare la risorsa Application Insights nell'elenco a discesa 'Risorsa' e quindi selezionare la metrica personalizzata in base a ciò che si vuole ridimensionare.
  ![Ridimensionare in base a una metrica personalizzata][6]
- Analogamente al passaggio precedente, aggiungere una regola di scalabilità che ridurrà il numero di istanze di 1 se la metrica personalizzata è al di sotto di una determinata soglia.
  ![Scalabilità in base alla CPU][7]
- Impostare i limiti per le istanze. Se ad esempio si vogliono ridimensionare da 2 a 5 istanze a seconda delle fluttuazioni della metrica personalizzata, impostare il valore minimo su 2, quello massimo su 5 e quello predefinito su 2
  > Note: se si verifica un problema relativo alla metrica delle risorse e la capacità corrente è inferiore alla capacità predefinita, per assicurare la disponibilità della risorsa, la funzionalità di scalabilità automatica aumenterà il numero di istanze fino al valore predefinito. Se la capacità corrente è già superiore alla capacità predefinita, la funzionalità di scalabilità automatica non ridurrà il numero di istanze.
- Fare clic su 'Salva'

A questo punto è stata creata l'impostazione di scalabilità automatica per l'app Web in base a una metrica personalizzata.

> Note: gli stessi passaggi sono applicabili ai set di scalabilità di macchine virtuali e al ruolo del servizio cloud.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

