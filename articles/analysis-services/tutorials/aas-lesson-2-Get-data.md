---
title: 'Esercitazione su Azure Analysis Services - Lezione 2: Ottenere i dati | Microsoft Docs'
description: Descrive come ottenere e importare i dati nel progetto per l'esercitazione su Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/19/2017
ms.author: owend
ms.openlocfilehash: 07151fdf79d3b06f3144fbebff9015645b1ed000
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-2-get-data"></a>Lezione 2: Ottenere i dati

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione si usa la funzionalità Recupera dati in SSDT per connettersi al database di esempio AdventureWorksDW2014, selezionare i dati, visualizzarli in anteprima e filtrarli, quindi importarli nell'area di lavoro del modello.  
  
Con Recupera dati è possibile importare dati da un'ampia gamma di origini: database SQL di Azure, Oracle, Sybase, feed OData, Teradata, file e altro ancora. I dati possono essere recuperati anche tramite query, usando un'espressione di formula Power Query M.

> [!NOTE]
> Attività e immagini in questa esercitazione mostrano la connessione a un database AdventureWorksDW2014 in un server locale. In alcuni casi, un database AdventureWorksDW2014 in Azure può essere diverso.
  
Tempo previsto per il completamento della lezione: **10 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 1: Creare un nuovo progetto di modello tabulare](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
## <a name="create-a-connection"></a>Creare una connessione  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Per creare una connessione al database AdventureWorksDW2014  
  
1.  In Esplora modelli tabulari fare doppio clic su **Origini dati** > **Importa da origine dati**.  
  
    Verrà avviata la funzionalità Recupera dati che consente di eseguire in modo guidato i passaggi per la connessione a un'origine dati. Se Esplora modelli tabulari non è visibile, in **Esplora soluzioni** fare doppio clic su **Model.bim** per aprire il modello nella finestra di progettazione. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  In Recupera dati fare clic su **Database** > **Database di SQL Server** > **Connetti**.  
  
3.  Nella finestra di dialogo **Database di SQL Server**, in **Server**, digitare il nome del server in cui è stato installato il database AdventureWorksDW2014 e quindi fare clic su **Connetti**.  

4.  Quando viene richiesto di immettere le credenziali, è necessario specificare le credenziali usate da Analysis Services per connettersi all'origine dati durante l'importazione e l'elaborazione dei dati. In **Modalità di rappresentazione** selezionare **Rappresenta account**, immettere le credenziali e quindi fare clic su **Connetti**. È consigliabile usare un account con password senza scadenza.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Il metodo più sicuro per la connessione a un'origine dati consiste nell'usare un account utente di Windows e relativa password.
  
5.  Nello strumento di navigazione selezionare il database **AdventureWorksDW2014** e quindi fare clic su **OK**. Verrà creata la connessione al database. 
  
6.  Nello strumento di navigazione selezionare la casella di controllo per le tabelle seguenti: **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** e **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
Dopo aver fatto clic su OK verrà aperto l'Editor di query. Nella sezione successiva si selezioneranno solo i dati da importare.

  
## <a name="filter-the-table-data"></a>Filtrare i dati della tabella  
Le tabelle nel database di esempio AdventureWorksDW2014 includono dati che non è necessario includere nel modello. Se possibile, è consigliabile filtrare i dati in modo da escludere quelli non necessari e ridurre così lo spazio in memoria occupato dal modello. Con il filtro è possibile escludere alcune colonne dalle tabelle, in modo che non vengano importate nel database dell'area di lavoro o nel database del modello dopo la distribuzione. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Per filtrare i dati della tabella prima dell'importazione  
  
1.  Nell'Editor di query selezionare la tabella **DimCustomer**. Verrà aperta una vista della tabella DimCustomer nell'origine dati, ovvero il database di esempio AdventureWorksDW2014. 
  
2.  Effettuare una selezione multipla con CTRL+clic di **SpanishEducation**, **FrenchEducation**, **SpanishOccupation**, **FrenchOccupation**, fare clic con il pulsante destro del mouse e quindi scegliere **Rimuovi colonne**. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Dato che i valori per queste colonne non sono attinenti all'analisi delle vendite Internet, non è necessario importare queste colonne. L'eliminazione delle colonne superflue consente di ridurre le dimensioni del modello e di aumentarne l'efficienza.  

    > [!TIP]
    > Se si commette un errore, è possibile tornare indietro eliminando un passaggio in **PASSAGGI APPLICATI**.   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  Filtrare le tabelle restanti rimuovendo le colonne seguenti in ogni tabella:  
    
    **DimDate**
    
      |Colonna|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |Colonna|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |Colonna|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |Colonna|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |Colonna|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      Nessuna colonna rimossa.
  
## <a name="Import"></a>Importare i dati delle tabelle e delle colonne selezionate  
Dopo aver visualizzato in anteprima i dati e avere escluso quelli non necessari, è possibile importare il resto dei dati necessari. La procedura guidata importa i dati delle tabelle insieme alle eventuali relazioni tra le tabelle. Nel modello vengono create nuove tabelle e colonne e i dati esclusi tramite filtro non vengono importati.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>Per importare i dati delle tabelle e delle colonne selezionate  
  
1.  Controllare le selezioni. Se è tutto corretto, fare clic su **Importa**. Nella finestra di dialogo Elaborazione dati viene visualizzato lo stato dell'importazione dei dati dall'origine dati al database dell'area di lavoro.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Fare clic su **Close**.  

  
## <a name="save-your-model-project"></a>Salvare il progetto del modello  
È importante salvare frequentemente il progetto del modello.  
  
#### <a name="to-save-the-model-project"></a>Per salvare il progetto del modello  
  
-   Fare clic su **File** > **Salva tutti**.  
  
## <a name="whats-next"></a>Passaggi successivi
[Lezione 3: Contrassegnare come tabella data](../tutorials/aas-lesson-3-mark-as-date-table.md).

  
  
