---
title: Importare un file di Power BI Desktop in Azure Analysis Services | Microsoft Docs
description: Viene descritto come importare un file di Power BI Desktop (file con estensione PBIX) tramite il portale di Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3dd90fc862e64812c0ba17bef74818d18788f4b5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440989"
---
# <a name="import-a-power-bi-desktop-file"></a>Importare un file di Power BI Desktop

È possibile importare un modello di dati in un file di Power BI Desktop in Azure Analysis Services. Vengono importati i metadati del modello, i dati memorizzati nella cache e le connessioni dell'origine dati. Non vengono importati i report e le visualizzazioni. I modelli di dati importati da Power BI Desktop hanno il livello di compatibilità 1400.

**Restrizioni**   
- Il modello con estensione pbix può connettersi solo alle origini dati di **database SQL di Azure** e **Azure SQL Data Warehouse**. 
- Il modello con estensione PBIX non può avere connessioni dinamiche o DirectQuery. 
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
