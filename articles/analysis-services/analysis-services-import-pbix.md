---
title: Importare un file di Power BI Desktop in Azure Analysis Services | Microsoft Docs
description: Viene descritto come importare un file di Power BI Desktop (file con estensione PBIX) tramite il portale di Azure.
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
ms.date: 02/26/2018
ms.author: owend
ms.openlocfilehash: 43eab587a1e5209069a248f1e2e1f57af158a2b8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importare un file di Power BI Desktop

È possibile creare un nuovo modello in Azure AS importando un DI file Power BI Desktop (file con estensione PBIX). Vengono importati i metadati del modello, i dati memorizzati nella cache e le connessioni dell'origine dati. Non vengono importati i report e le visualizzazioni. Nel server è possibile apportare modifiche al modello aggiornando o reimportando il file con estensione PBIX, tramite la funzionalità della finestra di progettazione Web (anteprima) nel portale oppure usando SQL Server Management Studio (SSMS). I modelli importati non possono essere aperti o esportati in Visual Studio.

> [!NOTE]
> Se il modello con estensione PBIX si connette alle origini dati locali, per il server deve essere configurato un [gateway locale](analysis-services-gateway.md).

## <a name="to-import-from-pbix"></a>Per importare da un file con estensione PBIX

1. Nel server aprire **Panoramica** > **Web designer** (Finestra di progettazione Web) e fare clic su **Apri**.

    ![Creare un modello nel portale di Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. In **Web designer** (Finestra di progettazione Web)  >  **Modelli** fare clic su **+ Aggiungi**.

    ![Creare un modello nel portale di Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. In **Nuovo modello** digitare un nome di modello e quindi selezionare il file di Power BI Desktop.

    ![Finestra di dialogo Nuovo modello nel portale di Azure](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. In **Importa** individuare e selezionare il file.

     ![Finestra di dialogo Connetti nel portale di Azure](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Vedere anche 

[Creare un modello nel portale di Azure](analysis-services-create-model-portal.md)   
[Connettersi ad Azure Analysis Services](analysis-services-connect.md)  
