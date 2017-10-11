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
ms.date: 09/26/2017
ms.author: magoedte; bwren
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: b205f226d95d94b938a70a834ac0147e76d459ea
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portali per la creazione e la modifica di ricerche log in Azure Log Analytics

È possibile usare le ricerche log in varie posizioni all'interno di Log Analytics per recuperare dati dall'area di lavoro.  Per le operazioni di creazione e modifica delle query e per interagire in modo interattivo con i dati restituiti, tuttavia, sono disponibili due opzioni descritte di seguito.  

## <a name="log-search"></a>Ricerca log 
La pagina Ricerca log è accessibile dal portale di Azure.  È adatto per la creazione di query semplici su una singola riga.  La pagina Ricerca log può essere usata senza avviare un portale esterno ed è possibile usarla per eseguire svariate funzioni, tra le quali la creazione di regole di avviso, la creazione di gruppi di computer e l'esportazione dei risultati delle query.  

Ricerca log offre molte funzionalità per la modifica delle query senza che sia necessaria una conoscenza completa del linguaggio di query.  Per ottenere un riepilogo di queste funzionalità, vedere [Creare ricerche log in Azure Log Analytics tramite Ricerca log](log-analytics-log-search-log-search-portal.md).


![Pagina Ricerca log](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portale Advanced Analytics
Il portale Advanced Analytics è un portale dedicato che offre funzionalità avanzate non disponibili in Ricerca log dal portale di Azure.  Le funzionalità includono la possibilità di modificare una query su più righe, eseguire codice in modo selettivo, Intellisense sensibile al contesto e analisi intelligente.  Il portale Advanced Analytics è più adatto per la progettazione di query complesse salvate come ricerca log oppure copiate e incollate in altri elementi di Log Analytics.  Avviare il portale Advanced Analytics da un collegamento nella pagina Ricerca log.

![Portale Advanced Analytics](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Il portale Advanced Analytics offre funzionalità avanzate e viene per questo di solito usato come strumento principale per la creazione e la modifica delle query.  Dopo aver determinato che la query funziona come previsto, sarà possibile copiarla e incollarla in altre posizioni, ad esempio la pagina Ricerca log o Progettazione viste.  Dato che il portale Advanced Analytics supporta anche query con più righe, tuttavia, è necessario tenere conto di quanto segue quando si copia una query da questo portale.

- È necessario rimuovere i commenti dalla query prima di copiarla e incollarla in un'altra posizione.  È possibile impostare una riga come commento anteponendovi due barre (//).  Quando si incolla una query con più righe su una riga singola, le interruzioni di riga vengono rimosse.  Se sono inclusi commenti, tutti i caratteri dopo il primo commento sono considerati parte del commento.


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla creazione di query tramite il linguaggio di query, vedere l'esercitazione dettagliata sull'uso di [Ricerca log](log-analytics-tutorial-viewdata.md)
- Per creare query avanzate e usare un ambiente di sviluppo per le ricerche nei log, vedere il [portale Advanced Analytics](https://go.microsoft.com/fwlink/?linkid=856587).


