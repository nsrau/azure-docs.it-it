---
title: Dashboard panoramica di Azure Application Insights| Microsoft Docs
description: Monitorare le applicazioni con la funzionalità Dashboard panoramica di Azure Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: d1823779f8a8070149811e2349fc9f4281072d38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497160"
---
# <a name="application-insights-overview-dashboard"></a>Dashboard panoramica di Application Insights

Application Insights ha sempre fornito un riquadro riepilogativo per consentire una valutazione rapida e immediata dello stato e delle prestazioni dell'applicazione. Il nuovo dashboard panoramica offre un'esperienza più flessibile e veloce.

## <a name="how-do-i-test-out-the-new-experience"></a>Come testare la nuova esperienza?

Il nuovo dashboard panoramica ora avvia per impostazione predefinita:

![Riquadro della Panoramica (anteprima)](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Migliori prestazioni

La selezione dell'intervallo di tempo è stata semplificata in una semplice interfaccia con un solo clic.

![Intervallo di tempo](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Le prestazioni complessive sono state notevolmente migliorate. È disponibile l'accesso con un clic a funzionalità comuni, ad esempio **Ricerca** e **Analitica**. Ciascun riquadro KPI con aggiornamento dinamico predefinito fornisce informazioni dettagliate sulle funzionalità Application Insights corrispondenti. Per altre informazioni sulle richieste non riuscite, selezionare **Errori** sotto l'intestazione **Analisi**:

![Errori](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Dashboard dell'applicazione

Il dashboard delle applicazioni sfrutta la tecnologia del dashboard esistente in Azure per fornire una visualizzazione a pannello singolo completamente personalizzabile dello stato e delle prestazioni dell'applicazione.

Per accedere al dashboard predefinito selezionare _Dashboard dell'applicazione_ nell'angolo superiore sinistro.

![Visualizzazione dashboard](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Se è la prima volta che si accede al dashboard, verrà avviata una visualizzazione predefinita:

![Visualizzazione dashboard](./media/overview-dashboard/0001-dashboard.png)

Se si desidera, è possibile mantenere la visualizzazione predefinita. Oppure è anche possibile aggiungerla ed eliminare dal dashboard per soddisfare al meglio le esigenze del team.

> [!NOTE]
> Tutti gli utenti con accesso alla risorsa di Application Insights condividono la stessa esperienza di dashboard dell'applicazione. Le modifiche apportate da un utente modificheranno la visualizzazione per tutti gli utenti.

Per tornare all'esperienza panoramica, selezionare:

![Pulsante Panoramica](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si seleziona **configurare le impostazioni di riquadro** e impostare un intervallo di tempo personalizzato che superano di 31 giorni al dashboard non verrà visualizzati oltre i 31 giorni di dati, anche con la conservazione dei dati predefinita di 90 giorni. Non è attualmente alcuna soluzione alternativa per questo comportamento.

## <a name="next-steps"></a>Passaggi successivi

- [Grafici a imbuto](../../azure-monitor/app/usage-funnels.md)
- [Conservazione](../../azure-monitor/app/usage-retention.md)
- [Flussi degli utenti](../../azure-monitor/app/usage-flows.md)