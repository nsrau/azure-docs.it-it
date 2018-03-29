---
title: 'Esercitazione: Creare una pipeline usando la Copia guidata | Documentazione Microsoft'
description: In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando la Copia guidata supportata da Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8407249ed56059f278f8e4f84a4466ab286a0c24
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
> * [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere l'[esercitazione sull'attività di copia nella documentazione sulla versione 2](../quickstart-create-data-factory-dot-net.md). 


Questa esercitazione illustra come usare la **copia guidata** per copiare i dati da un archivio BLOB di Azure a un database SQL di Azure. 

La **copia guidata** di Azure Data Factory consente di creare rapidamente una pipeline di dati che copia i dati da un archivio dati di origine supportato a un archivio dati di destinazione supporto. È quindi consigliabile usare la procedura guidata come primo passaggio per creare una pipeline di esempio per lo scenario di spostamento dei dati. Per un elenco degli archivi dati supportati come origini e come destinazioni, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

Questa esercitazione illustra come creare una data factory di Azure, avviare la Copia guidata ed eseguire una serie di passaggi per specificare i dettagli relativi allo scenario di inserimento/spostamento dei dati. Al termine dei passaggi della procedura guidata, verrà creata automaticamente una pipeline con un'attività di copia per copiare i dati da un archivio BLOB di Azure a un database SQL di Azure. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>prerequisiti
Prima di eseguire questa esercitazione, completare i prerequisiti indicati nella [panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

## <a name="create-data-factory"></a>Creare un'istanza di Data Factory
In questo passaggio viene usato il portale di Azure per creare un'istanza di Azure Data Factory denominata **ADFTutorialDataFactory**.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Crea una risorsa** nell'angolo in alto a sinistra, quindi su **Dati e analisi** e infine su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. Nel pannello **Nuova data factory** :
   
   1. Immettere **ADFTutorialDataFactory** come **nome**.
       È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore `Data factory name “ADFTutorialDataFactory” is not available`, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialDataFactoryAAAAMMGG, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md) .  
      
       ![Nome di data factory non disponibile](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Selezionare la **sottoscrizione**di Azure.
   3. In Gruppo di risorse eseguire una di queste operazioni: 
      
      - Selezionare **Usa esistente** per scegliere un gruppo di risorse esistente.
      - Selezionare **Crea nuovo** per immettere un nome per un gruppo di risorse.
          
        Alcuni dei passaggi di questa esercitazione presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../../azure-resource-manager/resource-group-overview.md).
   4. Selezionare una **località** per la data factory.
   5. Selezionare la casella di controllo **Aggiungi al dashboard** nella parte inferiore del pannello.  
   6. Fare clic su **Crea**.
      
       ![Pannello Nuova data factory](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Al termine della creazione verrà visualizzato il pannello **Data factory**, come illustrato nell'immagine seguente:
   
   ![Home page di Data factory](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Avviare la Copia guidata
1. Nel pannello Data Factory fare clic su **Copia dati (ANTEPRIMA)** per avviare **Copy Wizard** (Copia guidata). 
   
   > [!NOTE]
   > Se il Web browser è bloccato su "Concessione autorizzazioni in corso...", disabilitare/deselezionare l'impostazione **Block third party cookies and site data** (Blocca cookie e dati del sito di terze parti) nelle impostazioni del browser oppure lasciarla abilitata e creare un'eccezione per **login.microsoftonline.com** e quindi provare di nuovo ad avviare la procedura guidata.
2. Nella pagina **Proprietà** :
   
   1. Immettere **CopyFromBlobToAzureSql** per **Nome attività**.
   2. Immettere una **descrizione** (facoltativo).
   3. Modificare **Start date time** (Data e ora di inizio) ed **End date time** (Data e ora di fine) in modo che la data di fine corrisponda alla data odierna e la data di inizio a cinque giorni prima.  
   4. Fare clic su **Avanti**.  
      
      ![Strumento di copia - Pagina Proprietà](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Nella pagina **Source data store** (Archivio dati di origine) fare clic sul riquadro **Archivio BLOB di Azure**. Usare questa pagina per specificare l'archivio dati di origine per l'attività di copia. 
   
    ![Strumento di copia - Pagina Archivio dati di origine](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure):
   
   1. Immettere **AzureStorageLinkedService** per **Nome del servizio collegato**.
   2. Verificare che in **Account selection method** (Metodo di selezione dell'account) sia selezionata l'opzione **From Azure subscriptions** (Da sottoscrizioni di Azure).
   3. Selezionare la **sottoscrizione**di Azure.  
   4. Selezionare un **Account di archiviazione di Azure** nell'elenco di quelli disponibili nella sottoscrizione selezionata. È anche possibile scegliere di immettere manualmente le impostazioni dell'account di archiviazione, selezionando l'opzione **Immetti manualmente** per **Account selection method** (Metodo di selezione dell'account), quindi fare clic su **Avanti**. 
      
      ![Strumento di copia - Specificare l'account di archiviazione BLOB di Azure](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input):
   
   1. Fare doppio clic sulla cartella **adftutorial**.
   2. Selezionare **emp.txt** e fare clic su **Scegli**.
      
      ![Strumento di copia - Scegliere il file o la cartella di input](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) fare clic su **Next** (Avanti). Non selezionare **Binary copy**(Copia binaria). 
   
    ![Strumento di copia - Scegliere il file o la cartella di input](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. Nella pagina **File format settings** (Impostazioni di formato file) vengono visualizzati i delimitatori e lo schema rilevati automaticamente dalla procedura guidata analizzando il file. È anche possibile immettere i delimitatori manualmente per sostituirli o interrompere il rilevamento automatico nella Copia guidata. Dopo aver esaminato i delimitatori e i dati di anteprima, fare clic su **Next** (Avanti). 
   
    ![Strumento di copia - Impostazioni di formattazioni del file](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Nella pagina Destination data store (Archivio dati di destinazione) selezionare **Azure SQL Database** (Database SQL di Azure) e quindi fare clic su **Next** (Avanti).
   
    ![Strumento di copia - Scegliere l'archivio di destinazione](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. Nella pagina **Specify the Azure SQL database** (Specificare il database SQL di Azure):
   
   1. Immettere **AzureSqlLinkedService** nel campo **Connection name** (Nome connessione).
   2. Verificare che in **Server / database selection method** (Metodo di selezione del server/database) sia selezionata l'opzione **From Azure subscriptions** (Da sottoscrizioni di Azure).
   3. Selezionare la **sottoscrizione**di Azure.  
   4. Selezionare **Nome server** e **Database**.
   5. Immettere un **Nome utente** e una **Password**.
   6. Fare clic su **Avanti**.  
      
      ![Strumento di copia - Specificare il database SQL di Azure](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. Nella pagina **Mapping tabella** selezionare **emp** dall'elenco a discesa per il campo **Destinazione**, fare clic sulla **freccia giù** (facoltativo) per visualizzare lo schema e l'anteprima dei dati.
    
     ![Strumento di copia - Mapping tabella](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. Nella pagina **Mapping dello schema** fare clic su **Avanti**.
    
    ![Strumento di copia - Mapping dello schema](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. Nella pagina **Prestazioni** fare clic su **Avanti**. 
    
    ![Strumento di copia - Impostazioni relative alle prestazioni](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Verificare le informazioni nella pagina **Riepilogo** e fare clic su **Fine**. La procedura guidata crea due servizi collegati, due set di dati (input e output) e una pipeline nella data factory da cui è stata avviata la Copia guidata. 
    
    ![Strumento di copia - Impostazioni relative alle prestazioni](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Avviare l'applicazione di monitoraggio e gestione
1. Nella pagina **Distribuzione** fare clic sul collegamento: `Click here to monitor copy pipeline`.
   
   ![Strumento di copia - La distribuzione è riuscita](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. L'applicazione di monitoraggio viene avviata in una scheda separata del Web browser.   
   
   ![App di monitoraggio](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Per visualizzare lo stato più recente delle sezioni orarie, fare clic sul pulsante **Aggiorna** nell'elenco **ACTIVITY WINDOWS** (Finestre attività) nella parte inferiore. Vengono visualizzate cinque finestre attività relative a cinque giorni compresi tra le ore di inizio e di fine della pipeline. L'elenco non viene aggiornato automaticamente, quindi potrebbe essere necessario fare clic su Aggiorna un paio di volte prima di visualizzare tutte le finestre attività con lo stato Pronto. 
4. Selezionare una finestra attività nell'elenco. Visualizzarne i dettagli in **Activity Window Explorer** (Esplora finestre attività) a destra.

    ![Dettagli finestra attività](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Si noti che le date 11, 12, 13, 14 e 15 sono di colore verde per indicare che le sezioni di output giornaliere di queste date sono già stata generate. Questa codifica a colori viene usata anche nella pipeline e nel set di dati di output nella vista diagramma. Nel passaggio precedente si noti che due sezioni sono già state generate, una sezione è attualmente in fase di elaborazione e le altre due sono in attesa di essere elaborate (in base alla codifica a colori). 

    Per altre informazioni sull'uso di questa applicazione, vedere l'articolo [Monitorare e gestire le pipeline di Azure Data Factory con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati usati l'archivio BLOB di Azure come archivio dati di origine e un database SQL di Azure come archivio dati di destinazione in un'operazione di copia. La tabella seguente contiene un elenco degli archivi dati supportati come origini e come destinazioni dall'attività di copia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Per informazioni dettagliate sui campi o sulle proprietà visualizzate durante la copia guidata di un archivio dati, fare clic sul collegamento relativo all'archivio dati nella tabella. 