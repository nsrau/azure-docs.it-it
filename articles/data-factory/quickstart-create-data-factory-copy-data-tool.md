---
title: Copiare i dati tramite lo strumento Copia dati di Azure | Microsoft Docs
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per copiare i dati da una cartella in un archivio BLOB di Azure a un'altra cartella.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: c17e738e3db18503f7cdda24a5f01ceb78e4e6a3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="use-copy-data-tool-to-copy-data"></a>Usare lo strumento Copia dati per copiare i dati 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione 2 - Anteprima](quickstart-create-data-factory-copy-data-tool.md)

In questa guida introduttiva si userà il portale di Azure per creare una data factory. Viene quindi usato lo strumento Copia dati per creare una pipeline che copia i dati da una cartella in un archivio BLOB di Azure a un'altra cartella. 

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](data-factory-introduction.md) prima di seguire la guida introduttiva. 
>
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialDataFactory, e riprovare. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
     ![Il nome non è disponibile - Errore](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
      Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre località/aree.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data Factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata. 

## <a name="launch-copy-data-tool"></a>Avviare lo strumento Copia dati

1. Nella pagina delle attività iniziali fare clic sul riquadro **Copia dati** per avviare lo strumento Copia dati. 

   ![Riquadro dello strumento Copia dati](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** dello strumento Copia dati fare clic su **Avanti**. È possibile specificare un nome per la pipeline e la rispettiva descrizione in questa pagina. 

    ![Strumento Copia dati - Pagina Proprietà](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Nella pagina **Source data store** (Archivio dati di origine) selezionare **Archivio BLOB di Azure** e quindi fare clic su **Avanti**.

    ![Pagina Archivio dati di origine](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure) selezionare il **Nome account di archiviazione** dall'elenco a discesa e fare clic su Avanti. 

    ![Specificare l'account di archiviazione BLOB](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) seguire questa procedura:

    1. Passare alla cartella **adftutorial/input**. 
    2. Selezionare il file **emp.txt**.
    3. Fare clic su **Scegli**. È possibile fare doppio clic su **emp.txt** per ignorare questo passaggio. 
    4. Fare clic su **Avanti**. 

    ![Scegliere il file o la cartella di input](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. Nella pagina **File format settings** (Impostazioni di formato file) si noti che lo strumento rileva automaticamente i delimitatori di colonna e di riga, quindi fare clic su **Avanti**. È anche possibile visualizzare l'anteprima dei dati e lo schema dei dati di input in questa pagina. 

    ![Pagina di impostazioni del formato del file](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. Nella pagina **Destination data store** (Archivio dati di destinazione) selezionare **Archivio BLOB di Azure** e quindi fare clic su **Avanti**. 

    ![Pagina dell'archivio dati di destinazione](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure) selezionare l'account di archiviazione BLOB di Azure e fare clic su **Avanti**. 

    ![Pagina per specificare l'archivio dati sink](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) seguire questa procedura: 

    1. Immettere **adftutorial/output** per **Percorso cartella**.
    2. Immettere **emp.txt** per **nome file**. 
    3. Fare clic su **Avanti**. 

    ![Scegliere il file o la cartella di output](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. Nella pagina **File format settings** (Impostazioni di formato file) fare clic su **Avanti**. 

    ![Pagina di impostazioni del formato del file](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Fare clic su **Avanti** nella pagina **Impostazioni**. 

    ![Pagina di impostazioni avanzate](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Verificare tutte le impostazioni nella pagina **Riepilogo** e fare clic su Avanti. 

    ![Pagina Riepilogo](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Nella pagina **Distribuzione completata** fare clic su **Monitoraggio** per monitorare la pipeline creata. 

    ![Pagina Distribuzione](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. L'applicazione passa alla scheda **Monitoraggio**. In questa pagina viene visualizzato lo stato della pipeline. Fare clic su **Aggiorna** per aggiornare l'elenco. 
    
    ![Pagina di monitoraggio delle esecuzioni della pipeline](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna Azioni. La pipeline ha solo un'attività di tipo **Copia**. 

    ![Pagina di esecuzioni di attività](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Per visualizzare informazioni dettagliate sull'operazione di copia, fare clic sul collegamento **Dettagli** (immagine degli occhiali) nella colonna **Azioni**. Per informazioni dettagliate sulle proprietà, vedere [Panoramica dell'attività Copia](copy-activity-overview.md). 

    ![Dettagli dell'operazione di copia](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Verificare che il file **emp.txt** sia stato creato nella cartella **output** del contenitore **adftutorial**. Se la cartella output non esiste, il servizio Data Factory la crea automaticamente. 
18. Passare alla scheda **Modifica** per potere modificare i servizi collegati, i set di dati e le pipeline. Per informazioni sulla modifica di questi elementi nell'interfaccia utente di Data Factory, vedere [Creare una data factory con il portale di Azure](quickstart-create-data-factory-portal.md).

    ![Scheda Modifica](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-portal.md). 