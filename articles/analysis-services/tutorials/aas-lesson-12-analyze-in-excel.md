---
title: 'Esercitazione su Azure Analysis Services - Lezione 12: Analizzare in Excel | Microsoft Docs'
description: "Descrive come usare la funzionalità Analizza in Excel nel progetto per l'esercitazione su Azure Analysis Services."
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
ms.openlocfilehash: 8d7f79db612ee02eaf585f9b45d888b3533a91b2
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="analyze-in-excel"></a>Analizza in Excel

In questa lezione si usa la funzionalità Analizza in Excel per aprire Microsoft Excel, creare automaticamente una connessione all'area di lavoro del modello e aggiungere automaticamente una tabella pivot al foglio di lavoro. La funzionalità Analizza in Excel è progettata per offrire un modo rapido e semplice per testare l'efficacia della progettazione del modello prima di distribuirlo. In questa lezione non si esegue alcuna analisi dei dati. Lo scopo è invece quello di permettere agli autori di modelli di acquisire familiarità con gli strumenti disponibili per testare la progettazione del modello.   
  
Per completare questa lezione, è necessario che Excel sia installato nello stesso computer di Visual Studio.
  
Tempo previsto per il completamento della lezione: **cinque minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
Questo argomento fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione, è necessario avere completato la lezione precedente: [Lezione 11: Creare ruoli](../tutorials/aas-lesson-11-create-roles.md).  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>Esplorare il modello usando la prospettiva predefinita e la prospettiva Internet Sales  
In queste prime attività, il modello viene esplorato usando sia la prospettiva predefinita, che include tutti gli oggetti del modello, che la prospettiva Internet Sales creata in una lezione precedente. La prospettiva Internet Sales esclude l'oggetto tabella Customer.  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>Per esplorare il modello con la prospettiva predefinita  
  
1.  Fare clic sul menu **Modello** > **Analizza in Excel**.  
  
2.  Nella finestra di dialogo **Analizza in Excel** fare clic su **OK**.  
  
    Excel verrà aperto con una nuova cartella di lavoro. Viene creata una connessione all'origine dati con l'account utente corrente e viene usata la prospettiva predefinita per definire i campi visualizzabili. Viene aggiunta automaticamente una tabella pivot al foglio di lavoro.  
  
3.  In Excel, in **Elenco campi tabella pivot** notare che sono visualizzati i gruppi di misure **DimDate** e **FactInternetSales**, oltre alle tabelle **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** e **FactInternetSales** con le rispettive colonne.  
  
4.  Chiudere Excel senza salvare la cartella di lavoro.  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>Per esplorare il modello con la prospettiva Internet Sales  
  
1.  Fare clic sul menu **Modello** e quindi su **Analizza in Excel**.  
  
2.  Nella finestra di dialogo **Analizza in Excel** lasciare selezionata l'opzione **Utente corrente di Windows**, selezionare **Internet Sales** nell'elenco di riepilogo a discesa **Prospettiva** e quindi fare clic su **OK**. 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  In Excel, in **Campi tabella pivot**, notare che la tabella DimCustomer è esclusa dall'elenco dei campi.  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  Chiudere Excel senza salvare la cartella di lavoro.  
  
## <a name="browse-by-using-roles"></a>Esplorare il modello tramite i ruoli  
I ruoli sono una parte fondamentale di qualsiasi modello tabulare. Senza almeno un ruolo a cui gli utenti vengono aggiunti come membri, gli utenti non possono accedere ai dati e analizzarli usando il modello. La funzionalità Analizza in Excel consente di testare i ruoli definiti.  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Per esplorare il modello usando il ruolo utente Sales Manager  
  
1.  In SSDT fare clic sul menu **Modello** e quindi su **Analizza in Excel**.  
  
2.  In **Specificare il nome utente o il ruolo da utilizzare per la connessione al modello** selezionare **Ruolo**, selezionare **Sales Manager** nell'elenco di riepilogo a discesa e quindi fare clic su **OK**.  
  
    Excel verrà aperto con una nuova cartella di lavoro. Viene creata automaticamente una tabella pivot. L'elenco dei campi della tabella pivot include tutti i campi dati disponibili nel nuovo modello.  
      
3.  Chiudere Excel senza salvare la cartella di lavoro.  
  
## <a name="whats-next"></a>Passaggi successivi
Passare alla lezione successiva: [Lezione 13: Distribuire](../tutorials/aas-lesson-13-deploy.md).

  
  
  
