---
title: Tenere traccia dello stato di avanzamento dei progetti team Data Science process
description: Il data science modo in cui i responsabili del gruppo, i lead dei team e i lead del progetto possono tenere traccia dello stato di avanzamento di un progetto data science.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864197"
---
# <a name="track-the-progress-of-data-science-projects"></a>Tenere traccia dello stato di avanzamento dei progetti di data science

I responsabili dei gruppi, i lead del team e i lead del progetto di Data Science possono tenere traccia dello stato di avanzamento dei progetti.  I manager desiderano sapere quali operazioni sono state eseguite, chi ha eseguito il lavoro e il lavoro rimanente.   La gestione delle aspettative è un elemento importante di successo.

## <a name="azure-devops-dashboards"></a>Dashboard di Azure DevOps

Se si usa Azure DevOps, è possibile creare dashboard per tenere traccia delle attività e degli elementi di lavoro associati a un determinato progetto Agile. Per altre informazioni sui dashboard, vedere [Dashboard, report e widget](/azure/devops/report/dashboards/).

Per istruzioni su come creare e personalizzare i dashboard e i widget in Azure DevOps, vedere le guide introduttive seguenti:

- [Add and manage dashboards](/azure/devops/report/dashboards/dashboards) (Aggiungere e gestire i dashboard)
- [Aggiungere widget a un dashboard](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Dashboard di esempio

Ecco un semplice dashboard di esempio che tiene traccia delle attività sprint di un progetto di data science agile, incluso il numero di commit nei repository associati. 

- Il riquadro del **conto alla rovescia** indica il numero di giorni rimanenti nello sprint corrente. 

- I due **riquadri del codice** mostrano il numero di commit nei due repository di progetto per gli ultimi sette giorni. 

- **Elementi di lavoro per il progetto cliente TDSP** Mostra i risultati di una query per tutti gli elementi di lavoro e il relativo stato. 

- Un **diagramma di flusso cumulativo** (CFD) Mostra il numero di elementi di lavoro chiusi e attivi.

- Il **grafico Burn-down** Mostra ancora il lavoro da completare rispetto al tempo rimanente nello sprint.

- Il **grafico Burn-Up** Mostra il lavoro completato rispetto alla quantità totale di lavoro nello sprint.

![Dashboard](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

[Procedure dettagliate che eseguono il processo di Data Science per i team](walkthroughs.md) elenca le procedure dettagliate che illustrano tutti i passaggi del processo. Negli scenari collegati viene illustrato come gestire le risorse cloud e locali in applicazioni intelligenti. 
