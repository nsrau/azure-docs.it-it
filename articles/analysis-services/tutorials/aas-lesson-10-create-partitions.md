---
title: 'Esercitazione su Azure Analysis Services - Lezione 10: Creare partizioni | Microsoft Docs'
description: Descrive come creare partizioni nel progetto per l&quot;esercitazione su Azure Analysis Services.
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
ms.openlocfilehash: 74a9577e2fb5cc83dd3f01d8ff87374240798700
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-10-create-partitions"></a>Lezione 10: Creare partizioni

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione verranno create partizioni per dividere la tabella FactInternetSales in parti logiche più piccole che possono essere elaborate (aggiornate) indipendentemente da altre partizioni. Per impostazione predefinita, ogni tabella inclusa nel modello ha una sola partizione, che include tutte le colonne e le righe della tabella. Per la tabella FactInternetSales i dati verranno suddivisi in base all'anno, ovvero una partizione per ognuno dei cinque anni della tabella. Ogni partizione può quindi essere elaborata in modo indipendente. Per altre informazioni, vedere [Partitions](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular) (Partizioni). 
  
Tempo previsto per il completamento della lezione: **15 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 9: Creare gerarchie](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Creare partizioni  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Per creare partizioni nella tabella FactInternetSales  
  
1.  In Esplora modelli tabulari espandere **Tabelle** e quindi fare clic con il pulsante destro del mouse su **FactInternetSales** > **Partizioni**.  
  
2.  In Gestione partizioni fare clic su **Copia** e quindi modificare il nome in **FactInternetSales2010**.
  
    Dato che si vuole che la partizione includa solo le righe all'interno di un determinato periodo, ovvero l'anno 2010, è necessario modificare l'espressione di query.
  
4.  Fare clic su **Progetta** per aprire l'Editor di query e quindi fare clic sulla query **FactInternetSales2010**.

5.  Nell'anteprima fare clic sulla freccia in giù nell'intestazione di colonna **OrderDate** e quindi fare clic su **Filtri per data/ora** > **Tra**.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  Nella finestra di dialogo Filtra righe in **Mostra righe in cui: OrderDate**, lasciare **è dopo o uguale a** e quindi immettere **1/1/2010** nel campo della data. Lasciare selezionato l'operatore **And**, selezionare **è prima di**, immettere **1/1/2011** nel campo della data e quindi fare clic su **OK**.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Si noti che nell'Editor di query, in Passaggi applicati, è visualizzato un altro passaggio denominato Filtrate righe. Questo è il filtro applicato per selezionare solo le date degli ordini dal 2010.

8.  Fare clic su **Importa** per eseguire la query.

    In Gestione partizioni si noti che l'espressione di query include ora una clausola Filtrate righe aggiuntiva.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Questa istruzione specifica che la partizione deve includere solo i dati nelle righe in cui OrderDate rientra nell'anno di calendario 2010 come specificato nella clausola Filtrate righe.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Per creare una partizione per l'anno 2011  
  
1.  Nell'elenco delle partizioni fare clic sulla partizione **FactInternetSales2010** creata e quindi fare clic su **Copia**.  Modificare il nome della partizione in **FactInternetSales2011**. 

    Non è necessario usare l'Editor di query per creare una nuova clausola Righe filtrate. Dato che è stata creata una copia della query per il 2010, è sufficiente apportare una piccola modifica nella query per il 2011.
  
2.  Per fare in modo che questa partizione includa solo le righe per l'anno 2011, in **Espressione query** sostituire gli anni nella clausola Filtrate righe rispettivamente con **2011** e **2012**, come di seguito:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Per creare partizioni per gli anni 2012, 2013 e 2014  
  
- Seguire i passaggi precedenti e creare partizioni per gli anni 2012, 2013 e 2014, modificando gli anni nella clausola Filtrate righe in modo da includere solo le righe per ognuno di questi anni. 
  

## <a name="delete-the-factinternetsales-partition"></a>Eliminare la partizione FactInternetSales
Ora che sono disponibili partizioni per ogni anno, è possibile eliminare la partizione FactInternetSales, in modo da evitare sovrapposizioni quando si sceglie Elabora tutto per l'elaborazione delle partizioni.

#### <a name="to-delete-the-factinternetsales-partition"></a>Per eliminare la partizione FactInternetSales
-  Fare clic sulla partizione FactInternetSales e quindi fare clic su **Elimina**.



## <a name="process-partitions"></a>Elaborare le partizioni  
In Gestione partizioni, si noti che la colonna **Ultima elaborazione** per ogni nuova partizione creata indica che queste partizioni non sono mai state elaborate. Quando si creano partizioni, è necessario eseguire un'operazione Elabora partizioni o Elabora tabella per aggiornare i dati in tali partizioni.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Per elaborare le partizioni FactInternetSales  
  
1.  Fare clic su **OK** per chiudere Gestione partizioni.  
  
2.  Fare clic sulla tabella **FactInternetSales** e quindi fare clic sul menu **Modello** > **Elabora** > **Elabora partizioni**.  
  
3.  Nella finestra di dialogo Elabora partizioni verificare che **Modalità** sia impostata su **Elaborazione predefinita**.  
  
4.  Selezionare la casella di controllo **Elaborazione** per ognuna delle cinque partizioni create e quindi fare clic su **OK**.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Se vengono richieste le credenziali di rappresentazione, immettere il nome utente e la password di Windows specificati nella lezione 2.  
  
    Verrà aperta la finestra di dialogo **Elaborazione dati** in cui vengono visualizzati i dettagli dell'elaborazione per ogni partizione. Si noti che viene trasferito un numero diverso di righe per ogni partizione, perché ogni partizione include solo le righe per l'anno specificato nella clausola WHERE nell'istruzione SQL. Al termine dell'elaborazione, proseguire e chiudere la finestra di dialogo Elaborazione dati.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>Passaggi successivi
Passare alla lezione successiva: [Lezione 11: Creare ruoli](../tutorials/aas-lesson-11-create-roles.md). 

