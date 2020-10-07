---
title: Iniziare a esplorare la demo di Advisor metriche
titleSuffix: Azure Cognitive Services
description: Esplorare l'interfaccia Web di Advisor metriche usando la demo fornita
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: fbc73e20b8cc2baa1cc5c5a5b2f674fb1b2dde84
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944621"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Avvio rapido: Esplorare la demo di Advisor metriche con dati di esempio

> [!Note]
> La demo di Advisor metriche è di sola lettura, quindi non è possibile eseguire l'onboarding dei dati. Per usare il servizio sui propri dati, occorre prima di tutto [creare una risorsa di Advisor metriche](web-portal.md).

Usare questo articolo per iniziare rapidamente a esplorare le funzionalità principali di Advisor metriche. A questo scopo è disponibile un sito demo con dati di esempio e configurazioni predefinite per acquisire familiarità con tutte le funzionalità del portale Web.

Fare clic su [questo collegamento](https://aka.ms/MetricsAdvisor/Demo) per accedere al sito demo.

## <a name="view-the-available-sample-data"></a>Visualizzare i dati di esempio disponibili

Dopo aver eseguito l'accesso al sito demo, viene visualizzata la pagina **Data feed** (Feed di dati). un feed di dati è un gruppo logico di dati di serie temporali su cui vengono eseguite query dall'origine dati. Per altre informazioni sui termini e i concetti usati in Advisor metriche, vedere il [glossario](../glossary.md). 

Sono elencati diversi feed di dati che vengono inseriti da diversi tipi di origini dati, come un database SQL di Azure o una tabella di Azure. Ognuno usa impostazioni di configurazione leggermente diverse per connettersi agli archivi dati associati.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Elenco dei dati di esempio" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>Esplorare le configurazioni dei feed di dati

Fare clic sul feed di dati *Sample - Cost/Revenue - City/Category* (Esempio - Costi/Ricavi - Città/Categoria). Si vedranno diverse sezioni di dettagli per il feed:

* Nome e stato di inserimento del feed di dati.
* Elenco di metriche oggetto di query dall'origine dati. Ad esempio, *cost* (costi) e *revenue* (ricavi). 
* Cronologia degli avvisi relativi a quando il feed di dati diventa non disponibile. 
* Log relativi agli aggiornamenti del feed di dati.   
* Informazioni e impostazioni del feed di dati.

:::image type="content" source="../media/data-feed-view.png" alt-text="Elenco dei dati di esempio" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Visualizzare le visualizzazioni e le configurazioni delle serie temporali

Fare clic sulla metrica *cost* nel feed di dati *Sample - Cost/Revenue - City/Category*. Si vedranno le serie temporali associate suddivise per dimensioni, con le visualizzazioni in base ai dati cronologici della metrica. La banda blu intorno ai dati della metrica rappresenta l'intervallo di valori previsto dai modelli di Machine Learning di Advisor metriche. I punti che ricadono all'esterno di questa banda verranno contrassegnati in rosso, a indicare che si tratta di anomalie rilevate. 

:::image type="content" source="../media/series-visualization.png" alt-text="Elenco dei dati di esempio" lightbox="../media/series-visualization.png":::

Il rilevamento anomalie può essere configurato modificando le **configurazioni di rilevamento** sul lato sinistro della pagina dei dettagli della metrica. Sono disponibili più metodi di rilevamento anomalie che possono essere usati in combinazione. Si possono anche provare diverse sensibilità, direzioni di rilevamento e altre configurazioni. Il collegamento **Advanced configuration** (Configurazione avanzata) nella parte inferiore della finestra delle **configurazioni di rilevamento** consente di creare impostazioni di rilevamento più complesse e personalizzate, che possono essere usate su gruppi o singole serie. 

È anche possibile ottimizzare il rilevamento anomalie fornendo feedback all'algoritmo di rilevamento. Fare clic su un'anomalia e usare il riquadro **Add feedback** (Aggiungi feedback) per configurare lo stato dell'anomalia, la stagionalità e lo stato dei punti di modifica. Questo feedback verrà incorporato nel rilevamento per i punti futuri.  

Nella parte inferiore del riquadro **Add feedback** è presente un collegamento a un **hub eventi imprevisti** che indirizza alla pagina di analisi degli eventi imprevisti per analizzare la causa radice di uno specifico evento.  

:::image type="content" source="../media/incident-link.png" alt-text="Elenco dei dati di esempio" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Esplorare i risultati del rilevamento anomalie ed eseguire l'analisi della causa radice

Quando si fa clic sul collegamento **To incident hub** (Vai all'hub eventi imprevisti) da un'anomalia, viene visualizzata una pagina di analisi degli eventi imprevisti, contenente informazioni dettagliate di diagnostica sull'evento imprevisto, ad esempio la gravità, il numero di anomalie coinvolte e l'ora di inizio/fine. La sezione **Root cause** (Causa radice) mostra suggerimenti automatizzati basati sull'analisi dell'albero dell'evento imprevisto, prendendo in considerazione deviazione, distribuzione e contributo alle anomalie padre, che potrebbero essere la causa radice dell'evento imprevisto.

La sezione **Diagnostics** (Diagnostica) mostra un albero dell'evento imprevisto, oltre a diverse schede per la diagnosi dell'evento imprevisto.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Elenco dei dati di esempio" lightbox="../media/incident-diagnostic.png":::

Individuando la causa radice dell'evento imprevisto, è possibile intervenire e mitigare il problema prima che la situazione peggiori. È anche possibile esplorare altre informazioni dettagliate facendo clic sulle altre funzionalità di diagnostica disponibili. 

## <a name="next-steps"></a>Passaggi successivi

- [Usare il portale Web](web-portal.md)
- [Usare l'API REST](rest-api.md)
- [Eseguire l'onboarding dei feed di dati](../how-tos/onboard-your-data.md)
    - [Gestire feed di dati](../how-tos/manage-data-feeds.md)
    - [Configurazioni per origini dati diverse](../data-feeds-from-different-sources.md)
