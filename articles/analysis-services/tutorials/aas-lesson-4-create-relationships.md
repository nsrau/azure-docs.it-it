---
title: 'Esercitazione su Azure Analysis Services - Lezione 4: Creare relazioni | Microsoft Docs'
description: Descrive come creare relazioni nel progetto per l'esercitazione su Azure Analysis Services.
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
ms.openlocfilehash: 57b3a172445047291f0aea5b1616b9dcbf6bf745
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="create-relationships"></a>Creare relazioni

In questa lezione si verificheranno le relazioni create automaticamente quando sono stati importati i dati e si aggiungeranno nuove relazioni tra tabelle diverse. Una relazione è una connessione tra due tabelle che stabilisce la modalità con cui devono essere correlati i dati in tali tabelle. Ad esempio, la tabella DimProduct e la tabella DimProductSubcategory hanno una relazione basata sul fatto che ogni prodotto appartiene a una sottocategoria. Per altre informazioni, vedere [Relationships](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular) (Relazioni).
  
Tempo previsto per il completamento della lezione: **10 minuti**  
  
## <a name="prerequisites"></a>prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 3: Contrassegnare come tabella data](../tutorials/aas-lesson-3-mark-as-date-table.md). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Esaminare le relazioni esistenti e aggiungere nuove relazioni  
Quando sono stati importati i dati usando Recupera dati, dal database AdventureWorksDW2014 si sono ottenute sette tabelle. In genere, quando si importano dati da un'origine relazionale, le relazioni esistenti vengono importate automaticamente insieme ai dati. Affinché la funzionalità Recupera dati crei automaticamente le relazioni nel modello di dati, deve esserci una relazione tra le tabelle nell'origine dati.

Prima di procedere alla creazione del modello, verificare che tali relazioni tra tabelle siano state create in modo appropriato. Per questa esercitazione vengono aggiunte anche tre nuove relazioni.  

  
#### <a name="to-review-existing-relationships"></a>Per esaminare le relazioni esistenti  
  
1.  Fare clic sul menu **Modello** > **Vista modelli** > **Vista diagramma**.  

    La finestra di progettazione dei modelli viene ora aperta in Vista diagramma, ovvero un formato grafico in cui sono rappresentate tutte le tabelle importate, con linee tra di esse. Le linee tra le tabelle indicano le relazioni create automaticamente al momento dell'importazione dei dati.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    > [!NOTE]
    > Se non è visibile alcuna relazione tra le tabelle, probabilmente significa che non ci sono relazioni tra tali tabelle nell'origine dati.

    Includere il maggior numero di tabelle possibile usando i controlli mini mappa nell'angolo inferiore destro della finestra di progettazione dei modelli. È anche possibile fare clic e trascinare le tabelle in posizioni diverse, avvicinandole o disponendole in un ordine particolare. Lo spostamento delle tabelle non influisce sulle relazioni tra le tabelle. Per visualizzare tutte le colonne in una determinata tabella, fare clic e trascinare un bordo della tabella per ingrandirla o ridurla.  
  
2.  Fare clic sulla linea continua tra la tabella **DimCustomer** e la tabella **DimGeography**. La linea continua tra queste due tabelle indica che questa relazione è attiva, vale a dire usata per impostazione predefinita durante il calcolo delle formule DAX.  
  
    Si noti che la colonna **GeographyKey** nella tabella **DimCustomer** e la colonna **GeographyKey** nella tabella **DimGeography** sono ora entrambe visualizzate all'interno di un riquadro. Queste colonne vengono usate nella relazione. Le proprietà della relazione sono ora visualizzate nella finestra **Proprietà**.  
  
    > [!TIP]  
    > Oltre a usare la finestra di progettazione dei modelli nella vista diagramma, è anche possibile usare la finestra di dialogo Gestisci relazioni per mostrare le relazioni tra tutte le tabelle in formato tabella. In Esplora modelli tabulari fare clic con il pulsante destro del mouse su **Relazioni** > **Gestisci relazioni**.
  
3.  Verificare che siano state create le relazioni seguenti al momento dell'importazione di ogni tabella dal database AdventureWorksDW:  
  
    |Attivo|Tabella|Tabella di ricerca correlata|  
    |----------|---------|------------------------|  
    |Sì|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Sì|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Sì|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Sì|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Sì|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Se mancano alcune relazioni, verificare che il modello includa le tabelle seguenti: DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory e FactInternetSales. Se si importano tabelle dalla stessa connessione origine dati in momenti distinti, le relazioni tra tali tabelle non vengono create e devono essere create manualmente. Se non è visualizzata alcuna relazione, significa che non ci sono relazioni nell'origine dati. È possibile crearle manualmente nel modello di dati.

### <a name="take-a-closer-look"></a>Dettagli del diagramma e delle relazioni
Nella vista diagramma è possibile notare la presenza di una freccia, un asterisco e un numero sulle linee che mostrano la relazione tra le tabelle.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

La freccia indica la direzione del filtro, l'asterisco indica che la tabella rappresenta il lato "molti" nella cardinalità della relazione e il numero uno indica che la tabella corrisponde al lato "uno" della relazione. Se è necessario modificare una relazione, ad esempio cambiare la direzione del filtro o la cardinalità della relazione, fare doppio clic sulla linea della relazione per aprire la finestra di dialogo Modifica relazione.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

Queste funzionalità sono pensate per la modellazione avanzata dei dati ed esulano dagli scopi di questa esercitazione. Per altre informazioni, vedere [Filtri incrociati bidirezionali per i modelli tabulari in Analysis Services](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services).

In alcuni casi, potrebbe essere necessario creare relazioni aggiuntive tra le tabelle nel modello per supportare una determinata logica di business. Per questa esercitazione, è necessario creare tre relazioni aggiuntive tra la tabella FactInternetSales e la tabella DimDate.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Per aggiungere nuove relazioni tra le tabelle  
  
1.  Nella finestra di progettazione dei modelli, nella tabella **FactInternetSales** fare clic sulla colonna **OrderDate** e tenere premuto, trascinare il cursore sulla colonna **Date** nella tabella **DimDate** e quindi rilasciare.  

    Verrà visualizzata una linea continua che indica che è stata creata una relazione attiva tra la colonna **OrderDate** nella tabella **FactInternetSales** e la colonna **Date** nella tabella **Date**. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > Quando si creano nuove relazioni, la cardinalità e la direzione di filtro tra la tabella primaria e la tabella di ricerca correlata vengono selezionate automaticamente.  
  
2.  Nella tabella **FactInternetSales** fare clic sulla colonna **DueDate** e tenere premuto, trascinare il cursore fino alla colonna **Date** nella tabella **DimDate** e quindi rilasciare.  
  
    Verrà visualizzata una linea punteggiata che indica che è stata creata una relazione inattiva tra la colonna **DueDate** nella tabella **FactInternetSales** e la colonna **Date** nella tabella **DimDate**. È possibile creare più relazioni tra le tabelle, ma può essere attiva una sola relazione alla volta. Le relazioni inattive possono essere rese attive eseguire aggregazioni speciali in espressioni DAX personalizzate.  
  
3.  Creare infine un'ultima relazione. Nella tabella **FactInternetSales** fare clic sulla colonna **ShipDate** e tenere premuto, trascinare il cursore fino alla colonna **Date** nella tabella **DimDate** e quindi rilasciare.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>Passaggi successivi
[Lezione 5: Creare colonne calcolate](../tutorials/aas-lesson-5-create-calculated-columns.md).
  
  
  
