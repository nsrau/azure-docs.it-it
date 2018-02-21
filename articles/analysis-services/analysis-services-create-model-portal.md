---
title: Creare un modello tabulare usando la finestra di progettazione Web di Azure Analysis Services | Microsoft Docs
description: Informazioni su come creare un modello tabulare di Azure Analysis Services usando la finestra di progettazione Web nel portale di Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 0a70ce4a106b8d9103080f050ab2317cd69348c1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="create-a-model-in-azure-portal"></a>Creare un modello nel portale di Azure

La funzione finestra di progettazione Web di Azure Analysis Services (anteprima) nel portale di Azure offre un modo semplice e rapido per creare e modificare modelli tabulari ed eseguire query nei dati dei modelli direttamente dal browser in uso. 

Tenere presente che la finestra di progettazione Web è in versione di **anteprima**. Anche se vengono aggiunte continuamente nuove funzionalità, nell'anteprima le funzionalità sono limitate. Per operazioni più avanzate di sviluppo e testing dei modelli, è consigliabile usare Visual Studio (SSDT) e SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>prerequisiti

- Un server di Azure Analysis Services, livello Standard o Developer. I nuovi modelli creati usando la finestra di progettazione Web sono di tipo DirectQuery e sono supportati solo da questi livelli.
- Un database SQL di Azure, Azure SQL Data Warehouse o un file di Power BI Desktop (con estensione pbix) come origine dati. I nuovi modelli creati a partire da file di Power BI Desktop supportano origini dati di database SQL di Azure, Azure SQL Data Warehouse, Oracle e Teradata.
- Un account di SQL Server con password per la connessione a origini dati di database SQL di Azure e Azure SQL Data Warehouse.

## <a name="to-create-a-new-tabular-model"></a>Per creare un nuovo modello tabulare

1. Nel pannello **Panoramica**  del server > **Web designer** (Finestra di progettazione Web) fare clic su **Apri**.

    ![Creare un modello nel portale di Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. In **Web designer** (Finestra di progettazione Web)  >  **Modelli** fare clic su **+ Aggiungi**.

    ![Creare un modello nel portale di Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. In **Nuovo modello** digitare un nome di modello e quindi selezionare un'origine dati.

    ![Finestra di dialogo Nuovo modello nel portale di Azure](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. In **Connetti** immettere le proprietà di connessione. Il nome utente e la password devono fare riferimento a un account di SQL Server.

     ![Finestra di dialogo Connetti nel portale di Azure](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. In **Tabelle e viste** selezionare le tabelle da includere nel modello e quindi fare clic su **Crea**. Tra le tabelle con una coppia di chiavi vengono automaticamente create relazioni.

     ![Selezionare Viste e tabelle](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Nel browser verrà visualizzato il nuovo modello. A questo punto è possibile:   

- Eseguire query nei dati del modello trascinando i campi nella finestra di progettazione delle query e aggiungendo i filtri desiderati.
- Creare nuove misure nelle tabelle.
- Modificare i metadati del modello usando l'editor JSON.
- Aprire il modello in Visual Studio (SSDT), Power BI Desktop o Excel.

![Selezionare Viste e tabelle](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Quando si modificano i metadati del modello o si creano nuove misure nel browser, le modifiche vengono salvate nel modello in Azure. Se si stanno eseguendo operazioni sul modello anche in SSDT, Power BI Desktop o Excel, è possibile che il modello non venga sincronizzato.


## <a name="next-steps"></a>Passaggi successivi 
[Gestire ruoli e utenti del database](analysis-services-database-users.md)  
[Stabilire la connessione con Excel](analysis-services-connect-excel.md)  


