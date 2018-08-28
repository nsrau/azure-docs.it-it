---
title: Importare un file di Power BI Desktop in Azure Analysis Services | Microsoft Docs
description: Viene descritto come importare un file di Power BI Desktop (file con estensione PBIX) tramite il portale di Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a2855ca5dbb76d3fcc30c4b1007c20bb48c91c9b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "42144324"
---
# <a name="import-a-power-bi-desktop-file"></a>Importare un file di Power BI Desktop

È possibile importare un modello di dati in un file di Power BI Desktop in Azure Analysis Services. Vengono importati i metadati del modello, i dati memorizzati nella cache e le connessioni dell'origine dati. Non vengono importati i report e le visualizzazioni. I modelli di dati importati da Power BI Desktop hanno il livello di compatibilità 1400.

**Restrizioni**   

- L'importazione da un file pbix usa la funzionalità di progettazione web nel portale, ovvero **anteprima**. La funzionalità è limitata. Per operazioni più avanzate di sviluppo e testing dei modelli, è consigliabile usare Visual Studio (SSDT) e SQL Server Management Studio (SSMS).
- È necessario disporre delle autorizzazioni di amministratore del server per importare da un file con estensione pbix.
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

## <a name="change-credentials"></a>Modificare le credenziali

Quando si importa un modello di dati da un file pbix, per impostazione predefinita, le credenziali usate per connettersi a un'origine dati sono impostate su ServiceAccount. Dopo aver importato un modello da un file pbix, è possibile modificare le credenziali usando i metodi seguenti:

- Usare la versione di luglio 2018 (versione 17.8.1) o versioni successive di SQL Server Management Studio per modificare le credenziali. Questo è il modo più semplice.
- Usare TMSL [comando Alter](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl) nell’[oggetto DataSources](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl) per modificare le proprietà di stringa di connessione. 
- Aprire il modello in Visual Studio, modificare le credenziali per la connessione dell'origine dati e quindi ridistribuire il modello.

Per modificare le credenziali tramite SSMS. 

1. In SQL Server Management Studio, espandere database > **connessioni**. 
2. Fare doppio clic sulla connessione al database e quindi fare clic su **Aggiorna credenziali**. 

    ![Aggiornare le credenziali](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. Nella finestra di dialogo delle credenziali, selezionare un tipo di credenziali e immettere le credenziali. Per l'autenticazione SQL, selezionare Database. Per l'account dell'organizzazione (OAuth), selezionare l'account Microsoft.
    ![Modificare le credenziali](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

La versione di luglio 2018 di Power BI Desktop include una nuova funzionalità per la modifica delle autorizzazioni dell'origine dati. Nella scheda **Home**, fare clic su **Modifica query**  > **impostazioni origine dati**. Selezionare la connessione all'origine dati e quindi fare clic su **Modifica autorizzazioni**.


## <a name="see-also"></a>Vedere anche 

[Creare un modello nel portale di Azure](analysis-services-create-model-portal.md)   
[Connettersi ad Azure Analysis Services](analysis-services-connect.md)  
