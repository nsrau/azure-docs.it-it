<properties 
	pageTitle="Esercitazione: Creare una pipeline usando la Copia guidata" 
	description="In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando la Copia guidata supportata da Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/01/2016" 
	ms.author="spelluru"/> 

# Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory
> [AZURE.SELECTOR]
- [Panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Con l'editor di Data factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Tramite PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Con Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Uso dell'API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Con la Copia guidata](data-factory-copy-data-wizard-tutorial.md)

In questa esercitazione viene usata la Copia guidata di Data Factory per creare una pipeline con l'attività di copia in una data factory. Per prima cosa, si crea una data factory usando il portale di Azure. Successivamente, si usa la Copia guidata per creare servizi collegati, set di dati e una pipeline di Data Factory con un'attività di copia che copia i dati da un archivio BLOB di Azure a un database SQL di Azure. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md).

> [AZURE.IMPORTANT] Leggere l'articolo [Panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e completare i prerequisiti prima di eseguire questa esercitazione.

## Creare un'istanza di Data Factory
In questo passaggio viene usato il portale di Azure per creare un'istanza di Azure Data Factory denominata **ADFTutorialDataFactory**.

1.	Dopo l'accesso al [portale di Azure](https://portal.azure.com), fare clic su **+ NUOVO** nell'angolo in alto a sinistra, selezionare **Analisi dei dati** nel pannello **Crea**, quindi fare clic su **Data Factory** nel pannello **Analisi dei dati**.

	![Nuovo->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png) 

6. Nel pannello **Nuova data factory**:
	1. Immettere **ADFTutorialDataFactory** come **nome**.
	
  		![Pannello Nuova data factory](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png) 
	2. Fare clic su **NOME DEL GRUPPO DI RISORSE** ed eseguire queste operazioni:
		1. Fare clic su **Crea un nuovo gruppo di risorse**.
		2. Nel pannello **Crea gruppo di risorse** immettere **ADFTutorialResourceGroup** come **nome** del gruppo di risorse e fare clic su **OK**.

			![Crea gruppo di risorse](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png) 

		Alcuni dei passaggi di questa esercitazione presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../resource-group-overview.md).
7. Si noti che l'opzione **Aggiungi a Schermata iniziale** è selezionata nel pannello **Nuova data factory**.
8. Fare clic su **Crea** nel pannello **Nuova data factory**.

	È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato un errore simile a **Nome "ADFTutorialDataFactory" per la data factory non disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteADFTutorialDataFactory) e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md).
	 
	![Nome di data factory non disponibile](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png) 
	
	> [AZURE.NOTE] Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.

9. Fare clic sull'hub **NOTIFICHE** a sinistra e cercare le notifiche del processo di creazione. Fare clic su **X** per chiudere il pannello **NOTIFICHE**, se è aperto.
10. Al termine della creazione verrà visualizzato il pannello **DATA FACTORY**, come illustrato nell'immagine seguente.

    ![Home page di Data factory](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)  

## Creare una pipeline

1. Nella home page di Data Factory fare clic sul riquadro **Copia dati** per avviare la **Copy Wizard** (Copia guidata).

	> [AZURE.NOTE] Se il Web browser è bloccato su "Concessione autorizzazioni in corso...", disabilitare/deselezionare l'impostazione **Block third party cookies and site data** (Blocca cookie e dati del sito di terze parti) oppure lasciarla abilitata e creare un'eccezione per **login.microsoftonline.com** e quindi provare di nuovo ad avviare la procedura guidata.
2. Nella pagina **Proprietà**:
	1. Immettere **CopyFromBlobToAzureSql** per **Nome attività**.
	2. Immettere una **descrizione** (facoltativo).
	3. Annotare i valori per **Data/Ora di inizio** e **Data/Ora di fine**. Modificare il valore di **Data/Ora di fine** per impostarlo sul giorno successivo a **Data/Ora di inizio**.
	3. Fare clic su **Next**.

	![Strumento di copia - Pagina Proprietà](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Nella pagina **Source data store** (Archivio dati di origine) fare clic sul riquadro **Archivio BLOB di Azure**. Usare questa pagina per specificare l'archivio dati di origine per l'attività di copia. È possibile usare un servizio collegato di archivio dati esistente oppure specificare un nuovo archivio dati. Per usare un servizio collegato esistente, fare clic su **FROM EXISTING LINKED SERVICES** (DA SERVIZI COLLEGATI ESISTENTI) e selezionare il servizio collegato corretto.

	![Strumento di copia - Pagina Archivio dati di origine](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)  
5. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure):
	1. Immettere **AzureStorageLinkedService** per **Nome del servizio collegato**.
	2. Confermare il valore **From Azure subscriptions** (Da sottoscrizioni di Azure) per **Account selection method** (Metodo di selezione dell'account).
	3. Selezionare un **Account di archiviazione di Azure** dall'elenco di quelli disponibili nella sottoscrizione selezionata. È anche possibile scegliere di immettere manualmente le impostazioni dell'account di archiviazione, selezionando l'opzione **Immetti manualmente** per il **Account selection method** (Metodo di selezione dell'account), quindi fare clic su **Avanti**.

	![Strumento di copia - Specificare l'account di archiviazione BLOB di Azure](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png) 
6. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input):
	1. Passare alla cartella **adftutorial**.
	2. Selezionare **emp.txt** e fare clic su **Scegli**.
	3. Fare clic su **Next**.

	![Strumento di copia - Scegliere il file o la cartella di input](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png) 
7. Nella pagina **File format settings** (Impostazioni di formattazioni del file) selezionare i valori per le impostazioni **predefinite** e fare clic su **Avanti**.

	![Strumento di copia - Impostazioni di formattazioni del file](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png) 
8. Nella pagina dell'archivio dati di destinazione fare clic sul riquadro **Database SQL di Azure** e quindi su **Avanti**.
9. Nella pagina **Specify the Azure SQL database** (Specificare il database SQL di Azure):
	1. Immettere **AzureSqlLinkedService** nel campo **Nome del servizio collegato**.
	2. Verificare che il **Server/database selection method** (Metodo di selezione del server/database) sia impostato su **From Azure subscriptions** (Da sottoscrizioni di Azure).
	3. Selezionare **Nome server** e **Database**.
	4. Immettere un **Nome utente** e una **Password**.
	5. Fare clic su **Next**.
9. Nella pagina **Mapping tabella** selezionare **emp** dall'elenco a discesa per il campo **Destinazione**, fare clic sulla **freccia giù** (facoltativo) per visualizzare lo schema e l'anteprima dei dati.

	![Strumento di copia - Mapping tabella](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png)
10. Nella pagina **Mapping dello schema** fare clic su **Avanti**.
11. Verificare le informazioni nella pagina **Riepilogo** e fare clic su **Fine**. La procedura guidata crea due servizi collegati, due set di dati (input e output) e una pipeline nella data factory da cui è stata avviata la Copia guidata.
12. Nella pagina **La distribuzione è riuscita** fare clic su **Click here to monitor copy pipeline** (Fare clic qui per monitorare la pipeline di copia).

	![Strumento di copia - La distribuzione è riuscita](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
13. Per informazioni su come monitorare la pipeline creata, vedere [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

	![App di monitoraggio](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)  
 

## Vedere anche
| Argomento | Description |
| :---- | :---- |
| [Attività di spostamento dei dati](data-factory-data-movement-activities.md) | Questo articolo fornisce informazioni dettagliate sull'attività di copia usata nell'esercitazione. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo fornisce informazioni sui set di dati in Azure Data Factory.
| [Monitorare e gestire le pipeline con l'app di monitoraggio](data-factory-monitor-manage-app.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app di monitoraggio e gestione. 

<!---HONumber=AcomDC_0907_2016-->