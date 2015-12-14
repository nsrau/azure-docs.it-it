<properties 
	pageTitle="Copiare i dati di output dall'esercitazione a un database SQL Server locale (Azure Power Shell)" 
	description="Questa procedura dettagliata estende l'esercitazione utilizzando Azure PowerShell in modo che le copie della pipeline producano dei dati in un database SQL Server."
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
	ms.date="10/29/2015" 
	ms.author="spelluru"/>


# Procedura dettagliata: copiare i dati dell'efficacia di una campagna in un database SQL Server locale
In questa procedura dettagliata si imparerà a configurare l'ambiente per consentire alla pipeline di usare i dati locali.
 
Nell'ultimo passaggio dello scenario di elaborazione dei log dalla prima procedura dettagliata con flusso di lavoro Partizione -> Arricchimento -> Analisi, l'output dell'efficacia della campagna di marketing è stato copiato in un database SQL di Azure. È anche possibile spostare questi dati in SQL Server locale per l'analisi nell'organizzazione.
 
Per copiare i dati dell'efficacia della campagna di marketing dal BLOB di Azure a SQL Server locale, è necessario creare altri servizi collegati, tabelle e pipeline locali con lo stesso set di cmdlet presentati nella prima procedura dettagliata.

> [AZURE.IMPORTANT]Questo articolo non illustra tutti i cmlet di Data factory. Vedere [Riferimento ai cmdlet di Data factory][cmdlet-reference] per la documentazione completa sui cmdlet di Data factory.
>    
> Se si usa Azure PowerShell 1.0, sarà necessario usare i cmdlet documentati [qui](https://msdn.microsoft.com/library/dn820234.aspx). Usare ad esempio New-AzureRMDataFactory invece di New-AzureDataFactory.

## Prerequisiti

È **necessario** eseguire la procedura dettagliata in [Esercitazione: spostare ed elaborare file di log con Data factory][datafactorytutorial] prima di eseguire la procedura dettagliata di questo articolo.

**(consigliato)** Esaminare ed eseguire la procedura dettagliata per [consentire alla pipeline di usare dati locali][useonpremisesdatasources] nell'articolo sulla creazione di una pipeline per spostare i dati da SQL Server locale a un archivio BLOB di Azure.


In questa procedura dettagliata si eseguiranno i passaggi seguenti:

1. [Passaggio 1: Creare un gateway di gestione dati](#OnPremStep1). Il gateway di gestione dati è un agente client che fornisce accesso alle origini dati locali dell'organizzazione dal cloud. Il gateway consente il trasferimento di dati tra un'istanza di SQL Server locale e gli archivi dati di Azure.	

	È necessario che nell'ambiente aziendale sia installato almeno un gateway che deve anche essere registrato con Data factory di Azure prima di aggiungere il database SQL Server locale come servizio collegato a un'istanza di Data factory di Azure.

2. [Passaggio 2: Creare un servizio collegato per SQL Server locale](#OnPremStep2). In questo passaggio prima si creano un database e una tabella nel computer SQL Server locale e quindi si crea il servizio collegato: **OnPremSqlLinkedService**.
3. [Passaggio 3: Creare la tabella e la pipeline](#OnPremStep3). In questo passaggio si creeranno una tabella **MarketingCampaignEffectivenessOnPremSQLTable** e una pipeline **EgressDataToOnPremPipeline**. 

4. [Passaggio 4: Monitorare la pipeline e visualizzare il risultato](#OnPremStep4). In questo passaggio si monitoreranno le pipeline, le tabelle e le sezioni di dati usando il portale di Azure classico.


## <a name="OnPremStep1"></a> Passaggio 1: Creare un gateway di gestione dati

Il gateway di gestione dati è un agente client che fornisce accesso alle origini dati locali dell'organizzazione dal cloud. Il gateway consente il trasferimento di dati tra un'istanza di SQL Server locale e gli archivi dati di Azure.
  
È necessario che nell'ambiente aziendale sia installato almeno un gateway che deve anche essere registrato con Data factory di Azure prima di aggiungere il database SQL Server locale come servizio collegato a un'istanza di Data factory di Azure.

Se esiste già un gateway di dati che è possibile usare, saltare questo passaggio.

1.	Creare un gateway di dati logico. Nel **Portale di Azure** fare clic su **Servizi collegati** nel pannello **DATA FACTORY**.
2.	Fare clic su **Aggiungi (+) Gateway dati** nella barra dei comandi.  
3.	Nel pannello **Nuovo gateway dati** fare clic su **CREA**.
4.	Nel pannello **Crea** immettere **MyGateway** come **nome** del gateway di dati.
5.	Fare clic su **SELEZIONA UN'AREA** e modificarla, se necessario. 
6.	Fare clic su **OK** nel pannello **Crea**. 
7.	Dovrebbe essere visualizzato il pannello **Configura**. 
8.	Nel pannello **Configura** fare clic su **Installa direttamente nel computer**. Il gateway verrà scaricato, installato e configurato nel computer e registrato con il servizio. Se nel computer è già installato un gateway che si vuole collegare a questo gateway logico sul portale, usare la chiave in questo pannello per registrare nuovamente il gateway con lo strumento di gestione configurazione del gateway di gestione dati (anteprima).

	![Gateway di gestione dati di Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. Fare clic su **OK** per chiudere il pannello **Configura** e su **OK** per chiudere il pannello **Crea**. Attendere che lo stato di **MyGateway** nel pannello **Servizi collegati** venga impostato su **RIUSCITO**. È anche possibile avviare lo strumento **Gestione configurazione del gateway di gestione dati (anteprima)** per verificare che il nome del gateway corrisponda al nome nel portale e che lo **stato** sia **Registrato**. Potrebbe essere necessario chiudere e riaprire il pannello Servizi collegati per visualizzare l'ultimo stato. L'aggiornamento della schermata con l'ultimo stato potrebbe richiedere alcuni minuti.

## <a name="OnPremStep2"></a>Passaggio 2: Creare un servizio collegato per SQL Server locale

In questo passaggio prima si creano il database e la tabella necessari nel computer SQL Server locale e quindi si crea il servizio collegato.

### Preparare il database e la tabella locali

Per iniziare, è necessario creare il database SQL Server, la tabella, i tipi definiti dall'utente e le stored procedure. Questi verranno usati per spostare i risultati di **MarketingCampaignEffectiveness** dal BLOB di Azure al database SQL Server.

1.	In **Esplora risorse** passare alla sottocartella **OnPremises** in **C:\\ADFWalkthrough** (o il percorso in cui sono stati estratti gli esempi).
2.	Aprire **prepareOnPremDatabase&Table.ps1** nell'editor preferito, sostituire le informazioni evidenziate con quelle di SQL Server e salvare il file (fornire i dettagli dell'**autenticazione SQL**). Ai fini dell'esercitazione, abilitare l'autenticazione SQL per il database. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. In **Azure PowerShell** passare alla cartella **C:\\ADFWalkthrough\\OnPremises**.
4.	Eseguire **prepareOnPremDatabase&Table.ps1** **(& tra virgolette o come mostrato di seguito)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Una volta completata l'esecuzione dello script, verrà visualizzato quanto segue:
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### Creare il servizio collegato

1.	Nel **Portale di Azure** fare clic sul riquadro **Servizi collegati** nel pannello **DATA FACTORY** per **LogProcessingFactory**.
2.	Nel pannello **Servizi collegati** fare clic **su Aggiungi (+) archivio dati**.
3.	Nel pannello **Nuovo archivio dati** immettere **OnPremSqlLinkedService** come **nome**. 
4.	Fare clic su **Tipo (Impostazioni obbligatorie)** e selezionare **SQL Server**. Ora dovrebbero essere visualizzate le impostazioni **GATEWAY DATI**, **Server**, **Database** e **CREDENZIALI** nel pannello **Nuovo archivio dati**. 
5.	Fare clic su **GATEWAY DATI** (Configurare le impostazioni necessarie) e selezionare **MyGateway** creato in precedenza. 
6.	Immettere il **nome** del server di database che ospita il database **MarketingCampaigns**. 
7.	Immettere **MarketingCampaigns** come database. 
8.	Fare clic su **CREDENZIALI**. 
9.	Nel pannello **Credenziali** fare clic su **Fare clic qui per impostare le credenziali in modo sicuro**.
10.	Viene installata per la prima volta un'applicazione con un solo clic e viene aperta la finestra di dialogo **Impostazione credenziali**.
11.	Nella finestra di dialogo **Impostazione credenziali** immettere il **Nome utente** e la **Password** e fare clic su **OK**. Attendere che la finestra di dialogo si chiuda. 
12.	Fare clic su **OK** nel pannello **Nuovo archivio dati**. 
13.	Nel pannello **Servizi collegati** verificare che **OnPremSqlLinkedService** sia presente nell'elenco e che lo **stato** del servizio collegato sia **Riuscito**.

## <a name="OnPremStep3"></a> Passaggio 3: Creare la tabella e la pipeline

### Creare la tabella logica locale

1.	In **Azure PowerShell** passare alla cartella **C:\\ADFWalkthrough\\OnPremises**. 
2.	Usare il cmdlet **New-AzureDataFactoryDataset** per creare le tabelle nel modo seguente per **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Creare la pipeline per copiare i dati dal BLOB di Azure a SQL Server

1.	Usare il cmdlet **New-AzureDataFactoryPipeline** per creare la pipeline nel modo seguente per **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json
	 
2. Usare il cmdlet **Set-AzureDataFactoryPipelineActivePeriod** per specificare il periodo attivo per **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

	Premere **"Y"** per continuare.
	
## <a name="OnPremStep4"></a> Passaggio 4: Monitorare la pipeline e visualizzare il risultato

Ora si possono seguire gli stessi passaggi illustrati nella sezione [Passaggio 6: monitorare le pipeline e le sezioni di dati](#MainStep6) per monitorare la nuova pipeline e le sezioni di dati per la nuova tabella ADF locale.
 
Quando si vede che lo stato di una sezione della tabella **MarketingCampaignEffectivenessOnPremSQLTable** viene impostato su Ready, significa che la pipeline ha completato l'esecuzione per la sezione. Per visualizzare i risultati, eseguire una query della tabella **MarketingCampaignEffectiveness** nel database **MarketingCampaigns** in SQL Server.
 
Congratulazioni. È stata completata la procedura dettagliata per usare l'origine dati locale.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial-using-powershell.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-move-data-between-onprem-and-cloud.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png

 

<!---HONumber=AcomDC_1203_2015-->