---
title: Esercitazione su Adventure Works per Azure Analysis Services | Microsoft Docs
description: Introduzione all&quot;esercitazione su Adventure Works per Azure Analysis Services
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: a613bbe84a3834ab4fb237779248c7ad8d75b563
ms.contentlocale: it-it
ms.lasthandoff: 06/03/2017

---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services: esercitazione su Adventure Works

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Questa esercitazione comprende una serie di lezioni sulla creazione e la distribuzione di un modello tabulare al livello di compatibilità 1400 tramite [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

Se non si ha familiarità con Analysis Services e la modellazione tabulare, completare questa esercitazione è il modo più rapido per imparare a creare un modello tabulare di base. Dopo essersi assicurati che siano stati soddisfatti tutti i prerequisiti, per il completamento dell'esercitazione sono necessarie dalle due alle tre ore.  
  
## <a name="what-you-learn"></a>Contenuto dell'esercitazione   
  
-   Creazione di un nuovo progetto di modello tabulare al **livello di compatibilità 1400** in SSDT.
  
-   Importazione di dati da un database relazionale in un progetto di modello tabulare.  
  
-   Creazione e gestione di relazioni tra tabelle nel modello.  
  
-   Creazione di colonne calcolate, misure e indicatori di prestazioni chiave che consentono agli utenti di analizzare le metriche aziendali critiche.  
  
-   Creazione e gestione di prospettive e gerarchie che consentono agli utenti di esplorare con più facilità i dati del modello fornendo punti di vista specifici dell'applicazione e dell'azienda.  
  
-   Creazione di partizioni che dividono i dati della tabella in parti logiche più piccole che possono essere elaborate indipendentemente da altre partizioni.  
  
-   Protezione di oggetti e dati del modello tramite la creazione di ruoli con membri utente.  
  
-   Distribuzione di un modello tabulare in un server **Azure Analysis Services** o in un server SQL Server 2017 Analysis Services locale.  
  
## <a name="prerequisites"></a>Prerequisiti  
Per completare questa esercitazione, sono necessari:  
  
-   Un'istanza di Azure Analysis Services o di SQL Server 2017 Analysis Services in cui distribuire il modello. Iscriversi per ottenere una [versione di valutazione di Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) gratuita e [creare un server](../analysis-services-create-server.md). In alternativa, iscriversi e scaricare [SQL Server 2017 Community Technology Preview](https://www.microsoft.com/evalcenter/evaluate-sql-server-vnext-ctp). 

-   Un database SQL Server o un database SQL di Azure con il [database di esempio AdventureWorksDW2014](http://go.microsoft.com/fwlink/?LinkID=335807). Questo database di esempio include i dati necessari per completare l'esercitazione. Scaricare le [edizioni gratuite di SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads). In alternativa, iscriversi per ottenere una [versione di valutazione del database SQL di Azure](https://azure.microsoft.com/services/sql-database/) gratuita. 

    **Importante:** se si installa il database di esempio in un'istanza di SQL Server locale e si distribuisce il modello in un server Azure Analysis Services, è richiesto un [gateway dati locale](../analysis-services-gateway.md).

-   La versione più recente di [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).

-   La versione più recente di [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).    

-   Un'applicazione client, ad esempio [Power BI Desktop](https://powerbi.microsoft.com/desktop/) o Excel. 

## <a name="scenario"></a>Scenario  
Questa esercitazione è basata su Adventure Works Cycles, una società fittizia. Adventure Works è una società multinazionale di grandi dimensioni che produce e distribuisce biciclette in metallo e materiali compositi nei mercati commerciali di America del Nord, Europa e Asia. L'azienda ha un organico di 500 dipendenti. Inoltre, Adventure Works ha diversi team di vendita locali all'interno della sua base di mercato. Il progetto consiste nella creazione di un modello tabulare per gli utenti dei reparti vendite e marketing per l'analisi dei dati di vendita su Internet nel database AdventureWorksDW.  
  
Per portare a termine l'esercitazione, è necessario completare una serie di lezioni, ognuna delle quali prevede alcune attività. Per portare a termine la lezione è necessario completare ogni attività nell'ordine previsto. Una determinata lezione potrebbe includere più attività che producono un risultato analogo; tuttavia, le procedure per il completamento delle varie attività potrebbero essere leggermente diverse. Questo metodo dimostra che sono spesso disponibili più procedure per portare a termine un'attività e che l'utente dovrà applicare le competenze apprese nelle lezioni e nelle attività precedenti per ottenere il risultato previsto.  
  
Lo scopo delle lezioni è guidare l'utente durante la creazione di un modello tabulare di base in esecuzione usando alcune delle funzionalità incluse in SSDT. Poiché ogni lezione si basa su quella precedente, è necessario completare le lezioni in ordine.
  
Questa esercitazione non fornisce lezioni sulla gestione di un server nel portale di Azure, sulla gestione di un server o di un database tramite SSMS o sull'uso di un'applicazione client per l'esplorazione dei dati del modello. 


## <a name="lessons"></a>Lezioni  
Questa esercitazione include le lezioni seguenti:  
  
|Lezione|Tempo previsto per il completamento|  
|----------|------------------------------|  
|[Lezione 1: Creare un nuovo progetto di modello tabulare](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 minuti|  
|[Lezione 2: Ottenere i dati](../tutorials/aas-lesson-2-get-data.md)|10 minuti|  
|[Lezione 3: Contrassegnare come tabella data](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 minuti|  
|[Lezione 4: Creare relazioni](../tutorials/aas-lesson-4-create-relationships.md)|10 minuti|  
|[Lezione 5: Creare colonne calcolate](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 minuti|
|[Lezione 6: Creare misure](../tutorials/aas-lesson-6-create-measures.md)|30 minuti|  
|[Lezione 7: Creare indicatori di prestazioni chiave](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 minuti|  
|[Lezione 8: Creare prospettive](../tutorials/aas-lesson-8-create-perspectives.md)|5 minuti|  
|[Lezione 9: Creare gerarchie](../tutorials/aas-lesson-9-create-hierarchies.md)|20 minuti|  
|[Lezione 10: Creare partizioni](../tutorials/aas-lesson-10-create-partitions.md)|15 minuti|  
|[Lezione 11: Creare ruoli](../tutorials/aas-lesson-11-create-roles.md)|15 minuti|  
|[Lezione 12: Analizzare in Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 minuti| 
|[Lezione 13: Distribuire](../tutorials/aas-lesson-13-deploy.md)|5 minuti|  
  
## <a name="supplemental-lessons"></a>Lezioni supplementari  
Queste lezioni non sono necessarie per completare l'esercitazione, ma possono essere utili per una migliore comprensione delle funzionalità avanzate per la creazione di modelli tabulari.  
  
|Lezione|Tempo previsto per il completamento|  
|----------|------------------------------|  
|[Righe di dettaglio](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 minuti|
|[Sicurezza dinamica](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 minuti|
|[Gerarchie incomplete](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 minuti| 

  
## <a name="next-steps"></a>Passaggi successivi  
Per iniziare, vedere [Lezione 1: Creare un nuovo progetto di modello tabulare](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  


