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
ms.openlocfilehash: e0be0c69b501d7e93c65bcf23d4dd1b6bfa89caf
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importare un file di Power BI Desktop

È possibile creare un nuovo modello in Azure AS importando un DI file Power BI Desktop (file con estensione PBIX). Vengono importati i metadati del modello, i dati memorizzati nella cache e le connessioni dell'origine dati. Non vengono importati i report e le visualizzazioni.

**Restrizioni**   
- Il modello con estensione PBIX deve connettersi solo alle [origini dati di Analysis Services supportate](analysis-services-datasource.md). 
- Il modello con estensione PBIX non può avere connessioni dinamiche o DirectQuery. 
- Se il modello con estensione PBIX si connette alle origini dati locali, per il server Analysis Services deve essere configurato un [gateway dati locale](analysis-services-gateway.md).
- L'importazione potrebbe non riuscire se il modello di dati con estensione PBIX contiene metadati non supportati in Analysis Services.

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
