---
title: Analizzare i dati in Data Lake Store con Power BI | Documentazione Microsoft
description: Uso di Power BI per analizzare i dati presenti in Archivio Azure Data Lake
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cd2ef87032d1691f2c56a9da44ce29ccb4e9963


---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analizzare i dati in Archivio Data Lake con Power BI
Questo articolo spiega come usare Power BI Desktop per analizzare e visualizzare i dati presenti in Archivio Azure Data Lake.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Account di Archivio Data Lake di Azure**. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md). Questo articolo presuppone che sia già stato creato un account di Data Lake Store denominato **mybidatalakestore** e sia stato caricato un file di dati di esempio (**Drivers.txt**) nell'account. Il file di esempio può essere scaricato dal [repository Git di Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Può essere scaricato dall'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Creare un report in Power BI Desktop
1. Avviare Power BI Desktop sul computer.
2. Nella barra multifunzione **Home** fare clic su **Recupera dati**, quindi su Altro. Nella finestra di dialogo **Recupera dati** fare clic su **Azure**, **Azure Data Lake Store** e quindi su **Connetti**.
   
    ![Connessione ad Archivio Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Connect to Data Lake Store")
3. Se viene visualizzata una finestra di dialogo relativa al connettore in una fase di sviluppo, scegliere di continuare.
4. Nella finestra di dialogo **Microsoft Azure Data Lake Store** indicare l'URL al proprio account di Data Lake Store e fare clic su **OK**.
   
    ![URL per Archivio Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL for Data Lake Store")
5. Nella finestra di dialogo successiva fare clic su **Accedi** per accedere all'account di Archivio Data Lake. Si verrà reindirizzati alla pagina di accesso dell'organizzazione. Seguire le istruzioni per accedere all'account.
   
    ![Accesso ad Archivio Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Sign into Data Lake Store")
6. Dopo aver completato l'accesso, fare clic su **Connetti**.
   
    ![Connessione ad Archivio Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Connect to Data Lake Store")
7. Nella finestra di dialogo successiva viene visualizzato il file caricato nell'account di Archivio Data Lake. Verificare le informazioni e fare clic su **Carica**.
   
    ![Caricare dati da Archivio Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Load data from Data Lake Store")
8. Dopo il caricamento dei dati in Power BI, nella scheda **Campi** vengono visualizzati i campi indicati di seguito.
   
    ![Campi importati](./media/data-lake-store-power-bi/imported-fields.png "Imported fields")
   
    Al fine della visualizzazione e dell'analisi è tuttavia preferibile che i dati siano organizzati in questi campi:
   
    ![Campi desiderati](./media/data-lake-store-power-bi/desired-fields.png "Desired fields")
   
    La procedura che segue descrive come aggiornare la query per convertire i dati importati nel formato desiderato.
9. Nella barra multifunzione **Home** fare clic su **Modifica query**.
   
    ![Modifica query](./media/data-lake-store-power-bi/edit-queries.png "Edit queries")
10. Nell'editor di query fare clic su **Binario** nella colonna **Contenuto**.
    
    ![Modifica query](./media/data-lake-store-power-bi/convert-query1.png "Edit queries")
11. Viene visualizzata un'icona che rappresenta il file **Drivers.txt** caricato. Fare clic sul file con il pulsante destro del mouse e scegliere **CSV**.    
    
    ![Modifica query](./media/data-lake-store-power-bi/convert-query2.png "Edit queries")
12. L'output sarà come quello illustrato di seguito. I dati ora sono disponibili in un formato che può essere usato per creare visualizzazioni.
    
    ![Modifica query](./media/data-lake-store-power-bi/convert-query3.png "Edit queries")
13. Dalla barra multifunzione **Home** fare clic su **Chiudi e applica**, quindi su **Chiudi e applica**.
    
    ![Modifica query](./media/data-lake-store-power-bi/load-edited-query.png "Edit queries")
14. Dopo l'aggiornamento della query, la scheda **Campi** contiene i nuovi campi disponibili per la visualizzazione.
    
    ![Campi aggiornati](./media/data-lake-store-power-bi/updated-query-fields.png "Updated fields")
15. È possibile creare un grafico a torta per rappresentare i conducenti in ogni città per un determinato paese. Per eseguire questa operazione, effettuare le selezioni seguenti.
    
    1. Nella scheda Visualizzazioni fare clic sul simbolo del grafico a torta.
       
        ![Creazione di un grafico a torta](./media/data-lake-store-power-bi/create-pie-chart.png "Create pie chart")
    2. Le colonne che verranno usate sono la **colonna 4** (nome della città) e la **colonna 7** (nome del paese). Trascinare le colonne dalla scheda **Campi** nella scheda **Visualizzazioni** come illustrato di seguito.
       
        ![Creare visualizzazioni](./media/data-lake-store-power-bi/create-visualizations.png "Create visualizations")
    3. Il grafico a torta ora dovrebbe essere simile a quello che segue.
       
        ![Grafico a torta](./media/data-lake-store-power-bi/pie-chart.png "Create visualizations")
16. Se si seleziona un paese specifico dai filtri a livello di pagina, è possibile visualizzare il numero di conducenti in ogni città del paese selezionato. Ad esempio, nella scheda **Visualizzazioni** selezionare **Brazil** da **Filtri a livello di pagina**.
    
    ![Selezionare un paese](./media/data-lake-store-power-bi/select-country.png "Select a country")
17. Il grafico a torta viene automaticamente aggiornato in modo da visualizzare i conducenti delle città del Brasile.
    
    ![Conducenti in un paese](./media/data-lake-store-power-bi/driver-per-country.png "Drivers per country")
18. Dal menu **File** fare clic su **Salva** per salvare la visualizzazione come file di Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Pubblicare report nel servizio Power BI
Dopo aver creato le visualizzazioni in Power BI Desktop, è possibile condividerle con altri utenti pubblicandole nel servizio Power BI. Per istruzioni sulla procedura, vedere [Pubblicare da Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Vedere anche
* [Analizzare i dati in Archivio Data Lake con Analisi Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)




<!--HONumber=Nov16_HO3-->


