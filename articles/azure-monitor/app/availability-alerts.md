---
title: Configurare avvisi di disponibilità con Azure Application Insights | Microsoft Docs
description: Configurare i test Web in Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305142"
---
# <a name="availability-alerts"></a>Avvisi di disponibilità

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) invia richieste Web all'applicazione a intervalli regolari da diversi punti in tutto il mondo. Può inviare un avviso se l'applicazione non risponde o se la risposta è troppo lenta.

## <a name="enable-alerts"></a>Attivare gli avvisi

Gli avvisi sono ora abilitati automaticamente per impostazione predefinita, ma per poter configurare completamente l'avviso è innanzitutto necessario creare inizialmente il test di disponibilità.

![Esperienza di creazione](./media/availability-alerts/create-test.png)

> [!NOTE]
>  con i [nuovi avvisi unificati](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), la gravità delle regole di avviso e le preferenze di notifica con [gruppi di azioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **devono essere** configurate nell'esperienza degli avvisi. Se non si esegue la procedura seguente, non si riceveranno notifiche all'interno del portale.

1. Dopo il salvataggio del test di disponibilità, nella scheda Dettagli fare clic sui puntini di sospensione accanto al test appena creato. Fare clic su "Modifica avviso".

   ![Modifica dopo il salvataggio](./media/availability-alerts/edit-alert.png)

2. Impostare il livello di gravità desiderato, la descrizione della regola ed, elemento ancora più importante, il gruppo di azioni con le preferenze di notifica che si vogliono usare per questa regola di avviso.

   ![Modifica dopo il salvataggio](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Inviare avvisi per X di Y località con segnalazione di errori

Questa regola di avviso è abilitata per impostazione predefinita nell'[esperienza dei nuovi avvisi unificati](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), quando si crea un nuovo test di disponibilità. È possibile rifiutarla esplicitamente selezionando l'opzione "classica" o scegliendo di disabilitare la regola di avviso.

> [!NOTE]
> Configurare i gruppi di azioni per ricevere le notifiche quando viene attivato l'avviso seguendo la procedura precedente. Se non si esegue questa procedura, le notifiche all'interno del portale si riceveranno solo quando la regola viene attivata.
>

### <a name="alert-on-availability-metrics"></a>Inviare avvisi per le metriche di disponibilità

Usando i [nuovi avvisi unificati](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) è possibile generare avvisi per la disponibilità aggregata segmentata e anche per le metriche di durata dei test:

1. Selezionare una risorsa di Application Insights nell'esperienza delle metriche e quindi selezionare una metrica di disponibilità:

    ![Selezione delle metriche di disponibilità](./media/availability-alerts/select-metric.png)

2. L'opzione Configura avvisi disponibile nel menu consentirà di accedere alla nuova esperienza in cui è possibile selezionare test specifici o località per le quali configurare la regola di avviso. In questa schermata è anche possibile configurare i gruppi di azioni per questa regola di avviso.

### <a name="alert-on-custom-analytics-queries"></a>Inviare avvisi per le query di analisi personalizzate

Usando i [nuovi avvisi unificati](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) è possibile inviare avvisi per le [query di log personalizzate](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Con le query personalizzate è possibile inviare avvisi per qualsiasi condizione arbitraria che consenta di ottenere il segnale più affidabile di problemi di disponibilità. Questa opzione si applica in particolare se si inviano risultati di disponibilità personalizzati tramite l'SDK TrackAvailability. 

> [!Tip]
> Le metriche sui dati di disponibilità includono tutti i risultati di disponibilità personalizzati che si possono inviare chiamando l'SDK TrackAvailability. È possibile usare gli avvisi per il supporto delle metriche per inviare avvisi sui risultati di disponibilità personalizzati.
>

## <a name="troubleshooting"></a>risoluzione dei problemi

Dedicata [risoluzione dei problemi di articolo](troubleshoot-availability.md).

## <a name="next-steps"></a>Passaggi successivi

* [Test web in più passi](availability-multistep.md)
* [Test web di ping URL](monitor-web-app-availability.md)

