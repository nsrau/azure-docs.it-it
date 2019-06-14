---
title: Creare una nuova risorsa di Azure Application Insights | Microsoft Docs
description: Impostare manualmente il monitoraggio di Application Insights per una nuova applicazione live.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073259"
---
# <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Application Insights di Azure visualizza dati relativi all'applicazione in una *risorsa* di Microsoft Azure. La creazione di una nuova risorsa fa dunque parte della [configurazione di Application Insights per monitorare una nuova applicazione][start]. Dopo aver creato la nuova risorsa, è possibile ottenere la chiave di strumentazione e usarlo per configurare il SDK di Application Insights. La chiave di strumentazione collega i dati di telemetria alla risorsa.

## <a name="sign-in-to-microsoft-azure"></a>Accedi a Microsoft Azure

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Accedi per il [portale di Azure](https://portal.azure.com)e creare una risorsa di Application Insights:

![Fare clic su '+' Accedi nell'angolo superiore sinistro. Selezionare gli strumenti di sviluppo seguita da Application Insights](./media/create-new-resource/new-app-insights.png)

   | Impostazioni        |  Value           | DESCRIZIONE  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore globalmente univoco | Nome che identifica l'app da che monitorare. |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del gruppo di risorse nuovo o esistente per ospitare i dati di Application Insights. |
   | **Posizione** | Stati Uniti orientali | Scegliere una località nelle vicinanze o vicino a dove è ospitata l'app. |

Immettere i valori appropriati nei campi necessari e quindi selezionare **revisione + Crea**.

![Immettere valori nei campi obbligatori e quindi selezionare "Rivedi e crea".](./media/create-new-resource/review-create.png)

Quando l'app è stata creata, viene aperto un nuovo riquadro. Questo riquadro si trova in cui visualizzare dati di utilizzo e prestazioni relativi all'applicazione monitorata. 

## <a name="copy-the-instrumentation-key"></a>Eseguire una copia della chiave di strumentazione

La chiave di strumentazione identifica la risorsa che si desidera associare i dati di telemetria con. È necessario copiare per aggiungere la chiave di strumentazione per il codice dell'applicazione.

![Fare clic e copiare la chiave di strumentazione](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Installare l’SDK nell'app

Installare Application Insights SDK nell'app. Questo passaggio dipende dal tipo di applicazione.

Usare la chiave di strumentazione per configurare l'[SDK installato nell'applicazione][start].

il SDK include i moduli standard che inviano dati di telemetria senza che sia necessario scrivere codice aggiuntivo. Per rilevare le azioni degli utenti o diagnosticare i problemi in modo più dettagliato, [usare l'API][api] per inviare dati di telemetria personalizzati.

## <a name="creating-a-resource-automatically"></a>Creazione automatica di una risorsa
È possibile scrivere uno [script di PowerShell](../../azure-monitor/app/powershell.md) per creare automaticamente una risorsa.

## <a name="next-steps"></a>Passaggi successivi
* [Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md)
* [Esplorare le metriche](../../azure-monitor/app/metrics-explorer.md)
* [Scrivere query di Analisi](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md