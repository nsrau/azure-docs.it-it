---
title: Dashboard dell'applicazione per le app LUIS | Microsoft Docs
description: Informazioni sulla dashboard dell'applicazione, uno strumento di visualizzazione di report che consente di monitorare le app in un solo momento.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: 4e117aa734c551f76c3602f81e3e2ebc6aa9f337
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378542"
---
# <a name="application-dashboard"></a>Dashboard dell'applicazione
La dashboard dell'app consente di monitorare l'applicazione in un solo momento. La **Dashboard** viene visualizzata quando viene aperta un'app tramite la selezione del nome dell'applicazione nella pagina **App personali**, quindi della **Dashboard** nel riquadro superiore. 

> [!CAUTION]
> Per ottenere le metriche più aggiornate per LUIS, è necessario:
> * Usare una [chiave di endpoint](luis-how-to-azure-subscription.md) di LUIS creata in Azure;
> * Usare la chiave di endpoint di LUIS per tutte le richieste di endpoint incluse le [API](https://aka.ms/luis-endpoint-apis) LUIS e i bot e
> * Usare diverse chiavi di endpoint per ogni app LUIS. Non usare una singola chiave di endpoint per tutte le app. La chiave di endpoint viene rilevata a livello di chiave, non a livello di app.  

La pagina **Dashboard** offre una panoramica dell'app LUIS includendo lo stato attuale del modello, nonché l'utilizzo dell'[endpoint](luis-glossary.md#endpoint) nel corso del tempo. <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>Stato dell'app
Nella dashboard vengono visualizzati il training dell'applicazione e lo stato della pubblicazione, incluse la data e l'ora dell'ultimo training e dell'ultima pubblicazione dell'app.  

![Dashboard - Stato dell'applicazione](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Statistiche dei dati del modello
La dashboard consente di visualizzare il numero totale di finalità, entità ed espressioni etichettate esistenti nell'app. 

![Statistiche dei dati dell'app](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Riscontri di endpoint
Nella dashboard vengono visualizzate le tutte le occorrenze di endpoint ricevute dall'app LUIS, consentendo di visualizzare i riscontri per uno specifico periodo. Il totale dei riscontri visualizzati è la somma dei riscontri di endpoint che usano una [chiave di endpoint](./luis-concept-keys.md#endpoint-key) e di quelli che usano una [chiave di creazione](./luis-concept-keys.md#authoring-key).

<!-- TBD: this image is old but I don't have a new one based on usage -->
![Riscontri di endpoint](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Il numero di passaggi di endpoint più recente è visualizzabile nella panoramica di servizio di LUIS del portale di Azure. 
 
### <a name="total-endpoint-hits"></a>Totale dei riscontri di endpoint
Totale dei riscontri di endpoint ricevuti nell'app dal momento della creazione di quest'ultima fino alla data attuale.

### <a name="endpoint-hits-per-period"></a>Riscontri di endpoint per singolo periodo
Il numero di riscontri ricevuti in un periodo precedente, con visualizzazione giornaliera. I punti tra le date di inizio e di fine rappresentano i giorni che rientrano in questo periodo. Passare il puntatore del mouse su ogni punto per visualizzare il numero di passaggi di ciascun giorno nel periodo specificato. 

Per selezionare un periodo da visualizzare nel grafico è necessario:
 
1. Fare clic su **Impostazioni aggiuntive** ![pulsante Impostazioni aggiuntive](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) per accedere all'elenco dei periodi. È possibile selezionare periodi che vanno da una settimana a un anno. 

    ![Riscontri di endpoint per periodo](./media/luis-how-to-use-dashboard/timerange.png)

2. Selezionare un periodo dall'elenco e quindi fare clic sulla freccia indietro ![Freccia indietro](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) per visualizzare il grafico.

### <a name="key-usage"></a>Uso della chiave
Numero di riscontri usati dalla chiave di sottoscrizione dell'applicazione. Per altre informazioni sulle chiavi di sottoscrizione, vedi [Chiavi in LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Scomposizione della finalità
La **Scomposizione delle finalità** consente di visualizzare una suddivisione delle finalità sulla base di espressioni o riscontri di endpoint etichettate/i. Questo grafico di riepilogo mostra l'importanza relativa di ogni finalità nell'app. Quando si passa il puntatore del mouse su una sezione, vengono visualizzati il nome della finalità e la percentuale che rappresenta rispetto al totale delle espressioni/dei riscontri di endpoint etichettate/i. 

![Scomposizione della finalità](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Per controllare se la suddivisione è basata su espressioni o riscontri di endpoint etichettate/i è necessario:

1. Fare clic su **Impostazioni aggiuntive** ![pulsante Impostazioni aggiuntive](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) per accedere all'elenco come nell'immagine seguente:

    ![Elenco di scomposizione della finalità](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Selezionare un valore dall'elenco e quindi fare clic sulla freccia indietro ![Freccia indietro](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) per visualizzare il grafico.

## <a name="entity-breakdown"></a>Scomposizione delle entità
La dashboard consente di visualizzare una suddivisione delle entità sulla base di espressioni o riscontri di endpoint etichettate/i. Questo grafico di riepilogo mostra l'importanza relativa di ogni entità nell'app. Quando si passa il puntatore del mouse su una sezione, vengono visualizzati il nome dell'entità e la percentuale in espressioni/riscontri di endpoint etichettate/i. 

![Scomposizione delle entità](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Per controllare se la suddivisione è basata su espressioni o riscontri di endpoint etichettate/i è necessario:

1. Fare clic su **Impostazioni aggiuntive** ![pulsante Impostazioni aggiuntive](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) per accedere all'elenco come nell'immagine seguente:

    ![Elenco di scomposizione di entità](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Selezionare un valore dall'elenco e quindi fare clic sulla freccia indietro ![Freccia indietro](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) per visualizzare il grafico.
