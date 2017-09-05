---
title: Portali per la creazione e la modifica di ricerche log in Azure Log Analytics | Microsoft Docs
description: "Questo articolo descrive i portali che è possibile usare in Azure Log Analytics per la creazione e la modifica di ricerche log."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 29dfa31d38f85574f84ed351bc5c26224b1a7e16
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portali per la creazione e la modifica di ricerche log in Azure Log Analytics

> [!NOTE]
> Questo articolo descrive i portali in Azure Log Analytics che usano il nuovo linguaggio di query.  In [Aggiornare l'area di lavoro di Azure Log Analytics alla nuova ricerca log](log-analytics-log-search-upgrade.md) sono disponibili altre informazioni sul nuovo linguaggio e istruzioni per l'aggiornamento dell'area di lavoro.  
>
> Se l'area di lavoro non è stata aggiornata al nuovo linguaggio di query, è consigliabile consultare [Trovare dati tramite ricerche nei log in Log Analytics](log-analytics-log-searches.md) per informazioni sulla versione corrente del portale per la ricerca log.

È possibile usare le ricerche log in varie posizioni all'interno di Log Analytics per recuperare dati dall'area di lavoro.  Per le operazioni di creazione e modifica delle query e per interagire in modo interattivo con i dati restituiti, tuttavia, sono disponibili due opzioni descritte di seguito.  

## <a name="log-search-portal"></a>Portale per la ricerca log
Il portale per la ricerca log è accessibile dal portale di Azure o dal portale di OMS.  È adatto per la creazione di query semplici su una singola riga.  Il portale per la ricerca log può essere usato senza avviare un portale esterno ed è possibile usarlo per eseguire svariate funzioni, tra le quali la creazione di regole di avviso, la creazione di gruppi di computer e l'esportazione dei risultati delle query.  

Il portale per la ricerca log offre molte funzionalità per la modifica delle query senza che sia necessaria una conoscenza completa del linguaggio di query.  In [Creare ricerche log in Azure Log Analytics tramite il portale per la ricerca log](log-analytics-log-search-log-search-portal.md) è disponibile un riepilogo di queste funzionalità.


![Portale per la ricerca log](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portale Advanced Analytics
Il portale Advanced Analytics è un portale dedicato che offre funzionalità avanzate non disponibili nel portale per la ricerca log.  Le funzionalità includono la possibilità di modificare una query su più righe, eseguire codice in modo selettivo, Intellisense sensibile al contesto e analisi intelligente.  Il portale Advanced Analytics è più adatto per la progettazione di query complesse salvate come ricerca log oppure copiate e incollate in altri elementi di Log Analytics.  Avviare il portale Advanced Analytics da un collegamento nel portale per la ricerca log.

![Portale Advanced Analytics](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Il portale Advanced Analytics offre funzionalità avanzate e viene per questo di solito usato come strumento principale per la creazione e la modifica delle query.  Dopo aver determinato che la query funziona come previsto, sarà possibile copiarla e incollarla in altre posizioni, come il portale per la ricerca log o Progettazione viste.  Dato che il portale Advanced Analytics supporta anche query con più righe, tuttavia, è necessario tenere conto di quanto segue quando si copia una query da questo portale.

- È necessario rimuovere i commenti dalla query prima di copiarla e incollarla in un'altra posizione.  È possibile impostare una riga come commento anteponendovi due barre (//).  Quando si incolla una query con più righe su una riga singola, le interruzioni di riga vengono rimosse.  Se sono inclusi commenti, tutti i caratteri dopo il primo commento sono considerati parte del commento.


## <a name="next-steps"></a>Passaggi successivi

- Completare un'esercitazione sull'uso del [portale per la ricerca log](log-analytics-log-search-log-search-portal.md) o del [portale Advanced Analytics](https://go.microsoft.com/fwlink/?linkid=856587) per creare query.
- Vedere un'[esercitazione sulla scrittura di query](https://go.microsoft.com/fwlink/?linkid=856078) con il nuovo linguaggio di query.

