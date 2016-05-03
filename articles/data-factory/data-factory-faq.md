<properties 
	pageTitle="Data factory di Azure - Domande frequenti" 
	description="Domande frequenti su Data factory di Azure." 
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
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# Data factory di Azure - Domande frequenti

## Domande generali

### Che cos'è Data factory di Azure?

Data Factory è un servizio di integrazione dei dati basato sul cloud che **automatizza lo spostamento e la trasformazione dei dati**. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Data factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso.
 
Il servizio Data Factory consente di creare flussi di lavoro basati sui dati per spostare dati dagli archivi locali a quelli sul cloud e viceversa nonché per elaborare o trasformare i dati usando servizi di calcolo come Azure HDInsight e Analisi Azure Data Lake. Dopo aver creato una pipeline che esegue l'azione necessaria, è possibile pianificarne l'esecuzione periodica, ad esempio ogni ora, giorno, settimana e così via.

Vedere[Panoramica e concetti chiave](data-factory-introduction.md)per ulteriori dettagli.

### Dove posso trovare informazioni dettagliate sui prezzi di Data factory di Azure?

Per saperne di più, vedere la [pagina relativa ai prezzi di Data factory di Azure][adf-pricing-details].

### In che modo è possibile iniziare a usare Data factory di Azure?

- Per una panoramica di Data factory di Azure, vedere [Introduzione al servizio Data factory di Azure](data-factory-introduction.md).
- Per una rapida esercitazione, vedere [Introduzione a Data factory di Azure](data-factory-get-started.md).
- Per una documentazione completa, vedere [Documentazione di Data factory](https://azure.microsoft.com/documentation/services/data-factory/).
  
### In quali paesi è disponibile Data factory?
Data Factory è disponibile negli **Stati Uniti occidentali** e in **Europa settentrionale**. I servizi di calcolo e di archiviazione usati dalle istanze di Data factory possono essere disponibili in altri paesi. Vedere [Aree supportate](data-factory-introduction.md#supported-regions).
 
### Quali sono i limiti sul numero di Data factory/pipeline/attività/set di dati?
 
Vedere la sezione **Limiti di Azure Data Factory** dell'articolo [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../azure-subscription-service-limits.md#data-factory-limits).


### Quale esperienza di creazione/sviluppo offre il servizio Data factory di Azure?

È possibile creare data factory con uno degli strumenti seguenti:

- **Portale di Azure** I pannelli di Data Factory nel portale di Azure offrono una ricca interfaccia utente per la creazione di data factory e servizi collegati. L'**editor di Data factory**, anch'esso parte del portale, consente di creare facilmente servizi collegati, tabelle, set di dati e pipeline specificando definizioni JSON per questi elementi. Per un esempio dell'uso del portale o dell'editor per creare e distribuire una data factory, vedere l'articolo di [introduzione a Data Factory](data-factory-get-started.md).   

- **Azure PowerShell** Gli utenti di PowerShell che preferiscono usare PowerShell anziché l'interfaccia utente del portale possono usare i cmdlet di Azure Data Factory inclusi in Azure PowerShell per creare e distribuire data factory. Per un semplice esempio, vedere l'articolo relativo alla [creazione e monitoraggio di Data factory di Azure con Azure PowerShell](data-factory-monitor-manage-using-powershell.md). Per un esempio avanzato relativo all'uso dei cmdlet di PowerShell per creare e distribuire un'istanza di Data factory, vedere [Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial]. Per la documentazione completa dei cmdlet di Data factory, vedere le [informazioni di riferimento sui cmdlet di Data factory][adf-powershell-reference] in MSDN Library.
  
- **Visual Studio** È anche possibile usare Visual Studio per creare, monitorare e gestire le data factory a livello di programmazione. Vedere l’articolo [Creazione, monitoraggio e gestione di data factory di Azure mediante Data Factory .NET SDK](data-factory-create-data-factories-programmatically.md)per maggiori dettagli.
  
- **.NET Class Library** È possibile creare data factory a livello di programmazione usando .NET SDK per Data Factory. Per la procedura dettagliata per la creazione di un'istanza di Data factory con .NET SDK, vedere [Creazione, monitoraggio e gestione delle istanze di Data factory di Azure mediante .NET SDK][create-factory-using-dotnet-sdk]. Per la documentazione completa di Data Factory .NET SDK, vedere le [informazioni di riferimento sulla libreria di classi per Data factory][msdn-class-library-reference].

- **API REST** Per creare e distribuire data factory, è anche possibile usare l'API REST esposta dal servizio Azure Data Factory. Per la documentazione completa, vedere [Informazioni di riferimento sull'API REST di Data factory][msdn-rest-api-reference].
 
- **Modello di Azure Resource Manager** Per i dettagli, vedere [Esercitazione: Creare la prima data factory di Azure usando il modello di Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md).

### È possibile rinominare una data factory?
No. Come per le altre risorse di Azure, non è possibile modificare il nome di una data factory di Azure.

## Attività - Domande frequenti
### Quali sono i diversi tipi di attività che si possono usare in una pipeline di Data Factory? 

- [Attività di spostamento dei dati](data-factory-data-movement-activities.md) per spostare i dati.
- [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md) per elaborare o trasformare i dati. 

### Quando viene eseguita un'attività?
L'impostazione di configurazione **availability** della tabella dati di output determina quando viene eseguita l'attività. Se vengono specificati set di dati di input, prima dell'esecuzione l'attività verifica se sono soddisfatte tutte le dipendenze dei dati di input, ad esempio lo stato **Pronto**.

## Attività di copia - Domande frequenti
### È consigliabile avere una pipeline con più attività o una pipeline distinta per ogni attività? 
Le pipeline sono state progettate per aggregare attività correlate. È logicamente possibile mantenere le attività in un'unica pipeline se le tabelle che le connettono non vengono usate da altre attività esterne alla pipeline. In questo modo non sarà necessario concatenare periodi attivi della pipeline per allinearli reciprocamente. Ciò consentirà anche di mantenere meglio l'integrità dei dati delle tabelle interne alla pipeline durante l'aggiornamento di quest'ultima. Essenzialmente, l'aggiornamento arresta tutte le attività nella pipeline, le rimuove e le crea di nuovo. Dal punto di vista della creazione, potrebbe anche risultare più semplice visualizzare il flusso di dati entro le attività correlate in un file JSON per la pipeline.

## Attività di HDInsight - Domande frequenti

### Quali aree sono supportate da HDInsight?

Vedere la sezione sulla disponibilità a livello geografico nell'articolo relativo ai [dettagli dei prezzi di HDInsight][hdinsight-supported-regions].

### Quale area geografica viene usata per un cluster HDInsight su richiesta?

Il cluster HDInsight su richiesta viene creato nella stessa area geografica in cui è presente l'archivio specificato per l'utilizzo con il cluster.

### In che modo è possibile associare account di archiviazione aggiuntivi al cluster HDInsight?

Se si usa un cluster HDInsight personalizzato (BYOC - Bring Your Own Cluster), vedere gli argomenti seguenti:

- [Uso di un cluster HDInsight con account di archiviazione e metastore alternativi][hdinsight-alternate-storage]
- [Usare account di archiviazione aggiuntivi con HDInsight Hive][hdinsight-alternate-storage-2]

Se si usa un cluster su richiesta creato dal servizio Data factory, sarà necessario specificare altri account di archiviazione per il servizio collegato HDInsight, in modo che il servizio Data factory li possa registrare automaticamente. Nella definizione JSON per il servizio collegato su richiesta, usare la proprietà **additionalLinkedServiceNames** per specificare account di archiviazione alternativi, come illustrato nel frammento di codice JSON seguente:
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

Nell'esempio precedente otherLinkedServiceName1 e otherLinkedServiceName2 rappresentano servizi collegati le cui definizioni includono credenziali necessarie al cluster HDInsight per l'accesso ad account di archiviazione alternativi.

## Sezioni - Domande frequenti

### Come si riesegue una sezione?
È possibile rieseguire una sezione in uno dei modi seguenti:

- Fare clic su **Esegui** sulla barra dei comandi nel pannello **SEZIONE DATI** per la sezione nel portale. 
- Eseguire il cmdlet **Set-AzureRmDataFactorySliceStatus** con lo stato impostato su **Waiting** per la sezione.   
	
		Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Per informazioni dettagliate sul cmdlet, vedere [Set-AzureRmDataFactorySliceStatus][set-azure-datafactory-slice-status].

### Quanto tempo è stato necessario per elaborare una sezione?
1. Fare clic sul riquadro **Set di dati** nel pannello **DATA FACTORY** per l'istanza di Data factory.
2. Fare clic sul set di dati specifico nel pannello **Set di dati**.
3. Selezionare la sezione a cui si è interessati dall'elenco **Sezioni recenti** nel pannello **TABELLA**.
4. Fare clic sull'esecuzione di attività nell'elenco **Esecuzioni attività** nel pannello **SEZIONE DATI**. 
5. Fare clic sul riquadro **Proprietà** nel pannello **DETTAGLI ESECUZIONE ATTIVITÀ**. 
6. Nel campo **DURATA** dovrebbe essere visualizzato un valore, ovvero il tempo impiegato per elaborare la sezione.   

### In che modo è possibile interrompere una sezione in esecuzione?
Se è necessario interrompere l'esecuzione della pipeline, è possibile usare il cmdlet [Suspend-AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx). La sospensione della pipeline attualmente non interrompe le esecuzioni di sezioni in corso. Al termine delle esecuzioni in corso non verranno eseguite altre sezioni.

L'unica soluzione per interrompere immediatamente tutte le esecuzioni consiste nell'eliminare la pipeline e crearla di nuovo. Se si sceglie di eliminare la pipeline, NON sarà necessario eliminare le tabelle e i servizi collegati usati da essa.


[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=AcomDC_0420_2016-->