<properties 	
	pageTitle="Data factory di Azure - Esempi" 
	description="Fornisce informazioni dettagliate sugli esempi inclusi nel servizio Data factory di Azure." 
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
	ms.topic="article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Data factory di Azure - Esempi

## Esempi in GitHub
Il [repository Azure-DataFactory di GitHub](https://github.com/azure/azure-datafactory) include alcuni esempi che permettono di acquisire rapidamente familiarità con il servizio Data factory di Azure (o) modificare gli script e usarlo nella propria applicazione. La cartella Samples\\JSON include frammenti di codice JSON per scenari comuni.

## Esempi nel portale di Azure
È possibile usare il riquadro **Pipeline di esempio** nella home page della data factory per distribuire le pipeline di esempio e le entità associate (set di dati e servizi collegati) nella data factory.

1. Creare una nuova data factory o aprire una data factory esistente. Per informazioni su come creare una data factory, vedere [Introduzione a Data factory di Azure](data-factory-get-started.md#CreateDataFactory).
2. Nel pannello **DATA FACTORY** per la data factory, fare clic sul riquadro **Pipeline di esempio**.

	![Riquadro Pipeline di esempio](./media/data-factory-samples/SamplePipelinesTile.png)

2. Nel pannello **Pipeline di esempio**, fare clic sull'**esempio** da distribuire.
	
	![Pannello Pipeline di esempio](./media/data-factory-samples/SampleTile.png)

3. Specificare le impostazioni di configurazione per l'esempio, ad esempio il nome dell'account di archiviazione di Azure e la chiave dell'account, il nome del server di Azure SQL, il database, l'ID utente, la password e così via.

	![Pannello Esempio](./media/data-factory-samples/SampleBlade.png)

4. Dopo avere specificato le impostazioni di configurazione, fare clic su **Crea** per creare/distribuire le pipeline di esempio e i servizi collegati o le tabelle usati dalle pipeline.
5. Lo stato della distribuzione sarà visualizzato sul riquadro dell'esempio selezionato in precedenza nel pannello **Pipeline di esempio**.

	![Stato della distribuzione](./media/data-factory-samples/DeploymentStatus.png)

6. Quando viene visualizzato il messaggio **Distribuzione riuscita** nel riquadro relativo all'esempio, chiudere il pannello **Pipeline di esempio**.
5. Nel pannello **DATA FACTORY** si potrà notare che i servizi collegati, i set di dati e le pipeline vengono aggiunti alla data factory.  

	![Pannello Data factory](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## Esempi in Visual Studio

### Prerequisiti

È necessario disporre dei seguenti prodotti installati nel computer in uso:

- Visual Studio 2013 o Visual Studio 2015
- Download di Azure SDK per Visual Studio 2013 o Visual Studio 2015. Passare alla [pagina di download di Azure](https://azure.microsoft.com/downloads/) e fare clic su **VS 2013** o **VS 2015** nella sezione **.NET**.
- Scaricare il plug-in Data Factory di Azure più recente per Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [2015 VS](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se si usa Visual Studio 2013, è anche possibile aggiornare il plug-in nel modo seguente: nel menu fare clic su **Strumenti** -> **Estensioni e aggiornamenti** -> **Online** -> **Visual Studio Gallery** -> **Strumenti di Data factory di Azure per Visual Studio** -> **Aggiorna**.

### Usare Modelli di Data Factory

1. Fare clic su **File** nel menu, scegliere **Nuovo** e quindi fare clic su **Progetto**. 
2. Nella finestra di dialogo **Nuovo progetto** seguire questa procedura: 
	1. Selezionare **DataFactory** in **Modelli**. 
	2. Selezionare **Modelli di Data Factory** nel riquadro di destra. 
	3. Immettere un **nome** per il progetto. 
	4. Selezionare una **località** per il progetto. 
	5. Fare clic su **OK**. 

	![Finestra di dialogo Nuovo progetto](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. Nella finestra di dialogo **Modelli di Data Factory** selezionare il modello di esempio dalla sezione **Modelli del caso di utilizzo** e fare clic su **Avanti**. I passaggi seguenti forniscono istruzioni dettagliate all'uso del modello **Profiling cliente**. I passaggi sono simili ad altri esempi. 

	![Finestra di dialogo Modelli di Data Factory](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. Nella finestra di dialogo **Configurazione di Data Factory** fare clic su **Avanti** nella pagina **Informazioni di base di Data Factory**.
8. Nella pagina **Configura data factory** seguire questa procedura: 
	1. Selezionare **Crea nuova data factory** ai fini di questa procedura dettagliata. In alternativa, selezionare **Usa data factory esistente**.
	2. Immettere un **nome** per la data factory.
	3. Selezionare la **sottoscrizione di Azure** in cui creare la data factory. 
	4. Selezionare il **gruppo di risorse** per la data factory.
	5. Selezionare **Stati Uniti occidentali**, **Stati Uniti orientali** o **Europa settentrionale** per l'**area**.
	6. Fare clic su **Avanti**. 
9. Nella pagina **Configura archivi dati** specificare un **database SQL di Azure** e un **account di archiviazione di Azure** esistenti oppure crearne di nuovi, quindi scegliere Avanti. 
10. Nella pagina **Configura calcolo** selezionare le impostazioni predefinite e fare clic su **Avanti**. 
11. Nella pagina **Riepilogo** esaminare tutte le impostazioni e fare clic su **Avanti**. 
12. Nella pagina **Stato distribuzione** attendere finché non viene completata la distribuzione e fare clic su **Fine**.
13. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**. 
19. Se viene visualizzato **Accedere al proprio account Microsoft** nella finestra di dialogo immettere le credenziali per l'account che dispone di sottoscrizione di Azure e fare clic su **accedi**.
20. Verrà visualizzata la finestra di dialogo seguente:

	![Finestra di dialogo Pubblica](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Nella pagina **Configura data factory** procedere come segue:
	1. Confermare l'opzione **Usa data factory esistente**.
	2. Selezionare la stessa **data factory** del modello precedente. 
	6. Fare clic su **Avanti** per passare alla pagina **Pubblica elementi**. Premere **TAB** per uscire dal campo Nome se il pulsante **Avanti** è disabilitato. 
23. Nella pagina **Pubblica elementi** assicurarsi che tutte le data factory siano selezionate e fare clic su **Avanti** per passare alla pagina **Riepilogo**.     
24. Esaminare il riepilogo e fare clic su **Avanti** per avviare il processo di distribuzione e visualizzare lo **Stato della distribuzione**.
25. Nella pagina **Stato della distribuzione**, è possibile visualizzare lo stato del processo di distribuzione. Fare clic su Fine dopo il termine della distribuzione. 

Per informazioni dettagliate sull'uso di Visual Studio per creare entità di Data Factory e pubblicarle in Azure, vedere l'articolo relativo alla [creazione della prima data factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md).

<!---HONumber=AcomDC_0518_2016-->