<properties
	pageTitle="Eseguire il debug di Hadoop in HDInsight: visualizzare i log e interpretare i messaggi di errore | Microsoft Azure"
	description="Informazioni sui messaggi di errore che vengono visualizzati durante l'amministrazione di HDInsight con PowerShell e sulle operazioni da eseguire per risolvere i problemi."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="jgao"/>

# Analizzare i log di HDInsight

Ogni cluster Hadoop in Azure HDInsight ha un account di archiviazione di Azure usato come file system predefinito. L'account di archiviazione viene definito account di archiviazione predefinito. Il cluster usa l'archivio tabelle e l'archivio BLOB di Azure nell'account di archiviazione predefinito per archiviare i log. Per trovare l'account di archiviazione predefinito per il cluster, vedere [Gestire cluster Hadoop in HDInsight](hdinsight-administer-use-management-portal.md#find-the-default-storage-account). I log vengono conservati nell'account di archiviazione anche dopo l'eliminazione del cluster.

##Log scritti tabelle di Azure

I log scritti nelle tabelle di Azure offrono un livello di informazioni relative a ciò che accade in un cluster HDInsight.

Quando si crea un cluster HDInsight, vengono automaticamente create 6 tabelle per i cluster basati su Linux nell'archivio tabelle predefinito:

- hdinsightagentlog
- syslog
- daemonlog
- hadoopservicelog
- ambariserverlog
- ambariagentlog

Per i cluster basati su Windows vengono create 3 tabelle:

- setuplog: log di eventi/eccezioni rilevati durante il provisioning o la configurazione dei cluster HDInsight.
- hadoopinstalllog: log di eventi/eccezioni rilevati durante l'installazione di Hadoop nel cluster. Questa tabella può essere utile per i problemi di debug relativi ai cluster creati con parametri personalizzati.
- hadoopservicelog: log di eventi/eccezioni registrati da tutti i servizi Hadoop. Questa tabella può essere utile per i problemi di debug relativi agli errori di processo nei cluster HDInsight.

I nomi dei file tabella sono **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Queste tabelle contengono i campi seguenti:

- ClusterDnsName
- ComponentName
- EventTimestamp
- Host
- MALoggingHash
- Message
- N
- PreciseTimeStamp
- Ruolo
- RowIndex
- Tenant
- TIMESTAMP
- TraceLevel

### Strumenti per accedere ai log

Sono disponibili diversi strumenti per accedere ai dati in queste tabelle:

-  Visual Studio
-  Azure Storage Explorer
-  Power Query per Excel

#### Usare Power Query per Excel

Power Query può essere installato da [www.microsoft.com/it-it/download/details.aspx?id=39379](http://www.microsoft.com/it-IT/download/details.aspx?id=39379). Per i requisiti di sistema, vedere la pagina di download.

**Per usare Power Query per aprire e analizzare il log del servizio**

1. Aprire **Microsoft Excel**.
2. Scegliere **Da Azure** dal menu **Power Query** e quindi fare clic su **Da Archiviazione tabelle di Microsoft Azure**.
 
	![Hadoop in HDInsight - PowerQuery per Excel - Aprire l'archivio tabelle di Azure](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Immettere il nome dell'account di archiviazione, che può corrispondere al nome breve o al nome FQDN.
4. Immettere la chiave dell'account di archiviazione. Verrà visualizzato un elenco di tabelle:

	![Hadoop in HDInsight - Log archiviati nell'archivio tabelle di Azure](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Fare clic con il pulsante destro del mouse sulla tabella hadoopservicelog nel riquadro **Strumento di navigazione** e scegliere **Modifica**. Verranno visualizzate 4 colonne. Eliminare facoltativamente le colonne **Chiave di partizione**, **Chiave di riga** e **Timestamp** selezionandole, quindi facendo clic su **Rimuovi colonne** tra le opzioni della barra multifunzione.
6. Fare clic sull'icona di espansione nella colonna Contenuto per scegliere le colonne da importare nel foglio di lavoro di Excel. Per questa dimostrazione, sono state scelte TraceLevel e ComponentName che contengono alcune informazioni di base sui componenti con problemi.

	![Log di Hadoop in HDInsight - Scegliere le colonne](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Fare clic su **OK** per importare i dati.
8. Selezionare le colonne **TraceLevel**, Role e **ComponentName** e quindi fare clic sul controllo **Raggruppa per** nella barra multifunzione.
9. Fare clic su **OK** nella finestra di dialogo Raggruppa per.
10. Fare clic su **Applica e chiudi**.
 
Ora è possibile usare Excel per filtrare e ordinare in base alle esigenze. Ovviamente è possibile includere altre colonne (ad esempio, Message) per ottenere informazioni più dettagliate sui problemi quando si verificano, ma selezionando e raggruppando le colonne descritte sopra si ottiene una panoramica discreta di ciò che accade nei servizi Hadoop. Lo stesso concetto può essere applicato alle tabelle setuplog e hadoopinstalllog.

#### Usare Visual Studio

**Per usare Visual Studio**

1. Aprire Visual Studio.
2. Scegliere **Cloud Explorer** dal menu **Visualizza** oppure fare semplicemente clic su **CTRL+\\, CTRL+X**.
3. In **Cloud Explorer** selezionare **Tipi di risorse**. L'altra opzione disponibile è **Gruppi di risorse**.
4. Espandere **Account di archiviazione**, l'account di archiviazione predefinito per il cluster e quindi **Tabelle**.
5. Fare doppio clic su **hadoopservicelog**.
6. Aggiungere un filtro. Ad esempio:
	
		TraceLevel eq 'ERROR'

	![Log di Hadoop in HDInsight - Scegliere le colonne](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)

	Per altre informazioni sulla costruzione di filtri, vedere [Creazione di stringhe di filtro per Progettazione tabelle](../vs-azure-tools-table-designer-construct-filter-strings.md).
 
##Log scritti nell'archivio BLOB di Azure

[I log scritti nelle tabelle di Azure](#log-written-to-azure-tables) offrono un livello di informazioni relative a ciò che accade in un cluster HDInsight. Queste tabelle non offrono tuttavia log a livello di attività, che possono essere utili per approfondire i problemi quando si verificano. Per fornire questo livello successivo di dettaglio, i cluster HDInsight sono configurati per scrivere log sulle attività nell'account di archiviazione BLOB per qualsiasi processo inviato con Templeton. In pratica ad accedere al cluster sono i processi inviati usando i cmdlet di Microsoft Azure PowerShell o le API di invio processi di .NET e non i processi inviati tramite RDP/riga di comando.

Per visualizzare i log, vedere [Accedere ai log delle applicazioni YARN in HDInsight basato su Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md).

Per altre informazioni sui registri applicazioni, vedere la pagina che illustra come [semplificare la gestione e l'accesso ai log utenti in YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).
 
 
## Visualizzare i log di integrità e dei processi del cluster

###Accedere all'interfaccia utente di Hadoop

Dal portale di Azure fare clic sul nome di un cluster HDInsight per aprire il relativo pannello. Dal pannello del cluster fare clic su **Dashboard**.

![Avviare il dashboard del cluster](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard.png)

Quando richiesto, immettere le credenziali per l'amministratore del cluster. Nella console di query visualizzata fare clic su **Interfaccia utente Hadoop**.

![Avviare l'interfaccia utente di Hadoop](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard-hadoop-ui.png)


###Accedere all'interfaccia utente di Yarn

Dal portale di Azure fare clic sul nome di un cluster HDInsight per aprire il relativo pannello. Dal pannello del cluster fare clic su **Dashboard**. Quando richiesto, immettere le credenziali per l'amministratore del cluster. Nella console di query visualizzata fare clic su **Interfaccia utente YARN**.

È possibile usare l'interfaccia utente YARN per eseguire queste operazioni:

* **Ottenere lo stato del cluster**. Nel riquadro sinistro espandere **Cluster** e fare clic su **Informazioni**. Verranno visualizzate informazioni sullo stato del cluster, ad esempio la memoria totale allocata, i core usati, lo stato della gestione delle risorse del cluster, la versione del cluster e così via.

    ![Avviare il dashboard del cluster](./media/hdinsight-debug-jobs/hdi-debug-yarn-cluster-state.png)

* **Ottenere lo stato del nodo**. Nel riquadro sinistro espandere **Cluster** e fare clic su **Nodi**. Verranno elencati tutti i nodi del cluster, l'indirizzo HTTP di ogni nodo, le risorse allocate per ogni nodo e così via.

* **Monitorare lo stato dei processi**. Nel riquadro sinistro espandere **Cluster** e quindi fare clic su **Applicazioni** per elencare tutti i processi del cluster. Se si desidera esaminare i processi con uno stato specifico (ad esempio processi nuovi, inviati, in esecuzione e così via), fare clic sul collegamento appropriato in **Applicazioni**. È possibile fare clic sul nome del processo per ottenere altre informazioni, ad esempio l'output, i log e così via.

###Accedere all'interfaccia utente di HBase

Dal portale di Azure fare clic sul nome di un cluster HDInsight HBase per aprire il relativo pannello. Dal pannello del cluster fare clic su **Dashboard**. Quando richiesto, immettere le credenziali per l'amministratore del cluster. Nella console di query visualizzata fare clic su **Interfaccia utente HBase**.

## Codici di errore di HDInsight

I messaggi di errore indicati in questa sezione sono forniti allo scopo di aiutare gli utenti di Hadoop in Azure HDInsight a comprendere le possibili condizioni di errore che possono verificarsi quando il servizio viene amministrato mediante Azure PowerShell e di indicare le procedure di ripristino appropriate.

Alcuni di questi messaggi di errore possono essere visualizzati anche nel portale di Azure quando viene usato per la gestione dei cluster HDInsight. Altri messaggi di errore, invece, sono meno granulari a causa dei vincoli relativi alle azioni di correzione possibili in questo contesto. Altri messaggi di errore vengono visualizzati in contesti in cui la soluzione correttiva è ovvia.

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **Descrizione**:per usare le impostazioni personalizzate per i metastore Hive e Oozie, è necessario specificare i dettagli del database SQL di Azure per almeno un componente.
- **Soluzione**: è necessario specificare un metastore di SQL Azure valido e ripetere la richiesta.

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **Descrizione**: non è possibile creare il cluster nell'area *NomeAreaGeografica*. Utilizzare un'area HDInsight valida e ripetere le richiesta.
- **Soluzione**: il cliente deve creare l'area geografica del cluster che lo supporta: Asia sudorientale, Europa occidentale, Europa settentrionale, Stati Uniti orientali o Stati Uniti occidentali.

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **Descrizione**: non è possibile trovare il record cluster richiesto.
- **Soluzione**: ripetere l'operazione.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **Descrizione**: il nome DNS del cluster *NomeDNS* non è valido. Il nome deve iniziare e finire con un carattere alfanumerico e può contenere solo il carattere speciale '-'.
- **Soluzione**: assicurarsi che il nome usato sia un nome DNS valido per il cluster, che inizi e finisca con un carattere alfanumerico e che non contenga caratteri speciali diversi dal trattino "-", quindi riprovare.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **Descrizione**: il nome del cluster *NomeCluster* non è disponibile. Scegliere un altro nome.
- **Soluzione**: l'utente deve specificare un nome di cluster che sia univoco e insistente, quindi riprovare. Se usa il portale, durante la procedura di creazione l’interfaccia utente indica se un nome di cluster è già in uso.


### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **Descrizione**: la password del cluster non è valida. La password deve essere costituita da almeno 10 caratteri e deve contenere almeno un numero, una lettera maiuscola, una lettera minuscola e un carattere speciale senza spazi e non deve contenere al suo interno il nome utente.
- **Soluzione**: fornire una password valida per il cluster e riprovare.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **Descrizione**: il nome utente del cluster non è valido. Assicurarsi che il nome utente non contenga caratteri speciali o spazi.
- **Soluzione**: fornire un nome utente valido per il cluster e riprovare.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **Descrizione**: il nome DNS del cluster *NomeClusterDns* non è valido. Il nome deve iniziare e finire con un carattere alfanumerico e può contenere solo il carattere speciale '-'.
- **Soluzione**: fornire un nome utente valido per il cluster DNS e riprovare.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **Descrizione**: il nome del contenitore nell'URI *URIContenitore* e il nome DNS *NomeDns* nel corpo della richiesta devono corrispondere.
- **Soluzione**: verificare che il nome del contenitore e il nome DNS usati siano uguali, quindi riprovare.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **Descrizione**: configurazione del cluster non valida. Impossibile trovare le definizioni del nodo dati nelle dimensioni del nodo.
- **Soluzione**: ripetere l'operazione.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
- **Descrizione**:eliminazione della distribuzione non riuscita per il cluster.
- **Soluzione**: ripetere l'operazione di eliminazione.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **Descrizione**: errore di configurazione del servizio. Informazioni di mapping DNS richieste non trovate.
- **Soluzione**: eliminare il cluster e crearne uno nuovo.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **Descrizione**: tentativo di creazione di contenitore cluster duplicato. Esiste un record per *NomeContenitore* ma gli Etag non corrispondono.
- **Soluzione**: specificare un nome univoco per il contenitore e ripetere l'operazione di creazione.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **Descrizione**: il servizio ospitato *NomeServizioOspitato* contiene già un cluster. Un servizio ospitato non può contenere più cluster.
- **Soluzione**: ospitare il cluster in un altro servizio ospitato.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **Descrizione**: non è possibile aggiornare lo stato della distribuzione del cluster.
- **Soluzione**: ripetere l'operazione. Se il problema persiste, contattare il servizio di assistenza clienti.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **Descrizione**: il cluster *NomeCluster* è stato eliminato durante la manutenzione. Ricreare il cluster.
- **Soluzione**: ricreare il cluster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **Descrizione**: configurazione del cluster non valida. Impossibile trovare la configurazione del nodo head richiesta nelle dimensioni dei nodi.
- **Soluzione**: ripetere l'operazione.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **Descrizione**: non è possibile creare il servizio ospitato *NomeServizioOspitato*. Ripetere la richiesta.
- **Soluzione**: ripetere la richiesta.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **Descrizione**: il servizio ospitato *NomeServizioOspitato* include già una distribuzione di produzione. Un servizio ospitato non può contenere più distribuzioni di produzione. Ripetere la richiesta con un nome di cluster diverso.
- **Soluzione**: usare un nome di cluster diverso e ripetere la richiesta.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **Descrizione**: non è possibile trovare il servizio ospitato *NomeServizioOspitato* per il cluster.
- **Soluzione**: se il cluster è in stato di errore, eliminarlo e riprovare.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **Descrizione**: al servizio ospitato *NomeServizioOspitato* non è associata alcuna distribuzione.
- **Soluzione**: se il cluster è in stato di errore, eliminarlo e riprovare.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **Descrizione**: nell'ID sottoscrizione *IDSottoscrizione* non restano core disponibili per creare il cluster *NomeCluster*. Richiesti: *RisorseRichieste*, Disponibili: *RisorseDisponibili*.
- **Soluzione**: liberare risorse nella sottoscrizione o aumentare le risorse disponibili nella sottoscrizione, quindi provare nuovamente a creare il cluster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **Descrizione**: nell'ID sottoscrizione *IDSottoscrizione* non è disponibile una quota per un nuovo servizio ospitato per la creazione del cluster *NomeCluster*.
- **Soluzione**: liberare risorse nella sottoscrizione o aumentare le risorse disponibili nella sottoscrizione, quindi provare nuovamente a creare il cluster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **Descrizione**: si è verificato un errore interno del server. Ripetere la richiesta.
- **Soluzione**: ripetere la richiesta.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **Descrizione**: la posizione di archiviazione di Azure *NomeAreaDati* non è una posizione valida. Verificare che l'area sia corretta e ripetere la richiesta.
- **Soluzione**: selezionare una posizione di archiviazione che supporti HDInsight, verificare che il cluster si trovi nella stessa posizione e riprovare.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **Descrizione**: dimensioni della macchina virtuale non valide per i nodi dati. Per tutti i nodi dati è supportata solo la dimensione grande della macchina virtuale.
- **Soluzione**: specificare le dimensioni supportati per il nodo head e riprovare.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **Descrizione**: dimensioni della macchina virtuale non valide per il nodo head. Per il nodo head sono supportate solo le dimensioni Molto grande della macchina virtuale.
- **Soluzione**: specificare le dimensioni supportate per il nodo head e riprovare.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **Descrizione**: l'ID sottoscrizione *IDSottoscrizione* in uso non ha autorizzazioni sufficienti per eseguire l'operazione di eliminazione per il cluster *NomeCluster*.
- **Soluzione**: se il cluster è in stato di errore, rilasciarlo e riprovare.

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **Descrizione**: il nome del contenitore BLOB dell'account di archiviazione esterno *NomeContenitore* non è valido. Verificare che il nome inizi con una lettera e contenga solo lettere minuscole, numeri e trattini.
- **Soluzione**: specificare un nome valido per il contenitore BLOB dell'account di archiviazione e riprovare.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **Descrizione**: per configurare l'account di archiviazione esterno *NomeAccountArchiviazione* è necessario impostare i dettagli della chiave privata.
- **Soluzione**: specificare una chiave privata valida per l'account di archiviazione e riprovare.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **Descrizione**: l'intestazione della versione *IntestazioneVersione* non è nel formato valido aaaa-mm-gg.
- **Soluzione**: specificare un formato valido per l'intestazione della versione e ripetere la richiesta.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **Descrizione**: configurazione del cluster non valida. Sono state rilevate più configurazioni di nodi head.
- **Soluzione**: modificare la configurazione in modo da specificare un solo nodo head.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **Descrizione**: l'operazione non è stata completata nel tempo consentito o entro il numero massimo di tentativi possibili. Ripetere la richiesta.
- **Soluzione**: ripetere la richiesta.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **Descrizione**: il parametro *NomeParametro* non può essere Null o vuoto.
- **Soluzione**: specificare un valore valido per il parametro.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **Descrizione**: una o più richieste di creazione cluster non sono valide. Verificare che i valori di input siano corretti e ripetere la richiesta.
- **Soluzione**: verificare che i valori di input siano corretti e ripetere la richiesta.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **Descrizione**: la funzionalità relativa all'area geografica non è disponibile per l'area *NomeAreaGeografica* e l'ID sottoscrizione *IDSottoscrizione*.
- **Soluzione**: specificare un'area geografica che supporta i cluster HDInsight. Le aree geografiche supportate pubblicamente sono: Asia sudorientale, Europa occidentale, Europa settentrionale, Stati Uniti orientali o Stati Uniti occidentali.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **Descrizione**: l'account di archiviazione *NomeAccountArchiviazione* si trova nell'area *NomeAreaGeograficaCorrente*. Dovrebbe corrispondere all'area del cluster *NomeAreaGeograficaCluster*.
- **Soluzione**: specificare un account di archiviazione nella stessa area del cluster oppure, se i dati si trovano già nell'account di archiviazione, creare un nuovo cluster nella stessa area dell'account di archiviazione esistente. Se si usa il portale, questo problema verrà segnalato in anticipo dall’interfaccia utente.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **Descrizione**: l'ID sottoscrizione specificato *IDSottoscrizione* non è attivo.
- **Soluzione**: riattivare la sottoscrizione oppure ottenere una nuova sottoscrizione valida.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **Descrizione**:impossibile trovare l'ID sottoscrizione specificato *IDSottoscrizione*.
- **Soluzione**: verificare che l'ID sottoscrizione sia valido e riprovare.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **Descrizione**: impossibile risolvere il DNS *UrlDns*. Assicurarsi che sia specificato l'URL completo dell'endpoint BLOB.
- **Soluzione**: specificare un URL BLOB valido. L'URL DEVE essere valido in tutte le sue parti, deve iniziare con *http://* e finire con *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **Descrizione**: non è possibile verificare il percorso della risorsa *UrlDns*. Assicurarsi che sia specificato l'URL completo dell'endpoint BLOB.
- **Soluzione**: specificare un URL BLOB valido. L'URL DEVE essere valido in tutte le sue parti, deve iniziare con *http://* e finire con *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **Descrizione**: la funzionalità relativa alla versione non è disponibile per la versione *VersioneSpecificata* e l'ID sottoscrizione *IDSottoscrizione*.
- **Soluzione**: scegliere una versione disponibile e riprovare.

### <a id="VersionNotSupported"></a>VersionNotSupported
- **Descrizione**: la versione *VersioneSpecificata* non è supportata.
- **Soluzione**: scegliere una versione supportata e riprovare.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **Descrizione**: la versione *VersioneSpecificata* non è disponibile nell'area di Azure *AreaGeograficaSpecificata*.
- **Soluzione**: scegliere una versione supportata nell'area geografica specificata e riprovare.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **Descrizione**: configurazione del cluster non valida. Impossibile trovare la configurazione dell'account WASB negli account esterni.
- **Soluzione**: verificare che l'account sia esistente e specificato correttamente nella configurazione, quindi riprovare.

## Passaggi successivi

- [Usare le visualizzazioni di Ambari per il debug di processi Tez in HDInsight](hdinsight-debug-ambari-tez-view.md)
- [Abilitare i dump dell'heap per i servizi Hadoop in HDInsight basato su Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
- [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md)

<!---HONumber=AcomDC_0907_2016-->