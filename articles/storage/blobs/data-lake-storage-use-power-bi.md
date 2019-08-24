---
title: Analizzare i dati in Azure Data Lake Storage Gen2 tramite Power BI | Microsoft Docs
description: Usare Power BI per analizzare i dati archiviati in Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: 59574f0a07f4ecc145f2b0efd430e65cbdebcc65
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991567"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Analizzare i dati in Azure Data Lake Storage Gen2 tramite Power BI

Questo articolo illustra come usare Power BI Desktop per analizzare e visualizzare i dati archiviati in un account di archiviazione con uno spazio dei nomi gerarchico (Azure Data Lake Storage Gen2).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con uno spazio dei nomi gerarchico. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne uno.
> Questo articolo presuppone che sia stato creato un account denominato `myadlsg2`.
> * Un file di dati di `Drivers.txt` esempio denominato che si trova nell'account di archiviazione.
> È possibile scaricare questo esempio dal [Repository Azure Data Lake git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceDataDrivers.txt)e quindi caricare il file nell'account di archiviazione.
> * **Power BI Desktop**. Può essere scaricato dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Creare un report in Power BI Desktop

1. Avviare Power BI Desktop sul computer.
2. Dalla scheda **Home** della barra multifunzione fare clic su **Ottieni dati**, quindi fare clic su **altro**.
3. Nella finestra di dialogo **Ottieni dati** fare clic su **Azure**, fare clic su **Azure Data Lake Store Gen2 (beta)** e quindi su **Connetti**.

    ![Pagina di recuperare i dati](media/data-lake-storage-use-power-bi/get-data-page.png)

4. Nella finestra di dialogo **Azure Data Lake storage Gen2** è possibile specificare l'URL per l'account Azure Data Lake storage Gen2, il file System o la sottocartella usando il formato dell'endpoint del contenitore. Gli URL per data Lake storage Gen2 hanno il modello `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` seguente e quindi fare clic su **OK**.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. Nella finestra di dialogo successiva fare clic su **Accedi** per accedere al proprio account di archiviazione. Si verrà reindirizzati alla pagina di accesso dell'organizzazione. Seguire le istruzioni per accedere all'account.

    ![Pagina di accesso](media/data-lake-storage-use-power-bi/sign-in.png)

6. Dopo aver eseguito l'accesso, fare clic su **Connetti**.

    ![Pagina di accesso](media/data-lake-storage-use-power-bi/signed-in.png)

7. La finestra di dialogo successiva Mostra tutti i file nell'URL fornito nel passaggio 4 precedente, incluso il file caricato nell'account di archiviazione. Verificare le informazioni e quindi fare clic su **carica**.

    ![File system](media/data-lake-storage-use-power-bi/file-systems.png)

8. Una volta che i dati sono stati caricati correttamente in Power BI, nella scheda **campi** vengono visualizzati i campi seguenti.

    ![Scheda campi](media/data-lake-storage-use-power-bi/fields.png)

    Tuttavia, per visualizzare e analizzare i dati, è preferibile che i dati siano disponibili in base ai campi seguenti.

    ![Campi](media/data-lake-storage-use-power-bi/preferred-fields.png)

    La procedura che segue descrive come aggiornare la query per convertire i dati importati nel formato desiderato.

9. Nella scheda **Home** della barra multifunzione fare clic su **modifica query**.

    ![Query](media/data-lake-storage-use-power-bi/queries.png)

10. Nell' **editor di query**, nella colonna **contenuto** , fare clic su **binario**. Il file verrà rilevato automaticamente come CSV e dovrebbe essere visualizzato un output come illustrato di seguito. I dati ora sono disponibili in un formato che può essere usato per creare visualizzazioni.

    ![Output](media/data-lake-storage-use-power-bi/binary.png)

11. Nella scheda **Home** della barra multifunzione fare clic su **Chiudi** e **applica**, quindi fare clic su **Chiudi** e **applica**.

    ![Chiudere e applicare](media/data-lake-storage-use-power-bi/close-apply.png)

12. Dopo l'aggiornamento della query, la scheda **Campi** contiene i nuovi campi disponibili per la visualizzazione.

    ![Nuovi campi](media/data-lake-storage-use-power-bi/new-fields.png)

13. È possibile creare un grafico a torta per rappresentare i conducenti in ogni città per un determinato paese. Per eseguire questa operazione, effettuare le selezioni seguenti.

    Nella scheda **visualizzazioni** fare clic sul simbolo per un grafico a torta.

    ![Visualizzazioni](media/data-lake-storage-use-power-bi/visualizations.png)

    Le colonne che verranno utilizzate sono la colonna 4 (nome della città) e la colonna 7 (il nome del paese). Trascinare le colonne dalla scheda **Campi** nella scheda **Visualizzazioni** come illustrato di seguito.

    ![Trascinare i campi](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    Il grafico a torta ora dovrebbe essere simile a quello che segue.

    ![Grafico a torta](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Se si seleziona un paese specifico dai filtri a livello di pagina, è possibile visualizzare il numero di conducenti in ogni città del paese selezionato. Ad esempio, nella scheda **Visualizzazioni** selezionare **Brazil** da **Filtri a livello di pagina**.

    ![Filtri di pagina](media/data-lake-storage-use-power-bi/page-filters.png)

15. Il grafico a torta viene automaticamente aggiornato in modo da visualizzare i conducenti delle città del Brasile.

    ![Brasile](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. Dal menu **File** fare clic su **Salva** per salvare la visualizzazione come file di Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Pubblicare report nel servizio Power BI

Dopo aver creato le visualizzazioni in Power BI Desktop, è possibile condividerle con altri mediante la relativa pubblicazione nel servizio Power BI. Per istruzioni sulla procedura, vedere [Pubblicare da Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).