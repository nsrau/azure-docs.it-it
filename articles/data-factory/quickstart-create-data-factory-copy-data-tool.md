---
title: Copiare i dati usando lo strumento Copia dati di Azure | Microsoft Docs
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per copiare i dati da una posizione dell'archivio BLOB di Azure a un'altra posizione.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: aea76544f244adba8368a1d8fbe268746060b5a6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Usare lo strumento Copia dati per copiare i dati 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione 2 - Anteprima](quickstart-create-data-factory-copy-data-tool.md)

In questa guida introduttiva si userà il portale di Azure per creare una data factory. Viene quindi usato lo strumento Copia dati per creare una pipeline che copia i dati da una cartella dell'archivio BLOB di Azure a un'altra cartella. 

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](data-factory-introduction.md) prima di seguire la guida introduttiva. 
>
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio con disponibilità generale, vedere l'[Introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Scegliere **Nuovo** dal menu a sinistra, selezionare **Dati e analisi** e quindi selezionare **Data Factory**. 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
   ![Pagina "Nuova data factory"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, **&lt;nomeutente&gt;ADFTutorialDataFactory**, e provare di nuovo a crearla. Per le regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
   ![Errore quando un nome non è disponibile](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Per **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
4. In **Gruppo di risorse** eseguire una di queste operazioni:
     
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco. 
   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
   Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Per **Versione** selezionare **V2 (anteprima)**.
5. Per **Località** selezionare la località per la data factory. 

   L'elenco include solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (ad esempio, HDInsight) usati da Data Factory possono trovarsi in altre località/aree.

6. Selezionare **Aggiungi al dashboard**.     
7. Selezionare **Create**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory): 

    ![Riquadro "Deploying data factory" (Distribuzione della data factory)](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Azure Data Factory in una scheda separata.
   
   ![Home page della data factory, con il riquadro "Crea e monitora"](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Avviare lo strumento Copia dati

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati. 

   ![Riquadro "Copia dati"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** dello strumento Copia dati selezionare **Avanti**. È possibile specificare un nome per la pipeline e la rispettiva descrizione in questa pagina. 

   ![Pagina "Proprietà"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Nella pagina **Source data store** (Archivio dati di origine) selezionare **Archiviazione BLOB di Azure** e quindi selezionare **Avanti**.

   ![Pagina "Source data store" (Archivio dati di origine)](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure) selezionare l'account di archiviazione nell'elenco **Nome account di archiviazione** e quindi selezionare **Avanti**. 

   ![Pagina "Specify the Azure Blob storage account" (Specificare l'account di archiviazione BLOB di Azure)](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:

   a. Passare alla cartella **adftutorial/input**.

   b. Selezionare il file **emp.txt**.

   c. Selezionare **Scegli**. È possibile fare doppio clic su **emp.txt** per ignorare questo passaggio.

   d. Selezionare **Avanti**. 

   ![Pagina "Choose the input file or folder" (Scegliere il file o la cartella di input)](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. Nella pagina **File format settings** (Impostazioni di formato file) si noti che lo strumento rileva automaticamente i delimitatori di colonna e di riga, quindi selezionare **Avanti**. È anche possibile visualizzare l'anteprima dei dati e gli schemi dei dati di input in questa pagina. 

   ![Pagina "File format settings" (Impostazioni di formato file)](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. Nella pagina **Destination data store** (Archivio dati di destinazione) selezionare **Archiviazione BLOB di Azure** e quindi selezionare **Avanti**. 

   ![Pagina "Destination data store" (Archivio dati di destinazione)](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure) selezionare l'account di archiviazione BLOB di Azure e quindi selezionare **Avanti**. 

   ![Pagina "Specify the Azure Blob storage account" (Specificare l'account di archiviazione BLOB di Azure)](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) completare questa procedura: 

   a. Immettere **adftutorial/output** come percorso della cartella.

   b. Immettere **emp.txt** come nome file.

   c. Selezionare **Avanti**. 

   ![Pagina "Choose the output file or folder" (Scegliere il file o la cartella di output)](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. Nella pagina **File format settings** (Impostazioni di formato file) selezionare **Avanti**. 

    ![Pagina "File format settings" (Impostazioni di formato file)](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Nella pagina **Impostazioni** selezionare **Avanti**. 

    ![Pagina "Impostazioni"](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Verificare tutte le impostazioni nella pagina **Riepilogo** e selezionare **Avanti**. 

    ![Pagina "Riepilogo"](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Nella pagina **Distribuzione completata** selezionare **Monitoraggio** per monitorare la pipeline creata. 

    ![Pagina "Distribuzione completata"](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. L'applicazione passa alla scheda **Monitoraggio**. In questa scheda viene visualizzato lo stato della pipeline. Selezionare **Aggiorna** per aggiornare l'elenco. 
    
    ![Scheda per il monitoraggio delle esecuzioni della pipeline, con il pulsante "Aggiorna"](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. La pipeline ha solo un'attività di tipo **Copia**. 

    ![Elenco delle esecuzioni di attività](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Per visualizzare informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali) nella colonna **Azioni**. Per informazioni dettagliate sulle proprietà, vedere [Panoramica dell'attività Copia](copy-activity-overview.md). 

    ![Dettagli dell'operazione di copia](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Verificare che il file **emp.txt** sia stato creato nella cartella **output** del contenitore **adftutorial**. Se la cartella output non esiste, il servizio Data Factory la crea automaticamente. 
18. Passare alla scheda **Modifica** per poter modificare i servizi collegati, i set di dati e le pipeline. Per informazioni sulla modifica di questi elementi nell'interfaccia utente di Data Factory, vedere [Creare una data factory con il portale di Azure](quickstart-create-data-factory-portal.md).

    ![Scheda Modifica](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra nell'archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, vedere le [esercitazioni](tutorial-copy-data-portal.md). 