---
title: Azure Data Factory - Domande frequenti
description: Domande frequenti su Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/24/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 14b7900a8b4fbf86e8a814def6fa8c7915832376
ms.lasthandoff: 04/12/2017


---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - Domande frequenti
## <a name="general-questions"></a>Domande generali
### <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory?
Data Factory è un servizio di integrazione dei dati basato sul cloud che **automatizza lo spostamento e la trasformazione dei dati**. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Data Factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso.

Data Factory consente di creare flussi di lavoro basati sui dati per spostare dati dagli archivi locali a quelli sul cloud e viceversa, nonché per elaborare o trasformare i dati usando servizi di calcolo come Azure HDInsight e Azure Data Lake Analytics. Dopo aver creato una pipeline che esegue l'azione necessaria, è possibile pianificarne l'esecuzione periodica, ad esempio ogni ora, giorno, settimana e così via.   

Per altre informazioni, vedere [Cenni preliminari e concetti chiave](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Dove posso trovare informazioni dettagliate sui prezzi di Azure Data Factory?
Per saperne di più, vedere la [pagina Dettagli prezzi di Data Factory][adf-pricing-details].  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>In che modo è possibile iniziare a usare Azure Data Factory?
* Per una panoramica di Azure Data Factory, vedere [Introduzione al servizio Azure Data Factory](data-factory-introduction.md).
* Per un'esercitazione su come **copiare o spostare i dati** usando l'attività di copia, vedere [Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Per un'esercitazione su come **trasformare i dati** usando l'attività Hive di HDInsight, vedere [Elaborare i dati eseguendo lo script Hive in un cluster Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>In quali paesi è disponibile Data Factory?
Data Factory è disponibile negli **Stati Uniti occidentali** e in **Europa settentrionale**. I servizi di calcolo e di archiviazione usati dalle istanze di Data Factory possono essere disponibili in altri paesi. Vedere [Aree supportate](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quali sono i limiti sul numero di Data Factory/pipeline/attività/set di dati?
Vedere la sezione **Limiti di Azure Data Factory** dell'articolo [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Quale esperienza di creazione/sviluppo offre il servizio Azure Data Factory?
È possibile creare data factory con uno degli strumenti/SDK seguenti:

* **Portale di Azure**
   I pannelli di Data Factory nel portale di Azure offrono una ricca interfaccia utente per la creazione di data factory e servizi collegati. L' **editor di Data Factory**, anch'esso parte del portale, consente di creare facilmente servizi collegati, tabelle, set di dati e pipeline specificando definizioni JSON per questi elementi. Per un esempio dell'uso del portale o dell'editor per creare e distribuire una data factory, vedere [Creare la prima data factory di Azure usando il portale di Azure/l'editor di Data Factory](data-factory-build-your-first-pipeline-using-editor.md) .
* **Visual Studio**
   È possibile usare Visual Studio per la creazione di una data factory di Azure. Per i dettagli, vedere [Creare la prima data factory di Azure con Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) .
* **Azure PowerShell**
   Per un'esercitazione o la procedura dettagliata per la creazione di una data factory tramite PowerShell, vedere [Creare la prima data factory di Azure con Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md). Per la documentazione completa dei cmdlet di Data Factory, vedere [Data Factory Cmdlet Reference][adf-powershell-reference] (Informazioni di riferimento sui cmdlet di Data Factory) in MSDN Library.
* **.NET Class Library**
   È possibile creare data factory a livello di programmazione usando .NET SDK per Data Factory. Per la procedura dettagliata per la creazione di un'istanza di Data Factory con .NET SDK, vedere [Creazione, monitoraggio e gestione delle istanze di Azure Data Factory mediante .NET SDK](data-factory-create-data-factories-programmatically.md) . Per la documentazione completa di Data Factory .NET SDK, vedere [Data Factory Class Library Reference][msdn-class-library-reference] (Informazioni di riferimento sulla libreria di classi per Data Factory).
* **API REST**
   Per creare e distribuire data factory, è anche possibile usare l'API REST esposta dal servizio Azure Data Factory. Per la documentazione completa, vedere [Data Factory REST API Reference][msdn-rest-api-reference] (Informazioni di riferimento sull'API REST di Data Factory).
* **Modello di Azure Resource Manager**
    Per i dettagli, vedere [Esercitazione: Creare la prima data factory di Azure usando il modello di Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md).

### <a name="can-i-rename-a-data-factory"></a>È possibile rinominare una data factory?
No. Come per le altre risorse di Azure, non è possibile modificare il nome di una data factory di Azure.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>È possibile trasferire una data factory da una sottoscrizione di Azure a un'altra?
Sì. Usare il pulsante **Sposta** nel pannello della data factory come mostrato nel diagramma seguente:

![Spostare una data factory](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quali sono gli ambienti di calcolo supportati da Data Factory?
La seguente tabella presenta un elenco degli ambienti di calcolo supportati da Data Factory e le attività eseguibili in tali ambienti.

| Ambiente di calcolo | attività |
| --- | --- |
| [Cluster HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) o [il proprio cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Attività di Machine Learning: esecuzione batch e aggiornamento risorse](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics.](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Stored procedure](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Come si confronta Azure Data Factory con SQL Server Integration Services (SSIS)? 
Vedere la presentazione [Azure Data Factory e SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) di uno dei nostri MVP (Most Valued Professional): Reza Rad. Alcune delle modifiche recenti in Data Factory potrebbero non essere elencate della presentazione. Continuiamo ad aggiungere altre funzionalità ad Azure Data Factory. Continuiamo ad aggiungere altre funzionalità ad Azure Data Factory. Questi aggiornamenti verranno incorporati nel confronto delle tecnologie di integrazione di dati da Microsoft nel corso di quest'anno.   

## <a name="activities---faq"></a>Attività - Domande frequenti
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quali sono i diversi tipi di attività che si possono usare in una pipeline di Data Factory?
* [Attività di spostamento dei dati](data-factory-data-movement-activities.md) per spostare i dati.
* [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md) per elaborare o trasformare i dati.

### <a name="when-does-an-activity-run"></a>Quando viene eseguita un'attività?
L'impostazione di configurazione **availability** della tabella dati di output determina quando viene eseguita l'attività. Se vengono specificati set di dati di input, prima dell'esecuzione l'attività verifica se sono soddisfatte tutte le dipendenze dei dati di input, ad esempio lo stato **Pronto** .

## <a name="copy-activity---faq"></a>Attività di copia - Domande frequenti
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>È consigliabile avere una pipeline con più attività o una pipeline distinta per ogni attività?
Le pipeline sono state progettate per aggregare attività correlate. Se i set di dati che si interconnettono non vengono usati da altre attività esterne alla pipeline, è possibile mantenere le attività in un'unica pipeline. In questo modo non sarà necessario concatenare periodi attivi della pipeline per allinearli reciprocamente. Ciò consentirà anche di mantenere meglio l'integrità dei dati delle tabelle interne alla pipeline durante l'aggiornamento di quest'ultima. Essenzialmente, l'aggiornamento arresta tutte le attività nella pipeline, le rimuove e le crea di nuovo. Dal punto di vista della creazione, potrebbe anche risultare più semplice visualizzare il flusso di dati entro le attività correlate in un file JSON per la pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quali sono gli archivi dati supportati?
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

### <a name="what-are-the-supported-file-formats"></a>Quali sono i formati di file supportati?
[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Dove viene eseguita l'operazione di copia?
Per altre informazioni, vedere la sezione [Spostamento dei dati disponibile a livello globale](data-factory-data-movement-activities.md#global) . In breve, quando è coinvolto un archivio dati locale, l'operazione di copia viene eseguita dal Gateway di gestione dati nell'ambiente locale. Quando lo spostamento dei dati avviene tra due archivi cloud, l'operazione di copia viene eseguita nell'area più vicina alla località del sink nella stessa area geografica.

## <a name="hdinsight-activity---faq"></a>Attività di HDInsight - Domande frequenti
### <a name="what-regions-are-supported-by-hdinsight"></a>Quali aree sono supportate da HDInsight?
Vedere la sezione sulla disponibilità a livello geografico nell'articolo in [Dettagli dei prezzi di HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Quale area geografica viene usata per un cluster HDInsight su richiesta?
Il cluster HDInsight su richiesta viene creato nella stessa area geografica in cui è presente l'archivio specificato per l'utilizzo con il cluster.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>In che modo è possibile associare account di archiviazione aggiuntivi al cluster HDInsight?
Se si usa un cluster HDInsight personalizzato (BYOC - Bring Your Own Cluster), vedere gli argomenti seguenti:

* [Uso di un cluster HDInsight con account di archiviazione e metastore alternativi][hdinsight-alternate-storage]
* [Usare account di archiviazione aggiuntivi con HDInsight Hive][hdinsight-alternate-storage-2]

Se si usa un cluster su richiesta creato dal servizio Data Factory, specificare altri account di archiviazione per il servizio collegato HDInsight, in modo che il servizio Data Factory li possa registrare automaticamente. Nella definizione JSON per il servizio collegato su richiesta, usare la proprietà **additionalLinkedServiceNames** per specificare account di archiviazione alternativi, come illustrato nel frammento di codice JSON seguente:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "clusterSize": 1,
            "timeToLive": "00:01:00",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
Nell'esempio precedente otherLinkedServiceName1 e otherLinkedServiceName2 rappresentano servizi collegati le cui definizioni includono credenziali necessarie al cluster HDInsight per l'accesso ad account di archiviazione alternativi.

## <a name="slices---faq"></a>Sezioni - Domande frequenti
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Perché le sezioni di input non sono in stato Pronto?
Un errore frequente è la mancata impostazione della proprietà **external** su **true** nel set di dati di input quando i dati di input sono esterni alla data factory, ovvero non prodotti dalla data factory.

Nell'esempio seguente è necessario soltanto impostare **external** su true in **dataset1**.  

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset4

Se si dispone di un'altra data factory con una pipeline che accetta dataset4 (prodotto dalla pipeline 2 nella data factory 1), contrassegnare dataset4 come set di dati esterno perché tale set di dati è prodotto da una data factory differente (DataFactory1, non DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4->activity4->dataset5

Se la proprietà esterna è impostata correttamente, verificare che i dati di input siano presenti nel percorso specificato nella definizione del set di dati di input.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>In che modo è possibile eseguire una sezione in un momento diverso dalla mezzanotte quando tale sezione viene prodotta ogni giorno?
Usare la proprietà **offset** per specificare l'ora di produzione della sezione. Per altre informazioni su questa proprietà, vedere [Disponibilità dei set di dati](data-factory-create-datasets.md#Availability) . Di seguito è riportato un rapido esempio:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Le sezioni giornaliere iniziano alle **6.00** anziché a mezzanotte, ovvero l'impostazione predefinita.     

### <a name="how-can-i-rerun-a-slice"></a>Come si riesegue una sezione?
È possibile rieseguire una sezione in uno dei modi seguenti:

* Usare l'app di monitoraggio e gestione per eseguire di nuovo una finestra attività o una sezione. Per istruzioni, vedere la sezione [Rieseguire finestre attività selezionate](data-factory-monitor-manage-app.md#perform-batch-actions) .   
* Fare clic su **Esegui** sulla barra dei comandi nel pannello**SEZIONE DATI** per la sezione nel portale di Azure.
* Eseguire il cmdlet **Set-AzureRmDataFactorySliceStatus** con lo stato impostato su **Waiting** per la sezione.   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
Per informazioni dettagliate sul cmdlet, vedere [Set-AzureRmDataFactorySliceStatus][set-azure-datafactory-slice-status].

### <a name="how-long-did-it-take-to-process-a-slice"></a>Quanto tempo è stato necessario per elaborare una sezione?
Per conoscere la durata dell'elaborazione di una sezione di dati, usare Activity Window Explorer nell'app di monitoraggio e gestione. Per informazioni dettagliate, vedere la sezione [Activity Window Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) (Esplora finestre attività).

È possibile anche eseguire le operazioni seguenti nel portale di Azure:  

1. Fare clic sul riquadro**Set di dati** nel pannello **DATA FACTORY** per l'istanza di Data factory.
2. Fare clic sul set di dati specifico nel pannello **Set di dati** .
3. Selezionare la sezione a cui si è interessati dall'elenco **Sezioni recenti** nel pannello **TABELLA**.
4. Fare clic sull'esecuzione di attività nell'elenco **Esecuzioni attività** nel pannello **SEZIONE DATI**.
5. Fare clic sul riquadro **Proprietà** nel pannello **DETTAGLI ESECUZIONE ATTIVA**.
6. Nel campo **DURATA** dovrebbe essere visualizzato un valore, ovvero il tempo impiegato per elaborare la sezione.   

### <a name="how-to-stop-a-running-slice"></a>In che modo è possibile interrompere una sezione in esecuzione?
Se è necessario interrompere l'esecuzione della pipeline, è possibile usare il cmdlet [Suspend-AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) . La sospensione della pipeline attualmente non interrompe le esecuzioni di sezioni in corso. Al termine delle esecuzioni in corso non verranno eseguite altre sezioni.

L'unica soluzione per interrompere immediatamente tutte le esecuzioni consiste nell'eliminare la pipeline e crearla di nuovo. Se si sceglie di eliminare la pipeline, NON sarà necessario eliminare le tabelle e i servizi collegati usati da essa.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/azurerm.datafactories
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

