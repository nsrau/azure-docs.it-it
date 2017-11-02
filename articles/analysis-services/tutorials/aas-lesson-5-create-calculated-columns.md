---
title: 'Esercitazione su Azure Analysis Services - Lezione 5: Creare colonne calcolate | Microsoft Docs'
description: Descrive come creare colonne calcolate nel progetto per l'esercitazione su Azure Analysis Services.
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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: d7d2265aa82e54cdbd3b85e09a05e5ea55a7a2c2
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="lesson-5-create-calculated-columns"></a>Lezione 5: Creare colonne calcolate

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione vengono creati dati nel modello aggiungendo colonne calcolate. È possibile creare colonne calcolate (come colonne personalizzate) mentre si usa Recupera dati, tramite l'Editor di query o in un secondo momento nella finestra di progettazione dei modelli come descritto di seguito. Per altre informazioni, vedere [Calculated columns](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns) (Colonne calcolate).
  
Vengono create cinque nuove colonne calcolate in tre tabelle diverse. I passaggi sono leggermente diversi per ogni attività, per dimostrare che esistono diversi modi per creare nuove colonne, rinominarle e inserirle in diverse posizioni in una tabella.  

Questa è anche la prima lezione in cui si usano espressioni DAX (Data Analysis Expressions). DAX è un linguaggio speciale per la creazione di espressioni per formule altamente personalizzabili per i modelli tabulari. In questa esercitazione si usa il linguaggio DAX per creare colonne calcolate, misure e filtri di ruolo. Per altre informazioni, vedere [DAX in tabular models](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular) (DAX nei modelli tabulari). 
  
Tempo previsto per il completamento della lezione: **15 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 4: Creare relazioni](../tutorials/aas-lesson-4-create-relationships.md). 
  
## <a name="create-calculated-columns"></a>Creare colonne calcolate  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>Creare una colonna calcolata MonthCalendar nella tabella DimDate  
  
1.  Fare clic sul menu **Modello** > **Vista modelli** > **Vista dati**.  
  
    Le colonne calcolate possono essere create solo tramite la finestra di progettazione dei modelli in vista dati.  
  
2.  Nella finestra di progettazione dei modelli fare clic sula tabella (scheda) **DimDate**.  
  
3.  Fare clic con il pulsante destro del mouse sull'intestazione di colonna **CalendarQuarter** e quindi scegliere **Inserisci colonna**.  
  
    Una nuova colonna denominata **Colonna calcolata 1** verrà inserita a sinistra della colonna **CalendarQuarter**.  
  
4.  Nella barra della formula sopra la tabella, digitare la formula DAX seguente: La funzionalità Completamento automatico è utile per digitare correttamente i nomi completi di colonne e tabelle ed elenca le funzioni disponibili.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    I valori vengono quindi popolati per tutte le righe nella colonna calcolata. Se si scorre verso il basso nella tabella, si nota che le righe possono avere valori diversi per questa colonna, in base ai dati in ogni riga.    
  
5.  Rinominare la colonna **MonthCalendar**. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
La colonna calcolata MonthCalendar fornisce un nome ordinabile per il mese.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>Creare una colonna calcolata DayOfWeek nella tabella DimDate  
  
1.  Con la tabella **DimDate** ancora attiva, fare clic sul menu **Colonna** e quindi su **Aggiungi colonna**.  
  
2.  Nella barra della formula digitare la formula seguente:  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    Dopo aver completato la formula, premere INVIO. La nuova colonna verrà aggiunta all'estrema destra della tabella.  
  
3.  Rinominare la colonna **DayOfWeek**.  
  
4.  Fare clic sull'intestazione di colonna e quindi trascinare la colonna tra la colonna **EnglishDayNameOfWeek** e la colonna **DayNumberOfMonth**.  
  
    > [!TIP]  
    > Spostare le colonne nella tabella facilita la navigazione.  
  
La colonna calcolata DayOfWeek fornisce un nome ordinabile per il giorno della settimana.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>Creare una colonna calcolata ProductSubcategoryName nella tabella DimProduct  
  
  
1.  Nella tabella **DimProduct** scorrere fino all'estrema destra della tabella. Si noti che la colonna all'estrema destra è denominata **Aggiungi colonna** (in corsivo). Fare clic sull'intestazione di colonna.  
  
2.  Nella barra della formula digitare la formula seguente:  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  Rinominare la colonna **ProductSubcategoryName**.  
  
La colonna calcolata ProductSubcategoryName viene usata per creare una gerarchia nella tabella DimProduct che include i dati dalla colonna EnglishProductSubcategoryName nella tabella DimProductSubcategory. Le gerarchie non possono essere estese a più di una tabella. Le gerarchie verranno create più avanti nella lezione 9.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>Creare una colonna calcolata ProductCategoryName nella tabella DimProduct  
  
1.  Con la tabella **DimProduct** ancora attiva, fare clic sul menu **Colonna** e quindi su **Aggiungi colonna**.  
  
2.  Nella barra della formula digitare la formula seguente:  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  Rinominare la colonna **ProductCategoryName**.  
  
La colonna calcolata ProductCategoryName viene usata per creare una gerarchia nella tabella DimProduct che include i dati dalla colonna EnglishProductCategoryName nella tabella DimProductCategory. Le gerarchie non possono essere estese a più di una tabella.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>Creare una colonna calcolata Margin nella tabella FactInternetSales  
  
1.  Nella finestra di progettazione dei modelli selezionare la tabella **FactInternetSales**.  
  
2.  Creare una nuova colonna calcolata tra la colonna **SalesAmount** e la colonna **TaxAmt**.  
  
3.  Nella barra della formula digitare la formula seguente:  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  Rinominare la colonna **Margin**.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    La colonna calcolata Margin viene usata per analizzare i margini di profitto per ogni vendita.  
  
## <a name="whats-next"></a>Passaggi successivi
[Lezione 6: Creare misure](../tutorials/aas-lesson-6-create-measures.md).
  
  
  
