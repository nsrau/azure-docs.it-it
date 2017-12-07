---
title: Esecuzione di progetti di data science - Azure Machine Learning | Microsoft Docs
description: "In che modo un data scientist può monitorare l'avanzamento di un progetto di data science."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: c8e1882570a8cfcb4a75f0904a2138d007e0bc5b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="track-progress-of-data-science-projects"></a>Monitorare l'avanzamento dei progetti di data science

I manager del gruppo, i lead dei team e i lead del progetto di data science devono tenere traccia dell'avanzamento dei rispettivi progetti team, che lavoro è stato svolto su di essi e da chi e cosa resta negli elenchi delle attività da eseguire. 

## <a name="vsts-dashboards"></a>Dashboard VSTS
Se si usa Visual Studio Team Services (VSTS), si è in grado di compilare dei dashboard per tenere traccia delle attività e degli elementi di lavoro associati a un determinato progetto agile. 

Per altre informazioni su come creare e personalizzare i dashboard e i widget in Visual Studio Team Services, vedere i set di istruzioni seguenti:

- [Add and manage dashboards](https://docs.microsoft.com/vsts/report/dashboards/dashboards) (Aggiungere e gestire i dashboard)
- [Add widgets to a dashboard](https://docs.microsoft.com/vsts/report/dashboards/add-widget-to-dashboard) (Aggiungere widget a un dashboard).

## <a name="example-dashboard"></a>Dashboard di esempio

Di seguito è riportato un semplice dashboard di esempio creato per monitorare le attività degli sprint di un progetto di data science agile e il numero di commit ai repository associati. Il pannello **in alto a sinistra** mostra:

- il conto alla rovescia dello sprint corrente; 
- il numero di commit per ciascun repository negli ultimi 7 giorni;
- l'elemento di lavoro per determinati utenti. 

I pannelli rimanenti mostrano il diagramma di flusso cumulativo, il burn-down e il burn-up per un progetto:

- **In basso a sinistra**: diagramma di flusso cumulativo della quantità di lavoro in un determinato stato. Grigio significa approvato, blu significa che è stato eseguito il commit e verde che il lavoro è stato completato.
- **In alto a destra**: grafico burn-down del lavoro da completare in confronto al tempo rimanente.
- **In basso a destra**: grafico burn-up del lavoro completato in confronto alla quantità di lavoro totale.

![dashboard](./media/track-progress/dashboard.png)

Per informazioni su come creare questi grafici, vedere le guide introduttive e le esercitazioni in [Dashboards](https://docs.microsoft.com/vsts/report/dashboards/) (Dashboard).
 
## <a name="next-steps"></a>Passaggi successivi

Sono anche disponibili esercitazioni dettagliate che illustrano tutti i passaggi del processo per **scenari specifici**. Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'articolo [Procedure dettagliate di esempio](walkthroughs.md). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 
