---
title: 'Guida introduttiva: Strumento di esplorazione di Azure Time Series Insights - Azure Time Series Insights | Microsoft Docs'
description: Informazioni su come iniziare a usare lo strumento di esplorazione di Azure Time Series Insights. È possibile visualizzare grandi quantità di dati ed esplorare le funzionalità più importanti dell'ambiente.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/23/2019
ms.openlocfilehash: 0253a46cd73679c7c2f50a1e37d26d67578e342e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006431"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Guida introduttiva: Esplorare Azure Time Series Insights

In questo argomento di avvio rapido sullo strumento di esplorazione di Azure Time Series Insights viene illustrato come iniziare a usare Time Series Insights in un ambiente demo gratuito. Verrà descritto come usare il Web browser per visualizzare grandi volumi di dati IoT e verranno presentate le principali funzionalità attualmente disponibili a livello generale.

Azure Time Series Insights è un servizio completamente gestito per analisi, archiviazione e visualizzazione, che semplifica l'esplorazione e l'analisi di miliardi di eventi IoT contemporaneamente. Offre una visualizzazione globale dei dati per poter convalidare rapidamente le soluzioni IoT ed evitare così costosi periodi di inattività per i dispositivi di importanza strategica. Azure Time Series Insights consente di individuare tendenze nascoste e anomalie, nonché di eseguire analisi della causa radice.

Per una maggiore flessibilità, è possibile aggiungere Azure Time Series Insights a un'applicazione preesistente mediante le potenti [API REST](./time-series-insights-update-tsq.md) e l'[SDK client](https://github.com/microsoft/tsiclient). È possibile usare le API per archiviare, eseguire query e utilizzare dati di serie temporali in un'applicazione client di propria scelta. È anche possibile usare l'SDK client per aggiungere componenti dell'interfaccia utente all'applicazione esistente.

Questo argomento di avvio rapido sullo strumento di esplorazione di Time Series Insights offre una presentazione guidata delle funzionalità attualmente disponibili a livello generale.

> [!IMPORTANT]
> Se non è già stato creato un account Azure, crearne [uno gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="prepare-the-demo-environment"></a>Preparare l'ambiente demo

1. Nel browser passare alla [demo della disponibilità generale](https://insights.timeseries.azure.com/demo).

1. Se viene richiesto, accedere allo strumento di esplorazione di Time Series Insights usando le credenziali dell'account Azure.

1. Viene visualizzata la pagina della demo introduttiva di Time Series Insights. Selezionare **Next** (Avanti) per iniziare la demo introduttiva.

   [![Selezionare Next (Avanti)](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Esplorare l'ambiente demo

1. Viene visualizzato il **pannello di selezione di data e ora**. Usare questo pannello per selezionare un intervallo di tempo da visualizzare.

   [![Pannello di selezione di data e ora](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Selezionare un intervallo di tempo e trascinarlo nell'area, quindi selezionare **Search** (Cerca).

   [![Selezionare un intervallo di tempo](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   In Time Series Insights viene visualizzato un grafico riferito all'intervallo di tempo specificato. È possibile eseguire varie azioni all'interno di grafico a linee, ad esempio filtrare, aggiungere, ordinare e distribuire con spaziatura.

   Per tornare al **pannello di selezione di data e ora**, selezionare la freccia verso il basso come illustrato nella figura seguente:

   [![Grafico](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Selezionare **Add** (Aggiungi) nel **pannello Terms** (Termini) per aggiungere un nuovo termine di ricerca.

   [![Aggiungere un elemento](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. Nel grafico è possibile selezionare un'area, fare clic con il pulsante destro del mouse all'interno dell'area e scegliere **Explore Events** (Esplora eventi).

   [![Esplorare gli eventi](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Verrà visualizzata una griglia di dati non elaborati relativi all'area di riferimento.

   [![Visualizzazione griglia](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Selezionare e filtrare i dati

1. Modificare i termini per modificare i valori nel grafico. Aggiungere un altro termine per creare correlazioni incrociate tra tipi diversi di valori.

   [![Aggiungere un termine](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Immettere un termine di filtro nella casella **Filter series** (Filtra serie) per applicare filtri improvvisati alle serie. Per l'esempio descritto in questa guida introduttiva immettere **Station5** per creare una correlazione incrociata tra la temperatura e la pressione della stazione specifica.

   [![Filtrare le serie](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Dopo aver completato la guida introduttiva, è possibile usare il set di dati di esempio per creare visualizzazioni diverse.

## <a name="clean-up-resources"></a>Pulire le risorse

Ora che è stata completata l'esercitazione, pulire le risorse create:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutte le risorse** nel menu a sinistra e individuare il gruppo di risorse di Azure Time Series Insights.
1. Per eliminare l'intero gruppo di risorse e tutte le risorse in esso contenute, selezionare **Elimina** oppure rimuovere ogni risorsa singolarmente.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti a creare l'ambiente Time Series Insights vero e proprio:
> [!div class="nextstepaction"]
> [Pianificare l'ambiente Time Series Insights](time-series-insights-environment-planning.md)
