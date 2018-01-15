---
title: 'Esercitazione su Azure Analysis Services - Lezione 9: Creare gerarchie | Microsoft Docs'
description: 
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: 041096b1a93fdc671939b6d6715a7836d1977e3c
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="create-hierarchies"></a>Creare gerarchie

In questa lezione verranno create gerarchie. Le gerarchie sono gruppi di colonne disposte in livelli. Ad esempio, una gerarchia Geografia può includere i sottolivelli Paese, Stato, Regione e Città. Le gerarchie possono essere visualizzate separatamente da altre colonne in un elenco di campi dell'applicazione client per la creazione di report, in modo che sia più semplice per gli utenti del client individuare i campi appropriati da includere in un report. Per altre informazioni, vedere [Hierarchies](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular) (Gerarchie).
  
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
  
1.  Nella tabella **DimDate** creare una gerarchia denominata **Calendar**.  
  
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
  
  
