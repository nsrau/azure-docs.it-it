<properties 
	pageTitle="Opzioni per il trasferimento di dati archiviati nel cloud | Azure" 
	description="Informazioni aggiuntive per scegliere l'opzione migliore per il trasferimento di dati da distribuzioni locali o altre risorse cloud in Microsoft Azure per l'analisi avanzata." 
	services="data-factory, hdinsight, machine-learning, storage, sql-database" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="1/7/2014" 
	ms.author="cgronlun"/>

# Opzioni per il trasferimento di dati archiviati nel cloud Azure

Questo articolo fornisce informazioni aggiuntive per scegliere l'opzione migliore per il trasferimento di dati da una distribuzione locale o altre risorse cloud in Microsoft Azure per l'analisi avanzata. Il trasferimento di elevate quantità di dati può richiedere tempi lunghi e la sicurezza appropriata. 

Contenuto dell'articolo:

* [Servizio di Importazione/Esportazione di Azure per l'archiviazione BLOB](#blob)
* [Utilità AZCopy](#azcopy-utility)
* [Azure PowerShell](#ps)
* [Data factory di Azure (anteprima)](#data-factory)
* [Strumenti di migrazione del database SQL di Azure](#tools)
* [Sincronizzazione dati SQL di Azure (anteprima)](#data-sync)
* [Hub eventi di Azure](#event-hubs)
* [Altre opzioni per il trasferimento di dati](#other)
* [Scegliere l'opzione di trasferimento dati appropriata](#choose)


## Servizio di Importazione/Esportazione di Azure per l'archiviazione BLOB

È possibile usare il servizio Importazione/Esportazione di Azure per trasferire grandi quantità di dati di file verso o dall'archiviazione BLOB di Azure in situazioni in cui il caricamento o il download in rete è eccessivamente costoso o non è fattibile. Il caricamento o il download in rete di set di dati di grandi dimensioni richiede molto tempo. Ad esempio, per 10 TB è necessario 1 mese su T3 (44,7 Mbps). Con il servizio di importazione/Esportazione di Microsoft Azure, i clienti possono spedire il disco rigido per ridurre i tempi di caricamento o download dei dati. Per l'operazione sono necessari alcuni giorni, inclusa la spedizione. 

Per trasferire un set di grandi dimensioni di dati di file nell'archiviazione BLOB, è possibile inviare uno o più dischi rigidi contenenti tali dati a un data center di Azure, dove i dati verranno caricati nell'account di archiviazione. Analogamente, per esportare i dati dall'archiviazione BLOB, è possibile inviare dischi rigidi vuoti a un data center di Azure, dove i dati BLOB verranno copiati dall'account di archiviazione ai dischi rigidi e quindi restituiti all'utente. Prima di inviare un'unità contenente i dati, sarà opportuno crittografarli. I dati, dopo essere stati esportati dal servizio Importazione/Esportazione per l'invio all'utente, verranno crittografati anche prima della spedizione.

Per altre informazioni, vedere [Usare il servizio di Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archivio BLOB][import-export].


## Utilità AZCopy

AzCopy è un'utilità della riga di comando progettata per il caricamento, il download e la copia a prestazioni elevate dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure. Questa utilità è adatta per lo spostamento di dati monouso tra archiviazione di Azure e le risorse locali se il trasferimento dei dati tramite la rete è fattibile. Vedere [Introduzione all'utilità della riga di comando AzCopy][azcopy].

> [AZURE.NOTE] Utenti Linux: [download di ACP, AzCopy per Linux](http://www.paratools.com/acp)


## Azure PowerShell

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. È possibile usare Azure PowerShell per caricare dati nell'archiviazione BLOB, affinché possano essere elaborati dall'analisi avanzata o dai processi MapReduce.

Vedere anche:

* [Caricare i dati nell'archiviazione BLOB tramite Azure PowerShell][upload]
* [Come installare e configurare Azure PowerShell][install]


## Data factory di Azure (anteprima)

Data factory di Azure è un servizio completamente gestito per la composizione di servizi per l'archiviazione, l'elaborazione e lo spostamento dei dati in pipeline di produzione dei dati ottimizzate, scalabili ed affidabili. 

Gli sviluppatori possono compilare flussi di lavoro basati sui dati, aggregare e trasformare dati semistrutturati e strutturati originati da risorse locali (tramite il Gateway di gestione dati), servizi Internet e basati sul cloud, nonché configurare un'elaborazione di dati complessa mediante un semplice script JSON. I dati risultanti possono essere archiviati in Archiviazione di Azure o nel database SQL di Azure per l'analisi avanzata.

In particolare, uno sviluppatore è in grado di orchestrare le attività di copia standard tra diverse origini e destinazioni mostrate nella sezione "[Origini e sink supportati](data-factory-copy-activity.md#SupportedSourcesAndSinks)" di [Copia di dati con Data factory di Azure](data-factory-copy-activity.md), che include anche le proprietà per diversi tipi di archivi dati, mapping di colonne, formati di serializzazione e la gestione del tipo.

Il servizio può gestire gli errori con riavvio automatico e consentire la conversione del formato quando si spostano dati da un formato a un altro. Per definire un'attività di copia, vedere [Introduzione al servizio Data factory di Azure][intro]. Le attività di registrazione degli archivi dati e di installazione del gateway sono descritte in [Consentire alle pipeline di usare dati locali][pipelines]. 

Vedere anche:

* [Introduzione al servizio Data factory di Azure][intro]

## Strumenti di migrazione del database SQL di Azure

Sono disponibili numerosi strumenti per eseguire correttamente la migrazione di database SQL Server locali e database non SQL Server nel database SQL di Azure. La scelta dello strumento più adatto al proprio scenario dipende dal tipo, dalle dimensioni e dalla complessità del database di cui si esegue la migrazione:

* È possibile eseguire la migrazione di schemi e di dati di un database SQL di Azure esistente esportando il database, archiviando il file di esportazione in un account di archiviazione BLOB di Azure e quindi importandolo come nuovo database SQL di Azure. Il file creato al momento dell'esportazione è definito file BACPAC. Per altre informazioni, vedere [Procedura: Usare il servizio di importazione ed esportazione nel database SQL di Azure][sql-import].
* La funzionalità di copia del database consente di creare in Azure un nuovo database che è una copia coerente dal punto di vista transazionale di un database SQL di Azure esistente. Per altre informazioni, vedere, [Copia di database nel database SQL di Azure][sql-copy].
* Quando sono necessarie trasformazioni complesse di dati è possibile usare SQL Server Integration Services (SSIS). SSIS può essere usato per spostare dati all'interno e all'esterno del database SQL di Azure. Per altre informazioni, vedere [Procedura: usare Integration Services per eseguire la migrazione di un database al database SQL di Azure][integrate] e l'argomento relativo a [SSIS per lo spostamento di dati Azure e ibrido][SSIS].
* La procedura guidata di importazione ed esportazione di SQL Server offre un modo semplice di creare un pacchetto SSIS per la migrazione dei dati. Dopo aver configurato il database di origine e la destinazione, è possibile specificare trasformazioni dei dati di base. È quindi possibile salvare, modificare, eseguire e pianificare come processo questi pacchetti. Per altre informazioni, vedere [Procedura: usare l'Importazione/Esportazione guidata per eseguire la migrazione di un database al database SQL di Azure][wizard].
* La migrazione guidata di database SQL è uno strumento che consente di eseguire la migrazione dello schema e dei dati tra SQL Server in locale e il database di SQL di Azure, nonché tra i server del database SQL di Azure. Lo strumento consente inoltre di analizzare i file di traccia e gli script per individuare eventuali problemi di compatibilità con il database SQL di Azure. Per altre informazioni, vedere [Procedura: Usare la Migrazione guidata database SQL][use-wizard].
* L'utilità bcp può essere usata per importare un numero elevato di nuove righe nelle tabelle di SQL Server o per esportare dati dalle tabelle in file di dati. Per altre informazioni, vedere [Procedura: Usare bcp per eseguire la migrazione di un database al database SQL di Azure][bcp].

Vedere anche:

* [Migrazione di database nel database SQL di Azure][migrate]
 

## Sincronizzazione dati SQL di Azure (anteprima)

La sincronizzazione dati SQL (anteprima) consente di creare e pianificare sincronizzazioni regolari tra il database SQL di Azure e i database ospitati nel database SQL di Azure o SQL Server.

La sincronizzazione dati SQL è adatta per gli sviluppatori di dati per sincronizzare le modifiche delta tra database locali e database Azure nel cloud. Vedere [Sincronizzazione dati SQL][sync].

##	Hub eventi di Azure

Hub eventi di Microsoft Azure è un servizio di inserimento di eventi che fornisce un punto di ingresso nel cloud per grandi quantità di eventi e dati di telemetria su larga scala con bassa latenza e affidabilità elevata. Questo servizio, usato insieme ad altri servizi downstream, è particolarmente utile negli scenari di strumentazione delle applicazioni, elaborazione dei flussi di lavoro o dell'esperienza utente e di Internet delle cose.

Gli sviluppatori possono scrivere codice per inviare dati a un hub eventi, elaborare i dati e archiviarli nel BLOB di Azure o nel database SQL di Azure per ulteriori elaborazioni.

In alternativa, gli sviluppatori possono sfruttare Analisi di flusso di Azure, un servizio completamente gestito che fornisce funzionalità scalabili di elaborazione di eventi complessi sui dati in streaming per l'output. Gli sviluppatori possono scegliere un flusso da Hub eventi di Azure, specificare il formato usato per serializzare l'evento in ingresso (ad es. il formato JSON, CSV o Avro), quindi aggiungere il percorso di output tra cui il BLOB di Azure o il database SQL di Azure.

Vedere:

* [Informazioni sul servizio Hub eventi](/services/event-hubs/)
* [Panoramica su Hub eventi][overview]
* [Introduzione ad Analisi di flusso di Azure][stream]

## Altre opzioni per il trasferimento di dati

La funzionalità Connessioni ibride consente di connettere siti Web di Azure e servizi mobili di Azure alle risorse locali in modo facile e intuitivo. Gli sviluppatori possono creare un sito Web per spostare dati da locale ad Azure. Vedere [Panoramica delle connessioni ibride][hybrid] per informazioni dettagliate.

Con [Rete virtuale](/services/virtual-network/)è possibile usare gli strumenti di integrazione dati in esecuzione nella macchina virtuale di Azure per connettersi in modo sicuro ai database SQL Server locali nel data center locale. Solo le macchine virtuali e i servizi all'interno della stessa rete virtuale possono identificarsi o connettersi tra loro. Se si preferisce, è anche possibile creare una connessione diretta [ExpressRoute](/services/expressroute/) ad Azure tramite il provider di servizi di rete o il provider di Exchange e ignorare completamente la rete Internet pubblica.

[Azure Marketplace](?source=datamarket.md) offre soluzioni per i partner che consentono lo spostamento di dati in Azure, ad esempio il sistema STORM per il trasferimento gestito di file.

## Scegliere l'opzione di trasferimento dati appropriata

### Albero delle decisioni

![Semplifica la scelta sull'opzione di trasferimento dei dati.][decision]

Note sull'albero delle decisioni:

* la sincronizzazione dati SQL (anteprima) attiva una sincronizzazione dai dati modificati.
* Il servizio Data factory (anteprima) supporta la copia di dati tra l'archiviazione di Azure, il database SQL di Azure e SQL Server locale. 
* Oltre a usare il servizio Data factory, è possibile estendere i pacchetti SSIS esistenti per spostare i dati nel cloud in modo pianificato.

### Spostare GB di dati

<table border="1">
<tr>
<th>Spostare dati</th>
<th>Una sola volta</th>
<th>Pianificazione</th>
<th>Considerazioni</th>
</tr>

<tr>
<td><p>Da file nel servizio di archiviazione di Azure</p>
</td>
<td><ul>
<li><a href="/documentation/articles/storage-use-azcopy/">AzCopy</a></li>
<li><a href="http://www.paratools.com/acp" target="_blank">acp</a></li>
<li><a href="/documentation/articles/install-configure-powershell/">Azure PowerShell</a></li>
<li><a href="/documentation/articles/storage-import-export-service/">Importazione/Esportazione di Azure</a></li>
</ul>
</td>
<td><p>N/D</p>
</td>
<td><p>Quando non è possibile trasferire i dati tramite la rete, usare l'Importazione/Esportazione di Azure anziché AzCopy, acp e Azure PowerShell. Il servizio può richiedere diversi giorni oltre ai tempi di spedizione del disco al data center.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Da SQL Server al database SQL di Azure</li>
<li>Dal database SQL di Azure a SQL Server</li>
</ul>
</td>
<td><ul>
<li><a href="/documentation/articles/data-factory-introduction/">Data factory di Azure</a></li>
<li><a href="http://msdn.microsoft.com/library/azure/ee730904.aspx">Strumenti di migrazione del database SQL di Azure</a></li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Data factory di Azure</a></p>
</td>
<td><p>Invece degli strumenti di migrazione proporre di usare Data factory di Azure (anteprima) che offre la conversione del formato e il ripristino automatico dei processi in casi eccezionali. Se SQL Server è locale, è necessario il Gateway di gestione dati per connettersi a SQL Server.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Dal servizio di archiviazione di Azure al database SQL di Azure</li>
<li>Dal database SQL di Azure al servizio di archiviazione di Azure</li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Data factory di Azure</a></p>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Data factory di Azure</a></p>
</td>
<td><p>Data factory di Azure (anteprima) offre la conversione del formato e il ripristino automatico dei processi in casi eccezionali. Se SQL Server è locale, è necessario il Gateway di gestione dati per connettersi a SQL Server.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Da SQL Server alla sincronizzazione dati SQL</li>
<li>Dalla sincronizzazione dati SQL al database SQL di Azure</li>
</ul>
</td>
<td><p>N/D</p>
</td>
<td><p><a href="http://msdn.microsoft.com/library/azure/hh456371.aspx">Sincronizzazione dati SQL</a></p>
</td>
<td><p>La sincronizzazione dati SQL (anteprima) sincronizza i dati mediante gruppi di sincronizzazione che definiscono i database, le tabelle e le colonne da sincronizzare nonché la pianificazione della sincronizzazione.</p>
</td>
</tr>

</table>



### Spostare TB di dati

<table border="1">
<tr>
<th>Spostare dati</th>
<th>Una sola volta</th>
<th>Pianificazione</th>
<th>Considerazioni</th>
</tr>

<tr>
<td><p>Da file nel servizio di archiviazione di Azure</p>
</td>
<td><p><a href="/documentation/articles/storage-import-export-service/">Importazione/Esportazione di Azure</a></p>
</td>
<td><p>N/D</p>
</td>
<td><p>Il servizio può richiedere diversi giorni oltre ai tempi di spedizione del disco al data center.</p>
</td>
</tr>

</table>
<br>


<!--Anchors-->
[Servizio di Importazione/Esportazione di Azure per l'archiviazione BLOB]: #blob
[Utilità AZCopy]: #azcopy-utility
[Azure PowerShell]: #ps
[Data factory di Azure (anteprima)]: #data-factory
[Strumenti di migrazione del database SQL di Azure]: #tools
[Sincronizzazione dati SQL di Azure (anteprima)]: #data-sync
[Hub eventi di Azure]: #event-hubs
[Altre opzioni per il trasferimento di dati]: #other
[Scegliere l'opzione di trasferimento dati appropriata]: #choose

<!--Image references-->
[decision]: ./media/data-management-options-for-transferring-data/data-transfer-decision-tree.png


<!--Link references-->
[import-export]: storage-import-export-service.md
[azcopy]: storage-use-azcopy.md
[upload]: hdinsight-upload-data.md#powershell
[install]: install-configure-powershell.md
[start]: data-factory-get-started.md
[pipelines]: data-factory-use-onpremises-datasources.md
[copy]: data-factory-copy-activity.md
[intro]: data-factory-introduction.md
[sql-import]: http://msdn.microsoft.com/library/azure/hh335292.aspx
[sql-copy]: http://msdn.microsoft.com/library/azure/ff951624.aspx
[integrate]: http://msdn.microsoft.com/library/azure/jj156150.aspx
[SSIS]: http://msdn.microsoft.com/library/jj901708.aspx
[wizard]: http://msdn.microsoft.com/library/azure/jj156152.aspx
[use-wizard]: http://msdn.microsoft.com/library/azure/jj156144.aspx
[bcp]: http://msdn.microsoft.com/library/azure/jj156153.aspx
[migrate]: http://msdn.microsoft.com/library/azure/ee730904.aspx
[overview]: http://msdn.microsoft.com/library/dn836025.aspx
[stream]: stream-analytics-introduction.md
[sync]: http://msdn.microsoft.com/library/azure/hh456371.aspx
[hybrid]: integration-hybrid-connection-overview.md


<!--HONumber=52-->