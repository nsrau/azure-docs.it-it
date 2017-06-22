---
title: 'Esercitazione su Azure Analysis Services - Lezione 9: Creare gerarchie | Microsoft Docs'
description: 
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: abe9edb0cbcdae18964731b1ab90a27117fc0c15
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-9-create-hierarchies"></a>Lezione 9: Creare gerarchie

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione verranno create gerarchie. Le gerarchie sono gruppi di colonne disposti in livelli. Ad esempio, una gerarchia Geografia potrebbe includere i sottolivelli Paese, Stato, Regione e Città. Le gerarchie possono essere visualizzate separatamente da altre colonne in un elenco di campi dell'applicazione client per la creazione di report, in modo che sia più semplice per gli utenti del client individuare i campi appropriati da includere in un report. Per altre informazioni, vedere [Hierarchies](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular) (Gerarchie).
  
Per creare gerarchie, usare la finestra di progettazione dei modelli in *Vista diagramma*. La creazione e la gestione delle gerarchie non è supportata in vista dati.  
  
Tempo previsto per il completamento della lezione: **20 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 8: Creare prospettive](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Creare gerarchie  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Per creare una gerarchia Category nella tabella DimProduct  
  
1.  Nella finestra di progettazione dei modelli (vista diagramma) fare clic con il pulsante destro del mouse sulla tabella **DimProduct** e quindi scegliere **Crea gerarchia**. Verrà visualizzata una nuova gerarchia nella parte inferiore della finestra della tabella. Rinominare la gerarchia **Category**.  
  
2.  Fare clic e trascinare la colonna **ProductCategoryName** nella nuova gerarchia **Category**.  
  
3.  Nella gerarchia **Category** fare clic con il pulsante destro del mouse su **ProductCategoryName** > **Rinomina** e quindi digitare **Category**.  
  
    > [!NOTE]  
    > La ridenominazione di una colonna in una gerarchia non comporta la ridenominazione di tale colonna nella tabella. Una colonna in una gerarchia è semplicemente una rappresentazione della colonna nella tabella.  
  
4.  Fare clic e trascinare la colonna **ProductSubcategoryName** nella gerarchia **Category**. Rinominarla **Subcategory**. 
  
5.  Fare clic con il pulsante destro del mouse sulla colonna **ModelName**, scegliere **Aggiungi a gerarchia** e quindi selezionare **Category**. Rinominarla **Model**.

6.  Infine, aggiungere **EnglishProductName** alla gerarchia Category. Rinominarla **Product**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Per creare gerarchie nella tabella DimDate  
  
1.  Nella tabella **DimDate** creare una nuova gerarchia denominata **Calendar**.  
  
3.  Aggiungere le colonne seguenti nell'ordine:

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  Nella tabella **DimDate** creare una gerarchia denominata **Fiscal**. Includere le colonne seguenti nell'ordine:  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  Infine, nella tabella **DimDate** creare una gerarchia denominata **ProductionCalendar**. Includere le colonne seguenti nell'ordine:  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>Passaggi successivi
[Lezione 10: Creare partizioni](../tutorials/aas-lesson-10-create-partitions.md). 
  
  

