---
title: Grafico delle metriche di Advisor metrica
titleSuffix: Azure Cognitive Services
description: Come configurare il grafico delle metriche e visualizzare le anomalie correlate nei dati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: aahi
ms.openlocfilehash: 09ea16f07973757b169f21c7c3f909a24651daa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940647"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Procedura: compilare un grafico delle metriche per analizzare le metriche correlate

Ogni metrica in metrica Advisor viene monitorata separatamente da un modello che apprende dai dati cronologici per stimare le tendenze future. Ogni metrica dispone di un modello separato a cui viene applicato. In alcuni casi, tuttavia, diverse metriche possono essere correlate tra loro e le anomalie devono essere analizzate tra più metriche. Il **grafico delle metriche** contribuisce a questo. 

Ad esempio, se si dispone di flussi di dati di telemetria diversi in metriche separate, le metriche vengono monitorate separatamente da metrica Advisor. Se le anomalie in una metrica causano anomalie in altre, la ricerca di tali relazioni e la causa principale dei dati può essere utile per l'indirizzamento degli eventi imprevisti. Il grafico delle metriche consente di creare un grafico della topologia visiva delle anomalie rilevate. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Selezionare una metrica per inserire il primo nodo nel grafico

Fare clic sulla scheda **grafico metriche** nella barra di spostamento. Il primo passaggio nella creazione di un grafico delle metriche consiste nell'inserire un nodo nel grafico. Selezionare un feed di dati e una metrica nella parte superiore della pagina. Nel pannello inferiore verrà visualizzato un nodo. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Selezionare una metrica":::

## <a name="add-a-noderelation-on-existing-node"></a>Aggiungere un nodo/relazione sul nodo esistente

Successivamente, è necessario aggiungere un altro nodo e specificare una relazione con uno o più nodi esistenti. Selezionare un nodo esistente e fare clic con il pulsante destro del mouse su di esso. Verrà visualizzato un menu di scelta rapida con varie opzioni. 

Fare clic su **Aggiungi relazione**. sarà possibile scegliere un'altra metrica e specificare il tipo di relazione tra i due nodi. È inoltre possibile applicare filtri di dimensione specifici. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Selezionare una metrica":::

Dopo aver ripetuto i passaggi precedenti, si disporrà di un grafico delle metriche che descrive le relazioni tra tutte le metriche correlate.
**Hint sui colori del nodo**
> [!TIP]
> - Quando si seleziona una metrica e un filtro della dimensione, tutti i nodi con la stessa metrica e il filtro della dimensione nel grafico saranno colorati come **<font color=blue>blu</font>**.
> - I nodi non selezionati che rappresentano una metrica nel grafico saranno colorati in **<font color=green>verde</font>**.
> - Se si verifica un'anomalia nella metrica corrente, il nodo sarà colorato in **<font color=red>rosso</font>**.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Visualizzare lo stato anomalie delle metriche correlate nell'hub eventi

Quando si compila il grafico delle metriche, ogni volta che viene rilevata un'anomalia nelle metriche all'interno del grafo, sarà possibile visualizzare gli Stati delle anomalie correlate e ottenere una visualizzazione di alto livello dell'evento imprevisto. 

Fare clic in un evento imprevisto all'interno del grafico e scorrere verso il basso fino a **analisi delle metriche incrociate**, sotto le informazioni di diagnostica.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Selezionare una metrica":::

## <a name="next-steps"></a>Passaggi successivi

- [Regolare il rilevamento anomalie usando il feedback](anomaly-feedback.md)
- [Diagnosticare un evento imprevisto](diagnose-incident.md).
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](configure-metrics.md)
