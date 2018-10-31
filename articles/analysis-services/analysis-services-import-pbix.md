---
title: Importare un file di Power BI Desktop in Azure Analysis Services | Microsoft Docs
description: Viene descritto come importare un file di Power BI Desktop (file con estensione PBIX) tramite il portale di Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8882a44b0b6db1b3c23c017a072ebddfe9aa20f5
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090873"
---
# <a name="import-a-power-bi-desktop-file"></a>Importare un file di Power BI Desktop

È possibile importare un modello di dati in un file di Power BI Desktop in Azure Analysis Services. Vengono importati i metadati del modello, i dati memorizzati nella cache e le connessioni dell'origine dati. Non vengono importati i report e le visualizzazioni. I modelli di dati importati da Power BI Desktop hanno il livello di compatibilità 1400.

> [!IMPORTANT]
> Questa funzionalità è deprecata. Potrebbe venire rimossa o cambiata in modo significativo in un aggiornamento futuro. È consigliabile interrompere l'uso di questa funzionalità nei progetti nuovi ed esistenti per mantenere la compatibilità con gli aggiornamenti futuri. Per operazioni più avanzate di sviluppo e testing dei modelli, è consigliabile usare Visual Studio (SSDT) e SQL Server Management Studio (SSMS).

**Restrizioni**   


- Se il modello di dati è stato creato in Power BI Desktop, aggiornamento di luglio 2018 (2.60.5169.3201) o successivo, assicurarsi che nessuna funzionalità di anteprima è abilitata. Le funzionalità di anteprima non sono ancora supportate in Azure Analysis Services. Se viene visualizzato l'errore seguente durante l'importazione, sono abilitate funzionalità di anteprima del file pbix che non sono ancora supportate in Azure Analysis Services.

    ![Avviso sul livello di compatibilità](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)   
- È necessario disporre delle autorizzazioni di amministratore del server per importare da un file con estensione pbix.
- Il modello con estensione pbix può connettersi solo alle origini dati di **database SQL di Azure** e **Azure SQL Data Warehouse**.
- Il modello con estensione PBIX non può avere connessioni dinamiche o DirectQuery. 


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
