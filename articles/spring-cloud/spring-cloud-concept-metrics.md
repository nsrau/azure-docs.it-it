---
title: Informazioni sulle metriche per il cloud Spring di Azure
description: Informazioni su come esaminare le metriche nel cloud Spring di Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 952dfc1c707df3b7fa61443ea6ea18630352f0dc
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607804"
---
# <a name="metrics-for-azure-spring-cloud"></a>Metriche per il cloud Spring di Azure

Esplora metriche di monitoraggio di Azure è un componente del portale di Microsoft Azure che consente il tracciato dei grafici, la correlazione visiva delle tendenze e l'analisi di picchi e flessioni nelle metriche. Usare Esplora metriche per esaminare l'integrità e l'utilizzo delle risorse. Nel cloud Spring di Azure sono disponibili due opzioni per la visualizzazione delle metriche, ovvero i grafici nella pagina **Panoramica dell'applicazione** e la pagina metrica del livello di servizio.

## <a name="application-overview-page"></a>Pagina Panoramica applicazione

La pagina **Panoramica dell'applicazione** di ogni applicazione presenta un grafico delle metriche che consente di eseguire un rapido controllo dello stato dell'applicazione.  Passare alla pagina del servizio cloud di Azure Spring e selezionare **Dashboard applicazione**, quindi selezionare un'applicazione dall'elenco.  

Sono disponibili 5 grafici con metriche aggiornate ogni minuto per gli elementi seguenti:

* **Errori del server http**: numero di errori per le richieste HTTP all'app.
* **Dati in**: byte ricevuti dall'app.
* **Dati in uscita**: byte inviati all'app.
* **Richieste**: richieste ricevute dall'app.
* **Tempo medio di risposta**: tempo medio di risposta dall'app.

È possibile selezionare un intervallo di tempo per il grafico tra 1 ora fino a 7 giorni.

## <a name="service-level-metric-queries"></a>Query sulle metriche a livello di servizio

Il cloud Spring di Azure consente di monitorare un'ampia gamma di metriche dell'applicazione. Per altre informazioni su questo servizio, vedere la [Guida introduttiva](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) alle metriche di monitoraggio di Azure.

Per esaminare i dati delle metriche, selezionare la metrica, l' **aggregazione**e l'intervallo di tempo.  Questi concetti sono illustrati di seguito.

### <a name="aggregation"></a>Aggregazione 

Azure esegue il polling e aggiorna le metriche ogni minuto. Azure offre tre modi per aggregare i dati per un periodo di tempo scelto:

* **Total**: somma tutte le metriche come output di destinazione.
* **Media**: usare il valore medio nel periodo come output di destinazione.
* **Max/min**: usare il valore max/min nel periodo come output di destinazione.

### <a name="time-range"></a>Intervallo di tempo

Selezionare un intervallo di tempo predefinito o definirne uno personalizzato.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Modifica della granularità della query della metrica

Per impostazione predefinita, Azure aggrega le metriche per tutte le applicazioni del servizio cloud Spring di Azure. Per esaminare le metriche a livello di applicazione o di istanza, usare la funzione Filter.  
Selezionare **Aggiungi filtro**, impostare la proprietà su **app** e selezionare l'applicazione di destinazione che si vuole monitorare. Facoltativamente, usare l'opzione **applica suddivisione** per creare linee separate per ogni app nel grafico.

>[!TIP]
> È possibile creare grafici personalizzati nella pagina metrica e aggiungerli al **Dashboard**. Per iniziare, assegnare un nome al grafico.  Selezionare quindi **Aggiungi al dashboard nell'angolo superiore destro**. È ora possibile controllare l'applicazione nel **Dashboard**del portale.