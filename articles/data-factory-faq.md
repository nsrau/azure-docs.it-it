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
	ms.date="04/23/2015" 
	ms.author="spelluru"/>

# Data factory di Azure - Domande frequenti

## Domande generali

### D: qual è la Factory di dati di Azure?

Data factory è un servizio completamente gestito per gli sviluppatori per comporre i servizi di archiviazione, trasferimento ed elaborazione dei dati in pipeline di dati a disponibilità elevata e a tolleranza di errore. Data factory funziona tramite archiviazione dei dati locale e nel cloud. Una pipeline è un set di input di dati, le attività di elaborazione e output di dati e viene definita con semplici script JSON e attivata tramite comandi di PowerShell. Una volta attivato, Data factory organizza e pianifica l'esecuzione delle pipeline in HDInsight (Hadoop) con opzioni per la gestione automatica dei cluster per conto dell'utente. Data factory consente anche la gestione visiva e il monitoraggio attraverso il portale di anteprima di Azure, per monitorare tutte le pipeline con informazioni complete su operatività e integrità del servizio in un unico dashboard.
 
### D: quali esigenze del cliente viene Data Factory è stato risolto?

Data factory di Azure bilancia la possibilità di sfruttare archiviazioni dati diversificate, servizi di elaborazione e trasferimento attraverso tradizionali risorse di archiviazione relazionali parallelamente ai dati non strutturati con le funzionalità di controllo e monitoraggio di un servizio completamente gestito.

### D: chi è rivolto alle Factory di dati?


- Agli sviluppatori di dati, che sono responsabili della creazione di servizi di integrazione tra Hadoop e altri sistemi:
	- Devono stare al passo con un panorama in continua evoluzione e favorire l'integrazione
	- Devono scrivere codice personalizzato per la produzione di informazioni, costoso e di difficile manutenzione, non ad elevata disponibilità né a tolleranza di errore

- Ai professionisti IT, che mirano a integrare dati sempre più diversificati nella propria infrastruttura IT:
	- Devono analizzare tutti i dati aziendali per definire una visione completa dell'attività
	- Devono gestire le risorse di calcolo e archiviazione per il bilanciamento dei costi e la scalabilità in locale e nel cloud
	- Devono aggiungere rapidamente diverse origini ed elaborazioni per soddisfare nuove esigenze aziendali, pur mantenendo la visibilità in tutte le risorse di calcolo e di archiviazione

###  Q: dove posso trovare prezzi in dettaglio per la Factory di dati di Azure?

Vedere [pagina dettagli prezzi Factory dati][adf-pricing-details] per i dettagli sui prezzi per la Factory di dati di Azure.

### D. Come iniziare con una Factory di dati di Azure?

- Per una panoramica della Factory di dati di Azure, vedere [Introduzione a Azure dati Factory][adf-introduction].
- Per un'esercitazione rapida, vedere [iniziare a utilizzare Azure dati Factory][adfgetstarted].
- Per una documentazione completa, vedere [documentazione di Azure dati Factory][adf-documentation-landingpage].

  
### D: come i clienti accede Factory dati?

I clienti possono ottenere l'accesso ai dati Factory mediante la [portale Azure Preview][azure-preview-portal].

### D: qual è la disponibilità di dati della Factory area?

In anteprima pubblica, Data factory è disponibile solo negli Stati Uniti occidentali. I servizi di calcolo e di archiviazione usati dalle istanze di Data factory possono essere disponibili in altri paesi.
 
### D: quali sono i limiti sul numero di dati factory/pipeline/attività/datasets? 


- Numero di factory di dati all'interno di una sottoscrizione: 50
- Numero di pipeline all'interno di una factory di dati: 100
- Numero di attività all'interno di una pipeline: 10
- Numero di set di dati con una factory di dati: 100

### D: qual è l'esperienza di creazione/sviluppatore con servizio Factory di dati di Azure?

È possibile creare data factory con uno degli strumenti seguenti:

- **Portale azure Preview**. I pannelli di Data factory nel portale di anteprima di Azure offrono una ricca interfaccia per creare data factory e servizi collegati. Il **dati Factory Editor**, che fa parte del portale, consente di creare facilmente servizi collegati, tabelle, set di dati e pipeline specificando le definizioni di JSON per questi elementi. Vedere [dati Factory Editor][data-factory-editor] per una panoramica dell'editor e [Introduzione a Data Factory][datafactory-getstarted] per un esempio dell'utilizzo di editor/portale per creare e distribuire una factory di dati.   
- **Azure PowerShell**. Gli utenti di PowerShell potrebbero voler usare PowerShell invece dell'interfaccia utente del portale. In tal caso, i cmdlet di Data factory di Azure disponibili in Azure PowerShell consentono di creare e distribuire data factory. Vedere [Create e monitoraggio Factory di dati di Azure utilizzando Azure PowerShell][create-data-factory-using-powershell] per un esempio semplice e [Esercitazione: spostare ed elaborare i file di log utilizzando dati Factory][adf-tutorial] per un esempio dell'utilizzo di PowerShell avanzato cmdles per creare ad distribuire una factory di dati. Vedere [dati Factory Cmdlet riferimento][adf-powershell-reference] contenuto in MSDN Library per una documentazione completa di cmdlet di Factory di dati.  
- **Libreria di classi .NET**. È possibile creare data factory a livello di codice usando .NET SDK per Data factory. Vedere [creare, monitorare e gestire la factory di dati tramite .NET SDK][create-factory-using-dotnet-sdk] per una procedura dettagliata della creazione di una factory di dati tramite .NET SDK. Vedere [riferimenti alla libreria di classi Factory dati][msdn-class-library-reference] per una documentazione completa di dati Factory .NET SDK.  
- **API REST**. Per creare e distribuire data factory, è anche possibile usare l'API REST esposta dal servizio Data factory di Azure. Vedere [riferimento all'API REST di dati Factory][msdn-rest-api-reference] per una documentazione completa di API REST Factory di dati. 

## Attività - domande frequenti
### D: quali sono le origini dati supportate e le attività?

- **Origini dati supportate:** (Blob e tabelle) di archiviazione Azure, SQL Server, Database SQL di Azure, File System, Database Oracle.
- **Supportate attività:**: attività Copia (locale al cloud e cloud in locale), HDInsight attività (Pig, Hive, MapReduce, Hadoop Streaming trasformazioni), Azure Machine Learning Batch punteggio attività, attività di Stored Procedure e c# attività personalizzate.

### Esecuzione di un'attività
Il **disponibilità** impostazione di configurazione nella tabella di dati di output determina quando viene eseguita l'attività. L'attività controlla se vengono soddisfatte tutte le dipendenze di dati di input (ad esempio, **Pronto** stato) prima che venga avviato in esecuzione.

## Copiare attività - domande frequenti
### D: quali aree sono supportate dall'attività Copia?

Supporta attività di copia copia dei dati nelle aree seguenti: Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti centro, North Central US, centro-meridionali, Europa settentrionale, Europa occidentale e sud-est asiatico.

Copia di dati in altre regioni è supportato anche, utilizzando una delle aree sopra per il routing dei dati. Il consumo dell'operazione di copia viene misurato in base all'area geografica attraverso la quale i dati vengono reindirizzati.

Area di destinazione della copia | Area utilizzata per il routing
-------------------------- | -----------------------
Asia orientale | Asia sudorientale
Giappone orientale | Stati Uniti occidentali
Giappone occidentale | Stati Uniti occidentali
Brasile meridionale | Stati Uniti orientali 2

### Come copiare a più tabelle di output?
È possibile avere più tabelle di output in una pipeline come mostrato nell'esempio seguente:

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### È preferibile disporre di una pipeline con più attività o una pipeline distinta per ogni attività? 
Pipeline devono per aggregare le attività correlate. Punto di vista logico, è possibile mantenere le attività in una pipeline, se le tabelle che li connettono non vengono utilizzate da qualsiasi altra attività di fuori della pipeline. In questo modo, è non necessario a periodi di catena pipeline attiva in modo che l'allineamento tra loro. Inoltre, l'integrità dei dati nelle tabelle interne alla pipeline verranno meglio mantenuti quando si aggiorna la pipeline. Aggiornamento pipeline essenzialmente interrompe tutte le attività all'interno della pipeline, questi vengono rimossi e li crea nuovamente. Dalla Creazione punto di vista, potrebbe anche essere più semplice visualizzare il flusso di dati all'interno di attività correlate in un file JSON per la pipeline.

## Attività HDInsight - domande frequenti

### D: quali aree sono supportate da HDInsight?

Vedere la sezione di disponibilità a livello geografico nel seguente articolo: o [HDInsight prezzi][hdinsight-supported-regions].

### D: quali area viene utilizzata da un cluster HDInsight su richiesta?

Il cluster HDInsight su richiesta viene creato nella stessa area geografica in cui è presente l'archivio specificato per l'utilizzo con il cluster.

### D: come associare ulteriori account di archiviazione al cluster HDInsight?

Se si utilizza un HDInsight Cluster (BYOC - portare il proprio Cluster), vedere gli argomenti seguenti:

- [Utilizzo di un Cluster HDInsight con Metastore e gli account di archiviazione alternativo][hdinsight-alternate-storage]
- [Utilizzare account di archiviazione aggiuntivi con HDInsight Hive][hdinsight-alternate-storage-2]

Se si utilizza un cluster su richiesta che viene creato dal servizio dati Factory, è necessario specificare l'account di archiviazione aggiuntivi per il HDInsight collegato servizio in modo che il servizio dati Factory può registrare per conto dell'utente. Nella definizione di JSON per il servizio collegato su richiesta, utilizzare **additionalLinkedServiceNames** proprietà per specificare gli account di archiviazione alternativo, come illustrato nel frammento JSON seguente:
 
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

Nell'esempio precedente, otherLinkedServiceName1 e otherLinkedServiceName2 rappresentano servizi collegati le cui definizioni contengono le credenziali necessarie per accedere agli account di archiviazione alternativo cluster HDInsight.

## Attività di stored Procedure - domande frequenti
### Quali origini dati supportano l'attività di routine archiviate?
L'attività di routine archiviate supporta solo i Database di SQL Azure in questo momento.

## Slice - domande frequenti

### Come è possibile rieseguire una sezione?
È possibile eseguire nuovamente una sezione in uno dei modi seguenti:

- Fare clic su **eseguire** nella barra dei comandi sul **sezione di dati** blade della sezione del portale. 
- Eseguire **Set AzureDataFactorySliceStatus** cmdlet con stato impostato su **PendingExecution** della sezione.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Vedere [Set AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] per informazioni dettagliate sul cmdlet.

### Quanto tempo è stato necessario per elaborare una sezione?
1. Fare clic su **set di dati** riquadro il **dati FACTORY** blade per la factory di dati.
2. Scegliere il set di dati specifico il **set di dati** blade.
3. Selezionare la sezione che si è interessati dal **recenti sezioni** elenco il **tabella** blade.
4. Fare clic sull'attività eseguita dal **esegue attività** elenco il **sezione di dati** blade. 
5. Fare clic su **proprietà** riquadro il **dettagli di esecuzione di attività** blade. 
6. Verrà visualizzato il **durata** campo con un valore. Questo è il tempo impiegato per elaborare la sezione.   

### Come arrestare una sezione in esecuzione?
Se è necessario interrompere la pipeline di esecuzione, è possibile utilizzare [Suspend AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) cmdlet. Attualmente, la sospensione di pipeline non arresta le esecuzioni di sezione in corso. Una volta terminato le esecuzioni in corso, non viene prelevata alcuna sezione aggiuntiva.

Se si desidera arrestare immediatamente tutte le esecuzioni, l'unico modo sarebbe quello di eliminare la pipeline e crearne uno nuovo. Se si sceglie di eliminare la pipeline, non è necessario eliminare tabelle e i servizi collegati utilizzati dalla pipeline.



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

<!---HONumber=GIT-SubDir-->