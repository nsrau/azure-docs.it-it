---
title: 'Esercitazione su Azure Analysis Services - Lezione 4: Creare relazioni | Microsoft Docs'
description: Descrive come creare relazioni nel progetto per l&quot;esercitazione su Azure Analysis Services.
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
ms.openlocfilehash: 2d1cf3761a59350fde9bbb0ac99b6eb96261a322
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-4-create-relationships"></a>Lezione 4: Creare relazioni

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione si verificheranno le relazioni create automaticamente quando sono stati importati i dati e si aggiungeranno nuove relazioni tra tabelle diverse. Una relazione è una connessione tra due tabelle che stabilisce la modalità con cui devono essere correlati i dati in tali tabelle. Ad esempio, la tabella DimProduct e la tabella DimProductSubcategory hanno una relazione basata sul fatto che ogni prodotto appartiene a una sottocategoria. Per altre informazioni, vedere [Relationships](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular) (Relazioni).
  
Tempo previsto per il completamento della lezione: **10 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 3: Contrassegnare come tabella data](../tutorials/aas-lesson-3-mark-as-date-table.md). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Esaminare le relazioni esistenti e aggiungere nuove relazioni  
Quando sono stati importati i dati usando Recupera dati, dal database AdventureWorksDW2014 si sono ottenute sette tabelle. In genere, quando si importano dati da un'origine relazionale, le relazioni esistenti vengono importate automaticamente insieme ai dati. Tuttavia, prima di procedere alla creazione del modello è necessario verificare che le relazioni create tra le tabelle siano appropriate. Per gli scopi di questa esercitazione verranno anche aggiunte tre nuove relazioni.  
  
#### <a name="to-review-existing-relationships"></a>Per esaminare le relazioni esistenti  
  
1.  Fare clic sul menu **Modello** > **Vista modelli** > **Vista diagramma**.  

    La finestra di progettazione dei modelli viene ora aperta in Vista diagramma, ovvero un formato grafico in cui sono rappresentate tutte le tabelle importate, con linee tra di esse. Le linee tra le tabelle indicano le relazioni create automaticamente al momento dell'importazione dei dati.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    Usare i controlli mini mappa nell'angolo inferiore destro della finestra di progettazione dei modelli per modificare la vista in modo da includere il maggior numero di tabelle possibile. È anche possibile fare clic e trascinare le tabelle in posizioni diverse, avvicinandole o disponendole in un ordine particolare. Lo spostamento delle tabelle non influisce sulle relazioni già esistenti tra le tabelle. Per visualizzare tutte le colonne in una determinata tabella, fare clic e trascinare un bordo della tabella per ingrandirla o ridurla.  
  
2.  Fare clic sulla linea continua tra la tabella **DimCustomer** e la tabella **DimGeography**. La linea continua tra queste due tabelle indica che questa relazione è attiva, vale a dire usata per impostazione predefinita durante il calcolo delle formule DAX.  
  
    Si noti che la colonna **GeographyKey** nella tabella **DimCustomer** e la colonna **GeographyKey** nella tabella **DimGeography** sono ora entrambe visualizzate all'interno di un riquadro. Ciò indica che queste sono le colonne usate nella relazione. Le proprietà della relazione sono ora visualizzate nella finestra **Proprietà**.  
  
    > [!TIP]  
    > Oltre a usare la finestra di progettazione dei modelli nella vista diagramma, è anche possibile usare la finestra di dialogo Gestisci relazioni per mostrare le relazioni tra tutte le tabelle in formato tabella. In Esplora modelli tabulari fare clic con il pulsante destro del mouse su **Relazioni** > **Gestisci relazioni**.
  
3.  Usare la finestra di progettazione dei modelli in vista diagramma oppure la finestra di dialogo Gestisci relazioni per verificare che siano state create le relazioni seguenti al momento dell'importazione di ogni tabella dal database AdventureWorksDW:  
  
    |Attivo|Table|Tabella di ricerca correlata|  
    |----------|---------|------------------------|  
    |Sì|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Sì|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Sì|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Sì|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Sì|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Se mancano relazioni tra quelle nella tabella precedente, verificare che il modello includa le tabelle seguenti: DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory e FactInternetSales. Se si importano tabelle dalla stessa connessione origine dati in momenti distinti, tutte le relazioni tra tali tabelle non verranno create e devono essere create manualmente.  

### <a name="take-a-closer-look"></a>Dettagli del diagramma e delle relazioni
Nella vista diagramma è possibile notare la presenza di una freccia, un asterisco e un numero sulle linee che mostrano la relazione tra le tabelle.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

La freccia mostra la direzione del filtro, l'asterisco indica che la tabella rappresenta il lato "molti" nella cardinalità della relazione e il numero 1 indica che la tabella corrisponde al lato "uno" della relazione. Se è necessario modificare una relazione, ad esempio cambiare la direzione del filtro o la cardinalità della relazione, fare doppio clic sulla linea della relazione per aprire la finestra di dialogo Modifica relazione.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

È molto probabile che non sarà mai necessario modificare una relazione. Queste funzionalità sono pensate per la modellazione avanzata dei dati ed esulano dagli scopi di questa esercitazione. Per altre informazioni, vedere [Filtri incrociati bidirezionali per i modelli tabulari in Analysis Services](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services).

In alcuni casi, potrebbe essere necessario creare relazioni aggiuntive tra le tabelle nel modello per supportare una determinata logica di business. Per questa esercitazione, è necessario creare tre relazioni aggiuntive tra la tabella FactInternetSales e la tabella DimDate.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Per aggiungere nuove relazioni tra le tabelle  
  
1.  Nella finestra di progettazione dei modelli, nella tabella **FactInternetSales** fare clic sulla colonna **OrderDate** e tenere premuto, trascinare il cursore sulla colonna **Date** nella tabella **DimDate** e quindi rilasciare.  

    Verrà visualizzata una linea continua che indica che è stata creata una relazione attiva tra la colonna **OrderDate** nella tabella **FactInternetSales** e la colonna **Date** nella tabella **Date**. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > Quando si creano nuove relazioni, la cardinalità e la direzione di filtro tra la tabella primaria e la tabella di ricerca correlata vengono selezionate automaticamente.  
  
2.  Nella tabella **FactInternetSales** fare clic sulla colonna **DueDate** e tenere premuto, trascinare il cursore fino alla colonna **Date** nella tabella **DimDate** e quindi rilasciare.  
  
    Verrà visualizzata una linea punteggiata che indica che è stata creata una relazione inattiva tra la colonna **DueDate** nella tabella **FactInternetSales** e la colonna **Date** nella tabella **DimDate**. È possibile creare più relazioni tra le tabelle, ma può essere attiva una sola relazione alla volta. Le relazioni inattive possono essere rese attive eseguire aggregazioni speciali in espressioni DAX personalizzate.  
  
3.  Infine, creare una o più relazioni. Nella tabella **FactInternetSales** fare clic sulla colonna **ShipDate** e tenere premuto, trascinare il cursore sulla colonna **Date** nella tabella **DimDate** e quindi rilasciare.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>Passaggi successivi
[Lezione 5: Creare colonne calcolate](../tutorials/aas-lesson-5-create-calculated-columns.md).
  
  
  

