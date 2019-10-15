---
title: Informazioni sulle metriche in Azure Spring Cloud
description: Informazioni su come esaminare le metriche in Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038500"
---
# <a name="metrics-for-azure-spring-cloud"></a>Metriche di Azure Spring Cloud

Esplora metriche di Monitoraggio di Azure è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze ed esaminare i picchi e i cali nei valori delle metriche. Usare Esplora metriche per esaminare l'integrità e l'utilizzo delle risorse. In Azure Spring Cloud sono disponibili due opzioni per la visualizzazione delle metriche: i grafici nella pagina **Panoramica sull'applicazione** e la pagina Metriche a livello di servizio.

## <a name="application-overview-page"></a>Pagina Panoramica sull'applicazione

Ogni pagina **Panoramica sull'applicazione** presenta un grafico di metriche che consente di eseguire una rapida verifica dello stato dell'applicazione.  Passare alla pagina del servizio Azure Spring Cloud e selezionare **Dashboard dell'applicazione**, quindi scegliere un'applicazione nell'elenco.  

Sono disponibili 5 grafici con metriche aggiornate ogni minuto per quanto segue:

* **Errori server HTTP**: il numero di errori per le richieste HTTP inviate all'app.
* **Dati in entrata**: il numero di byte ricevuti dall'app.
* **Dati in uscita**: il numero di byte inviati all'app.
* **Richieste**: le richieste ricevute dall'app.
* **Tempo medio di risposta**: il tempo medio di risposta dell'app.

Per il grafico è possibile selezionare un intervallo di tempo compreso tra un'ora e 7 giorni.

## <a name="service-level-metric-queries"></a>Query sulle metriche a livello di servizio

Azure Spring Cloud consente di monitorare un'ampia varietà di metriche dell'applicazione. Per altre informazioni su questo servizio, vedere la [guida introduttiva](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) sulle metriche di Monitoraggio di Azure.

Per esaminare i dati delle metriche, selezionare una metrica, il valore di **Aggregazione** e l'intervallo di tempo.  Questi concetti vengono spiegati di seguito.

### <a name="aggregation"></a>Aggregazione 

Azure esegue il polling delle metriche e le aggiorna ogni minuto. Sono disponibili tre modi per aggregare i dati relativi a uno specifico periodo di tempo:

* **Total**: somma tutte le metriche come output di destinazione.
* **Average**: usa il valore medio del periodo come output di destinazione.
* **Max/Min**: usa il valore max/min del periodo come output di destinazione.

### <a name="time-range"></a>Intervallo di tempo

Selezionare un intervallo di tempo predefinito o definirne uno personalizzato.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Modifica della granularità della query sulle metriche

Per impostazione predefinita, Azure aggrega le metriche per tutte le applicazioni di un servizio Azure Spring Cloud. Per esaminare le metriche a livello di applicazione o di istanza, usare la funzione di filtro.  
Selezionare **Aggiungi filtro**, impostare la proprietà su **App** e selezionare l'applicazione di destinazione da monitorare. Facoltativamente, usare l'opzione **Applicare separazione** per tracciare linee separate per ogni app nel grafico.

>[!TIP]
> È possibile creare grafici personalizzati nella pagina di metriche e aggiungerli al **dashboard**. Per iniziare, assegnare un nome al grafico.  Selezionare quindi **Aggiungi al dashboard** nell'angolo in alto a destra. È ora possibile controllare l'applicazione nel **dashboard** del portale.