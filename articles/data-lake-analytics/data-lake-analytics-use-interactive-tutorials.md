<properties 
   pageTitle="Informazioni su Data Lake Analytics e U-SQL usando le esercitazioni interattiva del portale di Azure | Azure" 
   description="Guida introduttiva all'apprendimento di Data Lake Analytics e U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# Usare le esercitazioni interattive di Azure Data Lake Analytics

Il portale di Azure fornisce un'esercitazione interattiva che consente di iniziare a usare Data Lake Analytics. In questo articolo viene illustrato come completare l'esercitazione per analizzare i log del sito Web.


>[AZURE.NOTE] Se si desidera eseguire la stessa esercitazione usando Visual Studio, vedere [Analizzare i log del sito Web mediante Data Lake Analytics](data-lake-analytics-analyze-weblogs.md). Al portale verranno aggiunte altre esercitazioni interattive.


Per altre esercitazioni, vedere:

- [Introduzione a Analisi Data Lake tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
- [Introduzione ad Azure Data Lake Analytics con Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introduzione ad Azure Data Lake Analytics con .NET SDK](data-lake-analytics-get-started-net-sdk.md)
- [Sviluppare script U-SQL mediante Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Un account di Data Lake Analytics**. Vedere [Introduzione ad Analisi Data Lake di Azure con il portale di Azure](data-lake-analytics-get-started-portal.md).

##Creare un account di Analisi Data Lake 

È necessario disporre di un account di Data Lake Analytics prima di poter eseguire qualsiasi processo.

Ogni account di Analisi Data Lake presenta una dipendenza sull'account di [Archivio Data Lake di Azure](../data-lake-store/data-lake-store-overview.md). Questo account viene definito account di Data Lake Store predefinito. È possibile creare l'account di Data Lake Store anticipatamente o quando si crea l'account di Data Lake Analytics. In questa esercitazione si creerà l'account di Data Lake Store con l'account di Analytics.

**Per creare un account di Analisi Data Lake**

1. Accedere al [portale di Azure](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Fare clic su **Microsoft Azure** nell'angolo superiore sinistro per aprire la Schermata iniziale.
3. Fare clic sul riquadro **Marketplace**.
3. Digitare **Azure Data Lake Analytics** nella casella di ricerca del pannello **Tutto** e quindi premere **INVIO**. Nell'elenco verrà visualizzato **Azure Data Lake Analytics**.
4. Fare clic su **Azure Data Lake Analytics** nell'elenco.
5. Fare clic su **Crea** nella parte inferiore del pannello.
6. Immettere o selezionare le informazioni seguenti:

    ![Pannello del portale di Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **Nome**: nome dell'account di Analytics.
	- **Data Lake Store**: ogni account di Data Lake Analytics ha un account di Data Lake Store dipendente. L'account di Data Lake Analytics e l'account di Data Lake Store dipendente devono trovarsi nello stesso data center di Azure. Seguire le istruzioni per creare un nuovo account di Data Lake Store o selezionarne uno esistente.
	- **Sottoscrizione**: scegliere la sottoscrizione di Azure usata per l'account di Analytics.
	- **Gruppo di risorse**. Selezionare un gruppo di risorse di Azure esistente o crearne uno nuovo. Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Gestione risorse di Azure (ARM) consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, staging e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md).
	- **Località**. Selezionare un data center di Azure per l'account di Data Lake Analytics.
7. Selezionare **Aggiungi alla Schermata iniziale**. Questa impostazione è necessaria per l'esecuzione di questa esercitazione.
8. Fare clic su **Crea**. Viene visualizzata la Schermata iniziale del portale. Alla home page viene aggiunto un nuovo riquadro con l'etichetta "Deploying Azure Data Lake Analytics". Il processo di creazione di un account di Data Lake Analytics richiede alcuni istanti. Al termine del processo, il portale aprirà l'account in un nuovo pannello.

	![Analisi Data Lake di Azure - Pannello del portale](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

##Eseguire l'esercitazione interattiva Analisi del log del sito Web

**Per aprire l'esercitazione interattiva Website Log Analytics**

1. Nel portale, fare clic su **Microsoft Azure** dal menu a sinistra per aprire la Schermata iniziale.
2. Fare clic nel riquadro collegato all'account di Data Lake Analytics.
3. Fare clic su **Explore interactive tutorials** sulla barra **Essentials**.

	![Esercitazioni interattive di Data Lake Analytics](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)

4. Se viene visualizzato un avviso di colore arancione indicante "Samples not set up, click...", fare clic su **Copy Sample Data** per copiare i dati di esempio nell'account di Data Lake Store predefinito. L'esercitazione interattiva richiede i dati per l'esecuzione.
5. Nel pannello **Interactive Tutorials** fare clic su **Website Log Analytics**. Il portale aprirà l'esercitazione in un nuovo pannello.
5. Fare clic su **1 Introduction** e quindi seguire le istruzioni.

##Vedere anche

- [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introduzione a Analisi Data Lake tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
- [Introduzione ad Azure Data Lake Analytics con Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Sviluppare script U-SQL mediante Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Analizzare i log dei siti Web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)

<!---HONumber=AcomDC_0914_2016-->