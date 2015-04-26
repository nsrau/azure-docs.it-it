<properties title="Azure Data Factory - Release Notes" pageTitle="Data factory - Note sulla versione | Azure" description="Note sulla versione di Data Factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Note sulla versione di Data factory di Azure

## Note sulla versione di Data factory - 11/12/2014 ##

### Nuovi miglioramenti

- Integrazione con Azure Machine Learning
	- Questa versione del servizio Data factory di Azure consente di integrare Data factory di Azure con Azure Machine Learning (ML) mediante **AzureMLLinkedService** e **AzureMLBatchScoringActivity**. Per informazioni dettagliate, vedere la pagina relativa alla [creazione di pipeline predittive usando Data factory e Azure Machine Learning][adf-azure-ml]. 
- Viene fornito lo stato della versione del gateway
	- Se è disponibile una versione più recente del gateway rispetto a quella attualmente installata, verrà visualizzato lo stato "NewVersionAvailable" nel portale di anteprima di Azure e nell'output del cmdlet Get-AzureDataFactoryGateway. È quindi possibile seguire il percorso nel portale per scaricare il nuovo file di installazione (.msi) ed eseguirlo per installare il gateway più recente. Non è necessaria alcuna configurazione aggiuntiva.

### Modifiche

- Viene rimossa la proprietà JobsContainer in HdInsightOnDemandLinkedService
	- Nella definizione JSON per un HDInsightOnDemandLinkedService non è più necessario specificare la proprietà **jobsContainer**. Se è specificata per un servizio collegato su richiesta, la proprietà viene ignorata. È possibile rimuovere la proprietà dalla definizione JSON per il servizio collegato e aggiornare la definizione del servizio collegato tramite il cmdlet New-AzureDataFactoryLinkedService.
- Parametri di configurazione facoltativi per HDInsightOnDemandLinkedService
	- Questa versione introduce il supporto per alcuni parametri di configurazione facoltativi per HDInsightOnDemandLinked (cluster di HDInsight su richiesta). Per informazioni dettagliate, vedere la pagina relativa alle [proprietà ClusterCreateParameters][on-demand-hdi-parameters].
- Viene rimosso il percorso del gateway
	- Quando si crea un gateway di Data factory di Azure tramite il portale o tramite PowerShell (New-AzureDataFactoryGateway), non è più necessario specificare il percorso per il gateway. Verrà ereditata l'area di Data factory. Analogamente, per configurare un collegato servizio di SQL Server mediante JSON, la proprietà "gatewayLocation" non è più necessaria. .NET SDK di Data factory è stato aggiornato in base a queste modifiche.
	- Se si usa una versione precedente dell'SDK e di Azure PowerShell, è necessario fornire l'impostazione del percorso.
 
     

#### Modifiche di rilievo
	
- Da CustomActivity a DotNetActivity
	- L'interfaccia **ICustomActivity** è stata rinominata in **IDotNetActivity**. Sarà necessario aggiornare i pacchetti NuGet di Data factory e cambiare ICustomActivity in IDotNetActivity nel codice sorgente per l'attività personalizzata.  
	- Il tipo di attività personalizzata nella definizione JSON per l'attività personalizzata deve essere cambiato da **CustomActivity** a **DotNetActivity**. 
	- Le classi **CustomActivity** e **CustomActivityProperties** sono state rinominate in **DotNetActivity** e **DotNetActivityProperties** con lo stesso set di proprietà.

		Se si usa una versione precedente dell'SDK e di Azure PowerShell, è possibile continuare a usare CustomActivity anziché DotNetActivity.
    
  		Per una procedura dettagliata su come creare un'attività personalizzata e usarla in una pipeline di Data factory di Azure, vedere la pagina relativa all'[uso di attività personalizzate in una pipeline di Data factory di Azure][adf-custom-activities].  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/it-it/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->
