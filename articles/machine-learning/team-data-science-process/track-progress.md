---
title: Tenere traccia dello stato di avanzamento dei progetti del processo di analisi scientifica dei dati del team
description: In che modo i responsabili dei gruppi di data science, i responsabili del team e i responsabili del progetto possono tenere traccia dello stato di avanzamento di un progetto di data science.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864197"
---
# <a name="track-the-progress-of-data-science-projects"></a>Tenere traccia dello stato di avanzamento dei progetti di data science

I responsabili dei gruppi di analisi scientifica dei dati, i responsabili del team e i responsabili dei progetti possono tenere traccia dello stato di avanzamento dei progetti.  I manager vogliono sapere che lavoro è stato fatto, chi ha fatto il lavoro, e quale lavoro rimane.   Gestire le aspettative è un elemento importante di successo.

## <a name="azure-devops-dashboards"></a>Dashboard di Azure DevOps

Se si usa DevOps di Azure, è possibile creare dashboard per tenere traccia delle attività e degli elementi di lavoro associati a un determinato progetto Agile.If you're using Azure DevOps, you can build dashboards to track the activities and work items associated with a given Agile project. Per ulteriori informazioni sui dashboard, consultate [Dashboard, report e widget.](/azure/devops/report/dashboards/)

Per istruzioni su come creare e personalizzare dashboard e widget in Azure DevOps, vedere le guide introduttive seguenti:For instructions on how to create and customize dashboards and widgets in Azure DevOps, see the following quickstarts:

- [Add and manage dashboards](/azure/devops/report/dashboards/dashboards) (Aggiungere e gestire i dashboard)
- [Aggiungere widget a una dashboard](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Dashboard di esempio

Ecco un semplice dashboard di esempio che tiene traccia delle attività di sprint di un progetto di data science Agile, incluso il numero di commit nei repository associati. 

- Il riquadro **del conto alla rovescia** mostra il numero di giorni rimanenti nello sprint corrente. 

- I due **riquadri** di codice mostrano il numero di commit nei due repository di progetto negli ultimi sette giorni. 

- Elementi di lavoro **per TDSP Customer Project** mostra i risultati di una query per tutti gli elementi di lavoro e il relativo stato. 

- Un diagramma di **flusso cumulativo** (CFD) mostra il numero di elementi di lavoro chiusi e attivi.

- Il **grafico di burn-down** mostra il lavoro ancora da completare rispetto al tempo rimanente nello sprint.

- Il **grafico di burn-up** mostra il lavoro completato rispetto alla quantità totale di lavoro nello sprint.

![Dashboard](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

[Nelle procedure dettagliate in esecuzione del processo](walkthroughs.md) di analisi scientifica dei dati del team sono elencate le procedure dettagliate che illustrano tutti i passaggi del processo. Gli scenari collegati illustrano come gestire le risorse cloud e locali in applicazioni intelligenti. 
