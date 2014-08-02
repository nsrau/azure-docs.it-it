<properties linkid="manage-services-hdinsight-debug-error-messages" urlDisplayName="Debug HDInsight Errors" pageTitle="Debug HDInsight: Error messages | Azure" metaKeywords="dinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

Debug di HDInsight: messaggi di errore
======================================

Introduzione
------------

I messaggi di errore indicati in questo argomento vengono forniti allo scopo di aiutare gli utenti di Azure HDInsight a comprendere le possibili condizioni di errore che possono verificarsi quando il servizio viene amministrato mediante Azure PowerShell e di indicare le procedure di ripristino appropriate.

Alcuni di questi messaggi di errore possono essere visualizzati anche nel portale di Azure quando viene utilizzato per la gestione dei cluster HDinsight. Altri messaggi di errore, invece, sono meno granulari a causa dei vincoli relativi alle azioni di correzione possibili in questo contesto. Altri messaggi di errore vengono visualizzati in contesti in cui la soluzione correttiva è ovvia. In caso di violazione dei vincoli relativi ai parametri, ad esempio, il messaggio viene visualizzato sulla destra della casella in cui è stato immesso il valore. Nel caso seguente è stato richiesto un numero eccessivo di nodi di dati. La soluzione consiste nel ridurre il numero a un valore consentito che sia pari o inferiore a 22.

![HDI.Debugging.ErrorMessages.Portal](./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png)

Gli errori che possono verificarsi in Azure PowerShell o nel portale di Azure sono elencati in ordine alfabetico per nome nella sezione [Errori di HDInsight](#hdinsight-error-messages), in cui è riportato un collegamento a una voce della sezione [Descrizione e correzione egli errori](#discription-mitigation-errors) in cui sono fornite le informazioni seguenti sull'errore:

-   **Descrizione**: messaggio di errore visualizzato dagli utenti
-   **Soluzione**: procedura che è possibile effettuare per il ripristino in caso di errore.

### Errori di HDInsight

[AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided) 
[AzureRegionNotSupported](#AzureRegionNotSupported) 
[ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound) 
[ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord) 
[ClusterNameUnavailable](#ClusterNameUnavailable) 
[ClusterUserNameInvalid](#ClusterUserNameInvalid) 
[ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord) 
[ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName) 
[DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound) 
[DeploymentDeletionFailure](#DeploymentDeletionFailure) 
[DnsMappingNotFound](#DnsMappingNotFound) 
[DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest) 
[DuplicateClusterInHostedService](#DuplicateClusterInHostedService) 
[FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus) 
[HdiRestoreClusterAltered](#HdiRestoreClusterAltered) 
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound) 
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound) 
[HostedServiceCreationFailure](#HostedServiceCreationFailure) 
[HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment) 
[HostedServiceNotFound](#HostedServiceNotFound) 
[HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment) 
[InsufficientResourcesCores](#InsufficientResourcesCores) 
[InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices) 
[InternalErrorRetryRequest](#InternalErrorRetryRequest) 
[InvalidAzureStorageLocation](#InvalidAzureStorageLocation) 
[InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode) 
[InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode) 
[InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion) 
[InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName) 
[InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey) 
[InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat) 
[MoreThanOneHeadNode](#MoreThanOneHeadNode) 
[OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest) 
[ParameterNullOrEmpty](#ParameterNullOrEmpty) 
[PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure) 
[RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable) 
[StorageAccountNotColocated](#StorageAccountNotColocated) 
[SubscriptionIdNotActive](#SubscriptionIdNotActive) 
[SubscriptionIdNotFound](#SubscriptionIdNotFound) 
[UnableToResolveDNS](#UnableToResolveDNS) 
[UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource) 
[VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable) 
[VersionNotSupported](#VersionNotSupported) 
[VersionNotSupportedInRegion](#VersionNotSupportedInRegion) 
[WasbAccountConfigNotFound](#WasbAccountConfigNotFound)

Diagnostica e risoluzione degli errori
--------------------------------------

### AtleastOneSqlMetastoreMustBeProvided

-   **Descrizione**: per utilizzare le impostazioni personalizzate per i metastore Hive e Oozie è necessario specificare i dettagli del database SQL di Azure per almeno un componente.
-   **Soluzione**: è necessario specificare un metastore SQL Azure valido e ripetere la richiesta.

### AzureRegionNotSupported

-   **Descrizione**: impossibile creare il cluster nell'area geografica *NomeAreaGeografica*. Utilizzare un'area HDInsight valida e ripetere le richiesta.
-   **Soluzione**: il cliente deve creare l'area geografica del cluster che lo supporta: Europa settentrionale, Stati Uniti orientali o Stati Uniti occidentali.

### ClusterContainerRecordNotFound

-   **Descrizione**: impossibile trovare il record cluster richiesto.
-   **Soluzione**: riprovare.

### ClusterDnsNameInvalidReservedWord

-   **Descrizione**: il nome DNS del cluster *NomeDNS* non è valido. Il nome deve iniziare e finire con un carattere alfanumerico e può contenere solo il carattere speciale '-'.
-   **Soluzione**: assicurarsi che il nome utilizzato sia un nome DNS valido per il cluster, che inizi e finisca con un carattere alfanumerico e che non contenga caratteri speciali diversi dal trattino '-', quindi riprovare.

### ClusterNameUnavailable

-   **Descrizione**: il nome del cluster *NomeCluster* non è disponibile. Scegliere un altro nome.
-   **Soluzione**: l'utente deve specificare un nome di cluster che sia univoco e insistente, quindi riprovare. Se utilizza il portale, durante la procedura di creazione viene indicato se un nome di cluster è già in uso.

### ClusterPasswordInvalid

-   **Descrizione**: la password del cluster non è valida. La password deve essere costituita da almeno 10 caratteri e deve contenere almeno un numero, una lettera maiuscola, una lettera minuscola e un carattere speciale senza spazi e non deve contenere al suo interno il nome utente.
-   **Soluzione**: fornire una password valida per il cluster e riprovare.

### ClusterUserNameInvalid

-   **Descrizione**: il nome utente del cluster non è valido. Assicurarsi che il nome utente non contenga caratteri speciali o spazi.
-   **Soluzione**: fornire un nome utente valido per il cluster e riprovare.

### ClusterUserNameInvalidReservedWord

-   **Descrizione**: il nome DNS del cluster *NomeClusterDns* non è valido. Il nome deve iniziare e finire con un carattere alfanumerico e può contenere solo il carattere speciale '-'.
-   **Soluzione**: specificare un nome utente valido per il cluster e riprovare.

### ContainerNameMisMatchWithDnsName

-   **Descrizione**: il nome del contenitore nell'URI *URIContenitore* e il nome DNS *NomeDns* nel corpo della richiesta devono corrispondere.
-   **Soluzione**: verificare che il nome del contenitore e il nome DNS utilizzati siano uguali, quindi riprovare.

### DataNodeDefinitionNotFound

-   **Descrizione**: configurazione del cluster non valida. Impossibile trovare le definizioni del nodo dati nelle dimensioni del nodo.
-   **Soluzione**: riprovare.

### DeploymentDeletionFailure

-   **Descrizione**: eliminazione della distribuzione non riuscita per il cluster.
-   **Soluzione**: ripetere l'operazione di eliminazione.

### DnsMappingNotFound

-   **Descrizione**: errore di configurazione del servizio. Informazioni di mapping DNS richieste non trovate.
-   **Soluzione**: eliminare il cluster e crearne uno nuovo.

### DuplicateClusterContainerRequest

-   **Descrizione**: tentativo di creazione di contenitore cluster duplicato. Esiste un record per *NomeContenitore* ma gli Etag non corrispondono.
-   **Soluzione**: specificare un nome univoco per il contenitore e ripetere l'operazione di creazione.

### DuplicateClusterInHostedService

-   **Descrizione**: il servizio ospitato *NomeServizioOspitato* contiene già un cluster. Un servizio ospitato non può contenere più cluster.
-   **Soluzione**: ospitare il cluster in un altro servizio ospitato.

### FailureToUpdateDeploymentStatus

-   **Descrizione**: impossibile aggiornare lo stato della distribuzione del cluster.
-   **Soluzione**: riprovare. Se il problema persiste, contattare il servizio di assistenza clienti.

### HdiRestoreClusterAltered

-   **Descrizione**: il cluster *NomeCluster* è stato eliminato durante la manutenzione. Ricreare il cluster.
-   **Soluzione**: ricreare il cluster.

### HeadNodeConfigNotFound

-   **Descrizione**: configurazione del cluster non valida. Impossibile trovare la configurazione del nodo head richiesta nelle dimensioni dei nodi.
-   **Soluzione**: riprovare.

### HostedServiceCreationFailure

-   **Descrizione**: impossibile creare il servizio ospitato *NomeServizioOspitato*. Ripetere la richiesta.
-   **Soluzione**: ripetere la richiesta.

### HostedServiceHasProductionDeployment

-   **Descrizione**: il servizio ospitato *NomeServizioOspitato* include già una distribuzione di produzione. Un servizio ospitato non può contenere più distribuzioni di produzione. Ripetere la richiesta con un nome di cluster diverso.
-   **Soluzione**: utilizzare un nome di cluster diverso e ripetere la richiesta.

### HostedServiceNotFound

-   **Descrizione**: impossibile trovare il servizio ospitato *NomeServizioOspitato* per il cluster.
-   **Soluzione**: se il cluster è in stato di errore, eliminarlo e riprovare.

### HostedServiceWithNoDeployment

-   **Descrizione**: al servizio ospitato *NomeServizioOspitato* non è associata alcuna distribuzione.
-   **Soluzione**: se il cluster è in stato di errore, eliminarlo e riprovare.

### InsufficientResourcesCores

-   **Descrizione**: nell'ID sottoscrizione *IDSottoscrizione* non restano core disponibili per la creazione del cluster *NomeCluster*. Richiesto: *RisorseRichieste*, Disponibile: *RisorseDisponibili*.
-   **Soluzione**: liberare risorse nella sottoscrizione o aumentare le risorse disponibili nella sottoscrizione, quindi provare nuovamente a creare il cluster.

### InsufficientResourcesHostedServices

-   **Descrizione**: nell'ID sottoscrizione *IDSottoscrizione* non è disponibile una quota per un nuovo servizio ospitato per la creazione del cluster *NomeCluster*.
-   **Soluzione**: liberare risorse nella sottoscrizione o aumentare le risorse disponibili nella sottoscrizione, quindi provare nuovamente a creare il cluster.

### InternalErrorRetryRequest

-   **Descrizione**: si è verificato un errore interno del server. Ripetere la richiesta.
-   **Soluzione**: ripetere la richiesta.

### InvalidAzureStorageLocation

-   **Descrizione**: la posizione di archiviazione di Azure *NomeAreaDati* non è una posizione valida. Verificare che l'area sia corretta e ripetere la richiesta.
-   **Soluzione**: Selezionare una posizione di archiviazione che supporti HDInsight, verificare che il cluster si trovi nella stessa posizione e riprovare.

### InvalidNodeSizeForDataNode

-   **Descrizione**: Dimensione della macchina virtuale non valida per i nodi dati. Per tutti i nodi dati è supportata solo la dimensione grande della macchina virtuale.
-   **Soluzione**: specificare la dimensione nodo supportata per il nodo dati e riprovare.

### InvalidNodeSizeForHeadNode

-   **Descrizione**: dimensione macchina virtuale non valida per il nodo head. Per il nodo head è supportata solo la dimensione molto grande della macchina virtuale.
-   **Soluzione**: specificare la dimensione supportata per il nodo head e riprovare.

### InvalidRightsForDeploymentDeletion

-   **Descrizione**: l'ID sottoscrizione *IDSottoscrizione* in uso non dispone di autorizzazioni sufficienti per eseguire l'operazione di eliminazione per il cluster *NomeCluster*.
-   **Soluzione**: se il cluster è in stato di errore, rilasciarlo e riprovare.

### InvalidStorageAccountBlobContainerName

-   **Descrizione**: il nome del contenitore BLOB dell'account di archiviazione esterno non è valido *NomeContenitore* non è valido. Verificare che il nome inizi con una lettera e contenga solo lettere minuscole, numeri e trattini.
-   **Soluzione**: specificare un nome valido per il contenitore BLOB dell'account di archiviazione e riprovare.

### InvalidStorageAccountConfigurationSecretKey

-   **Descrizione**: per configurare l'account di archiviazione esterno *NomeAccountArchiviazione* è necessario impostare i dettagli della chiave privata.
-   **Soluzione**: specificare una chiave privata valida per l'account di archiviazione e riprovare.

### InvalidVersionHeaderFormat

-   **Descrizione**: l'intestazione della versione *IntestazioneVersione* non è nel formato valido aaaa-mm-gg.
-   **Soluzione**: specificare un formato valido per l'intestazione della versione e ripetere la richiesta.

### MoreThanOneHeadNode

-   **Descrizione**: configurazione del cluster non valida. Sono state rilevate più configurazioni di nodi head.
-   **Soluzione**: modificare la configurazione in modo da specificare un solo nodo head.

### OperationTimedOutRetryRequest

-   **Descrizione**: l'operazione non è stata completata nel tempo consentito o entro il numero massimo di tentativi possibili. Ripetere la richiesta.
-   **Soluzione**: ripetere la richiesta.

### ParameterNullOrEmpty

-   **Descrizione**: il parametro *NomeParametro* non può essere null o vuoto.
-   **Soluzione**: specificare un valore valido per il parametro.

### PreClusterCreationValidationFailure

-   **Descrizione**: una o più richieste di creazione cluster non sono valide. Verificare che i valori di input siano corretti e ripetere la richiesta.
-   **Soluzione**: Verificare che i valori di input siano corretti e ripetere la richiesta.

### RegionCapabilityNotAvailable

-   **Descrizione**: la funzionalità relativa all'area geografica non è disponibile per l'area *NomeAreaGeografica* e l'ID sottoscrizione *IDSottoscrizione*.
-   **Soluzione**: specificare un'area geografica che supporta i cluster HDInsight. Le aree geografiche supportate pubblicamente sono: Stati Uniti orientali, Stati Uniti occidentali, Europa settentrionale

### StorageAccountNotColocated

-   **Descrizione**: l'account di archiviazione *NomeAccountArchiviazione* si trova nell'area *NomeAreaGeograficaCorrente*. Dovrebbe corrispondere all'area del cluster *NomeAreaGeograficaCluster*.
-   **Soluzione**: specificare un account di archiviazione nella stessa area del cluster oppure, se i dati si trovano già nell'account di archiviazione, creare un nuovo cluster nella stessa area dell'account di archiviazione esistente. Se si utilizza il portale, questo problema verrà segnalato in anticipo.

### SubscriptionIdNotActive

-   **Descrizione**: l'ID sottoscrizione specificato *IDSottoscrizione* non è attivo.
-   **Soluzione**: riattivare la sottoscrizione oppure ottenere una nuova sottoscrizione valida.

### SubscriptionIdNotFound

-   **Descrizione**: impossibile trovare l'ID sottoscrizione specificato *IDSottoscrizione*.
-   **Soluzione**: Verificare che l'ID sottoscrizione sia valido e riprovare.

### UnableToResolveDNS

-   **Descrizione**: impossibile risolvere il DNS *URLDNS*. Assicurarsi che sia specificato l'URL completo dell'endpoint BLOB.
-   **Soluzione**: specificare un URL BLOB valido. L'URL DEVE essere valido in tutte le sue parti, iniziare con *http://* e finire con *.com*. L'URL completo di solito si trova nella scheda Storage del portale di gestione di Azure.

### UnableToVerifyLocationOfResource

-   **Descrizione**: impossibile verificare il percorso della risorsa *URLDNS*. Assicurarsi che sia specificato l'URL completo dell'endpoint BLOB.
-   **Soluzione**: specificare un URL BLOB valido. L'URL DEVE essere valido in tutte le sue parti, iniziare con *http://* e finire con *.com*. L'URL completo di solito si trova nella scheda Storage del portale di gestione di Azure.

### VersionCapabilityNotAvailable

-   **Descrizione**: la funzionalità relativa alla versione non è disponibile per la versione *VersioneSpecificata* e l'ID sottoscrizione *IDSottoscrizione*.
-   **Soluzione**: scegliere una versione disponibile e riprovare.

### VersionNotSupported

-   **Descrizione**: la versione *VersioneSpecificata* non è supportata.
-   **Soluzione**: scegliere una versione supportata e riprovare.

### VersionNotSupportedInRegion

-   **Descrizione**: la versione *VersioneSpecificata* non è disponibile nell'area di Azure *AreaGeograficaSpecificata*.
-   **Soluzione**: scegliere una versione supportata nell'area geografica specificata e riprovare.

### WasbAccountConfigNotFound

-   **Descrizione**: configurazione del cluster non valida. Impossibile trovare la configurazione dell'account WASB negli account esterni.
-   **Soluzione**: verificare che l'account sia esistente e specificato correttamente nella configurazione, quindi riprovare.

Ulteriori risorse di debug
--------------------------

-   [Documentazione di Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png


