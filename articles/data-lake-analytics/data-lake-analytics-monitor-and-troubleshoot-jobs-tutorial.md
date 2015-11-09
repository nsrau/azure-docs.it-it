<properties 
   pageTitle="Risolvere i problemi dei processi di Azure Data Lake Analytics mediante il portale di anteprima di Azure | Azure" 
   description="Informazioni su come usare il portale di anteprima di Azure per risolvere i problemi relativi ai processi di Data Lake Analytics." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/27/2015"
   ms.author="jgao"/>

# Risolvere i problemi dei processi di Azure Data Lake Analytics mediante il portale di anteprima di Azure

Informazioni su come usare il portale di anteprima di Azure per risolvere i problemi relativi ai processi di Data Lake Analytics.

In questa esercitazione verrà impostato un problema relativo a un file di origine mancante e verrà usato il portale di anteprima di Azure per risolvere il problema.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Conoscenza di base dell'elaborazione dei processi di Data Lake Analytics**. Vedere [Introduzione ad Azure Data Lake Analytics tramite il portale di anteprima di Azure](data-lake-analytics-get-started-use-portal.md).
- **Un account di Data Lake Analytics**. Vedere [Introduzione ad Azure Data Lake Analytics tramite il portale di anteprima di Azure](data-lake-analytics-get-started-use-portal.md#create-adl-analytics-account).
- **Copiare i dati di esempio nell'account di Data Lake Store predefinito**. Vedere [Preparare i dati di origine](data-lake-analytics-get-started-use-portal.md.md#prepare-source-data).

##Inviare un processo di Data Lake Analytics

A questo punto verrà creato un processo di U-SQL con un nome di file di origine non valido.

**Per inviare il processo**

1. Nel portale di anteprima di Azure fare clic su **Microsoft Azure** nell'angolo superiore sinistro.
2. Fare clic nel riquadro contenente il nome dell'account di Data Lake Analytics. È stato aggiunto qui durante la creazione dell'account. Se l'account non è stato aggiunto, vedere [Aprire un account di Analytics dal portale](data-lake-analytics-manage-use-portal.md#access-adla-account).
3. Fare clic su **Nuovo processo** nel menu in alto.
4. Immettere un nome per il processo e lo script U-SQL seguente:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    Il file di origine definito nello script è **/Samples/Data/SearchLog.tsv1**, ma dovrebbe essere modificato in **/Samples/Data/SearchLog.tsv**.
     
5. Fare clic su **Invia processo** nel menu in alto. Viene visualizzato un nuovo riquadro Dettagli processo. Sulla barra del titolo viene visualizzato lo stato del processo. Il completamento dell'operazione richiede alcuni minuti. È possibile fare clic **Aggiorna** per visualizzare lo stato più recente.
6. Attendere finché lo stato del processo non viene modificato in **Non riuscito**. Se lo stato del processo è **Riuscito**, significa che la cartella /Samples non è stata rimossa. Vedere la sezione **Prerequisiti** all'inizio dell'esercitazione.

È lecito chiedersi come mai il completamento di un processo così piccolo abbia richiesto molto tempo. Tenere presente che Data Lake Analytics è progettato per l'elaborazione di Big Data. Pertanto è caratterizzato da prestazioni ottimali durante l'elaborazione di una grande quantità di dati usando il relativo sistema distribuito.

Si supponga di aver inviato il processo e chiuso il portale. Nella sezione successiva si apprenderà come risolvere i problemi relativi al processo.


## Risolvere i problemi relativi al processo

Nella sezione precedente il processo è stato inviato e la sua esecuzione ha avuto esito negativo.

**Per visualizzare tutti i processi**

1. Nel portale di Azure fare clic su **Microsoft Azure** nell'angolo superiore sinistro.
2. Fare clic nel riquadro contenente il nome dell'account di Data Lake Analytics. Viene visualizzato il riepilogo del processo nel riquadro **Gestione processo** .

    ![Azure Data Lake Analytics - Gestione dei processi](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)
    
    Nel riquadro Gestione processo viene fornito un riepilogo dello stato del processo. Si noti che è presente un processo non riuscito.
   
3. Fare clic nel riquadro **Gestione processo** per visualizzare i processi. I processi sono classificati in base agli stati **Running**, **Queued** e **Ended**. Il processo non riuscito verrà visualizzato nella sezione **Ended** e occuperà la prima posizione nell'elenco. Quando sono presenti molti processi, è possibile fare clic su **Filtro** per semplificare l'individuazione dei processi.

    ![Azure Data Lake Analytics - Filtrare i processi](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Fare clic sul processo non riuscito nell'elenco per aprire i relativi dettagli in un nuovo pannello:

    ![Azure Data Lake Analytics - Processo non riuscito](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)
    
    Si noti il pulsante **Invia di nuovo**. Dopo aver risolto il problema, è possibile inviare nuovamente il processo.

5. Fare clic sulla parte evidenziata nella schermata precedente per aprire i dettagli dell'errore. Verrà visualizzato qualcosa di simile a quanto segue:

    ![Azure Data Lake Analytics - Dettagli del processo non riuscito](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Indica che la cartella di origine non è disponibile.
    
6. Fare clic su **Duplicate Script**.
7. Aggiornare il percorso nella riga **FROM** impostando il seguente percorso:

    "/Samples/Data/SearchLog.tsv"

8. Fare clic su **Submit Job**.


##Vedere anche

- [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Introduzione ad Azure Data Lake Analytics con Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introduzione ad Azure Data Lake Analytics e U-SQL con Visual Studio](data-lake-analytics-get-started-u-sql-studio.md)
- [Gestire Azure Data Lake Analytics mediante il portale di anteprima di Azure](data-lake-analytics-manage-use-portal.md)

<!---HONumber=Nov15_HO1-->