---
title: Monitoraggio dell'avanzamento dei progetti di data science - Processo di data science per i team
description: Informazioni su come i manager del gruppo di data science e i responsabili di team e di progetto possono tenere traccia dell'avanzamento di un progetto di data science.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7745a53084f4477f7b736ea9d130ffd3eed771f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60252592"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>Monitoraggio dell'avanzamento dei progetti di data science

I manager del gruppo, i lead dei team e i lead del progetto di data science devono tenere traccia dell'avanzamento dei rispettivi progetti, che lavoro è stato svolto su di essi e da chi e cosa resta negli elenchi delle attività da eseguire. 

## <a name="azure-devops-dashboards"></a>Dashboard di Azure DevOps
Se si usa Azure DevOps, è possibile compilare dashboard per tenere traccia delle attività e degli elementi di lavoro associati a un determinato progetto agile. 

Per altre informazioni su come creare e personalizzare i dashboard e i widget in Azure DevOps, vedere i set di istruzioni seguenti:

- [Add and manage dashboards](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards) (Aggiungere e gestire i dashboard)
- [Add widgets to a dashboard](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard) (Aggiungere widget a un dashboard).

## <a name="example-dashboard"></a>Dashboard di esempio

Di seguito è riportato un semplice dashboard di esempio creato per monitorare le attività degli sprint di un progetto di data science agile e il numero di commit ai repository associati. Il pannello **in alto a sinistra** mostra:

- il conto alla rovescia dello sprint corrente; 
- il numero di commit per ciascun repository negli ultimi 7 giorni;
- l'elemento di lavoro per determinati utenti. 

I pannelli rimanenti mostrano il diagramma di flusso cumulativo, il burn-down e il burn-up per un progetto:

- **In basso a sinistra**:  diagramma di flusso cumulativo della quantità di lavoro in un determinato stato. Grigio significa approvato, blu significa che è stato eseguito il commit e verde che il lavoro è stato completato.
- **In alto a destra**: grafico burn-down del lavoro da completare in confronto al tempo rimanente.
- **In basso a destra**: grafico burn-up del lavoro completato in confronto alla quantità di lavoro totale.

![dashboard](./media/track-progress/dashboard.png)

Per informazioni su come creare questi grafici, vedere le guide introduttive e le esercitazioni in [Dashboards](https://docs.microsoft.com/azure/devops/report/dashboards/) (Dashboard).
 
## <a name="next-steps"></a>Passaggi successivi

Sono anche disponibili esercitazioni dettagliate che illustrano tutti i passaggi del processo per **scenari specifici**. Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'articolo [Procedure dettagliate di esempio](walkthroughs.md). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 
