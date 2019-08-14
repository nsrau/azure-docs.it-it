---
title: Risoluzione dei problemi Azure Data Factory | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi alle attività di controllo esterno in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 1995ce2a91bfbc115f80c99687cc84b52ef614ec
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950102"
---
# <a name="troubleshoot-azure-data-factory"></a>Risoluzione dei problemi Azure Data Factory

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per le attività di controllo esterno in Azure Data Factory.

## <a name="azure-databricks"></a>Azure Databricks

| Codice di errore | Messaggio di errore                                          | Descrizione                             | Recommendation                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Errore 403.                                                    | Il token di accesso di databricks è scaduto.                         | Per impostazione predefinita, il token di accesso databricks è valido per 90 giorni.  Creare un nuovo token e aggiornare il servizio collegato. |
| 3201           | Campo obbligatorio mancante: Settings. Task. notebook_task. notebook_path | Creazione non valida: Il percorso del notebook non è stato specificato correttamente. | Specificare il percorso del notebook nell'attività databricks. |
| 3201           | Cluster... non esiste.                                 | Errore di creazione: Il cluster databricks non esiste o è stato eliminato. | Verificare che il cluster databricks esista. |
| 3201           | URI del file Python non valido... Per gli schemi URI supportati, vedere la guida dell'utente di databricks. | Creazione non valida.                                                | Specificare percorsi assoluti per gli schemi di indirizzamento dell'area `dbfs:/folder/subfolder/foo.py` di lavoro o per i file archiviati nel file System di databricks. |
| 3201           | {0}LinkedService deve avere Domain e accessToken come proprietà obbligatorie. | Creazione non valida.                                                | Verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}LinkedService deve specificare l'ID cluster esistente o le informazioni sul nuovo cluster per la creazione. | Creazione non valida.                                                | Verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Il tipo di nodo Standard_D16S_v3 non è supportato. Tipi di nodo supportati:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | Creazione non valida.                                                | Fare riferimento al messaggio di errore.                                          |
| 3201           | Notebook_path non valido:... Attualmente sono supportati solo i percorsi assoluti. I percorsi devono iniziare con '/'. | Creazione non valida.                                                | Fare riferimento al messaggio di errore.                                          |
| 3202           | Sono già stati creati 1000 processi negli ultimi 3600 secondi, superando il limite di velocità:   1000 creazioni di processi per 3600 secondi. | Un numero eccessivo di databricks viene eseguito in un'ora.                         | Controllare tutte le pipeline che usano l'area di lavoro databricks per la velocità di creazione del processo.  Se le pipeline avviate un numero eccessivo di databricks vengono eseguite in modo aggregato, eseguire la migrazione di alcune pipeline in una nuova area di lavoro. |
| 3202           | Non è stato possibile analizzare l'oggetto richiesta: Sono previsti ' Key ' è value ' da impostare per il campo mappa JSON base_parameters, Got ' Key: "..."' invece. | Errore di creazione: Nessun valore specificato per il parametro.         | Esaminare il codice JSON della pipeline e verificare che tutti i parametri nel notebook di baseParameters specifichino un valore non vuoto. |
| 3202           | Utente: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` non è autorizzato ad accedere al cluster. | L'utente che ha generato il token di accesso non è autorizzato ad accedere al cluster databricks specificato nel servizio collegato. | Verificare che l'utente disponga delle autorizzazioni necessarie nell'area di lavoro.   |
| 3203           | Il cluster è in stato terminato, non disponibile per la ricezione di processi. Correggere il cluster o riprovare più tardi. | Il cluster è stato terminato.    Per i cluster interattivi, potrebbe trattarsi di un race condition. | Il modo migliore per evitare questo errore consiste nell'usare i cluster di processi.             |
| 3204           | Esecuzione del processo non riuscita.  | I messaggi di errore indicano vari problemi, ad esempio uno stato del cluster imprevisto o un'attività specifica. Spesso non viene visualizzato alcun messaggio di errore.                                                          | N/D                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.

La tabella seguente si applica a U-SQL.

| Codice di errore         | Messaggio di errore                                                | Descrizione                                          | Recommendation                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Il token di accesso è del tenant errato.                    | Tenant di Azure Active Directory (Azure AD) errato.                                         | L'entità servizio usata per accedere a Azure Data Lake Analytics appartiene a un altro tenant di Azure AD. Creare una nuova entità servizio nello stesso tenant dell'account Data Lake Analytics. |
| 2711,   2705,   2704 | Accesso negato. Verifica ACL non riuscita. La risorsa non esiste o l'utente non è autorizzato a eseguire l'operazione richiesta.<br/><br/>L'utente non è in grado di accedere Data Lake Store.  <br/><br/>L'utente non è autorizzato a utilizzare Data Lake Analytics. | L'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione. | Verificare che l'entità servizio o il certificato fornito dall'utente per i processi di Data Lake Analytics abbia accesso all'account di Data Lake Analytics e all'istanza di Data Lake Storage predefinita dalla cartella radice. |
| 2711                 | Impossibile trovare il file o la cartella ' Azure Data Lake Store '.       | Il percorso del file U-SQL non è corretto oppure le credenziali del servizio collegato non hanno accesso. | Verificare il percorso e le credenziali fornite nel servizio collegato. |
| 2707                 | Non è possibile risolvere l'account di AzureDataLakeAnalytics. Verificare ' AccountName ' è DataLakeAnalyticsUri '. | L'account Data Lake Analytics nel servizio collegato non è corretto.                  | Verificare che sia specificato l'account corretto.             |
| 2703                 | ID errore: E_CQO_SYSTEM_INTERNAL_ERROR (o qualsiasi errore che inizia con "ID errore:"). | L'errore è stato Data Lake Analytics.                                    | Un errore come l'esempio indica che il processo è stato inviato a Data Lake Analytics e che lo script non è riuscito. Esaminare Data Lake Analytics. Nel portale passare all'account Data Lake Analytics e cercare il processo usando l'ID esecuzione dell'attività Data Factory (non l'ID di esecuzione della pipeline). Il processo contiene ulteriori informazioni sull'errore e consente di risolvere i problemi. Se la risoluzione non è chiara, contattare il team di supporto di Data Lake Analytics e specificare l'URL del processo, che include il nome dell'account e l'ID del processo. |
| 2709                 | Al momento non è possibile accettare il processo. Il numero massimo di processi in coda per l'account è 200. | Questo errore è causato dalla limitazione delle richieste Data Lake Analytics.                                           | Ridurre il numero di processi inviati a Data Lake Analytics modificando Data Factory trigger e le impostazioni di concorrenza per le attività. In alternativa, aumentare i limiti per Data Lake Analytics. |
| 2709                 | Questo processo è stato rifiutato perché richiede 24 AUs. Il criterio definito dall'amministratore di questo account impedisce a un processo di usare più di 5 unità di allocazione. | Questo errore è causato dalla limitazione delle richieste Data Lake Analytics.                                           | Ridurre il numero di processi inviati a Data Lake Analytics modificando Data Factory trigger e le impostazioni di concorrenza per le attività. In alternativa, aumentare i limiti per Data Lake Analytics. |



## <a name="azure-functions"></a>Funzioni di Azure

| Codice di errore | Messaggio di errore                           | Descrizione                                                  | Recommendation                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Il contenuto della risposta non è un JObject valido. | La funzione di Azure chiamata non ha restituito un payload JSON nella risposta. L'attività della funzione di Azure in Data Factory supporta solo il contenuto della risposta JSON. | Aggiornare la funzione di Azure per restituire un payload JSON valido. Ad esempio, una C# funzione può restituire `(ActionResult)new<OkObjectResult("{` \"ID\":\"123\".`}");` |
| 3600         | HttpMethod:'.. .' non valido.               | Il metodo HTTP specificato nel payload dell'attività non è supportato dall'attività funzione di Azure. | Usare un metodo HTTP supportato, ad esempio PUT, POST, GET, DELETE, OPTIONS, HEAD o TRACE. |



## <a name="custom"></a>Personalizzato

La tabella seguente si applica a Azure Batch.

| Codice di errore | Messaggio di errore                                                | Descrizione                                                  | Recommendation                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Impossibile eseguire l'eccezione imprevista ed esecuzione.             | Non è possibile avviare il comando oppure il programma ha restituito un codice di errore. | Verificare che il file eseguibile esista. Se il programma è stato avviato, assicurarsi che *stdout. txt* e *stderr. txt* siano stati caricati nell'account di archiviazione. È consigliabile creare log copii nel codice per il debug. |
| 2501         | Non è possibile accedere all'account batch utente; verificare le impostazioni dell'account batch. | Il nome del pool o la chiave di accesso batch non è corretta.            | Verificare il nome del pool e la chiave di accesso batch nel servizio collegato. |
| 2502         | Non è possibile accedere all'account di archiviazione utente; verificare le impostazioni dell'account di archiviazione. | Nome dell'account di archiviazione o chiave di accesso non corretta.       | Verificare il nome dell'account di archiviazione e la chiave di accesso nel servizio collegato. |
| 2504         | L'operazione ha restituito un codice di stato non valido ' richiesta non valida '.     | Troppi file nel folderPath dell'attività personalizzata. La dimensione totale di resourceFiles non può contenere più di 32.768 caratteri. | Rimuovere i file non necessari. Oppure è possibile eseguirne il zip e aggiungere un comando unzip per estrarli. Ad esempio, usare`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | Impossibile creare la firma di accesso condiviso a meno che non vengano utilizzate le credenziali della chiave dell'account. | Le attività personalizzate supportano solo gli account di archiviazione che usano una chiave di accesso. | Vedere la descrizione dell'errore.                                            |
| 2507         | Il percorso della cartella non esiste o è vuoto:....            | Nessun file si trova nell'account di archiviazione nel percorso specificato.       | Il percorso della cartella deve contenere i file eseguibili che si desidera eseguire. |
| 2508         | Sono presenti file duplicati nella cartella delle risorse.               | Più file con lo stesso nome si trovano in sottocartelle diverse di folderPath. | Le attività personalizzate appiattiscono la struttura di cartelle in folderPath.  Se è necessario mantenere la struttura di cartelle, comprimere i file ed estrarli in Azure Batch usando un comando unzip. Ad esempio, usare`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | URL batch... non è valido; deve essere in formato URI.         | Gli URL batch devono essere simili a`https://mybatchaccount.eastus.batch.azure.com` | Vedere la descrizione dell'errore.                                            |
| 2510         | Si è verificato un errore durante l'invio della richiesta.               | L'URL del batch non è valido.                                         | Verificare l'URL del batch.                                            |

## <a name="hdinsight"></a>HDInsight

La tabella seguente si applica a Spark, hive, MapReduce, Pig e Hadoop Streaming.

| Codice di errore | Messaggio di errore                                                | DESCRIZIONE                                                  | Recommendation                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Invio del processo Hadoop non riuscito. Errore: Non è stato possibile risolvere il nome remoto. <br/><br/>Il cluster non è stato trovato. | L'URI del cluster specificato non è valido.                              | Verificare che il cluster non sia stato eliminato e che l'URI fornito sia corretto. Quando si apre l'URI in un browser, viene visualizzata l'interfaccia utente di Ambari. Se il cluster si trova in una rete virtuale, l'URI deve essere l'URI privato. Per aprirlo, usare una macchina virtuale che fa parte della stessa rete virtuale. Per ulteriori informazioni, vedere la pagina relativa [alla connessione diretta ai servizi Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | Invio del processo Hadoop non riuscito. Processo:..., cluster:.../. Errore: Un'attività è stata annullata. | Timeout dell'invio del processo.                         | Il problema potrebbe essere la connettività generale HDInsight o la connettività di rete. Verificare innanzitutto che l'interfaccia utente di HDInsight Ambari sia disponibile da qualsiasi browser. Verificare che le credenziali siano ancora valide. Se si usa il runtime di integrazione self-hosted (IR), assicurarsi di eseguire questa operazione dalla macchina virtuale o dal computer in cui è installato il runtime di integrazione self-hosted. Provare quindi a inviare di nuovo il processo da Data Factory. Se il problema persiste, contattare il team di Data Factory per assistenza. |
| 2300         | Autorizzato   Il nome utente o la password di Ambari non è corretta  <br/><br/>Autorizzato   L'amministratore utente è bloccato in Ambari.   <br/><br/>403-accesso negato: Accesso negato. | Le credenziali per HDInsight sono errate o scadute. | Correggere le credenziali e ridistribuire il servizio collegato. Assicurarsi prima di tutto che le credenziali funzionino in HDInsight aprendo l'URI del cluster in qualsiasi browser e tentando di eseguire l'accesso. Se le credenziali non funzionano, è possibile reimpostarle dalla portale di Azure. |
| 2300,   2310 | 502 - Il server Web, in funzione come gateway o proxy, ha ricevuto una risposta non valida.       <br/>Gateway non valido. | Questo errore è di HDInsight.                               | Questo errore viene dal cluster HDInsight. Per altre informazioni, vedere [errore 502 dell'interfaccia utente di Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 errori di connessione al server di spark Thrifty](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 errori di connessione al server di risparmio di Spark](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)e [risoluzione degli errori del gateway non valido nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502). |
| 2300         | Invio del processo Hadoop non riuscito. Processo:..., cluster:... Errore: {\"Error\":\"Impossibile soddisfare la richiesta di invio del processo perché il servizio Templeton è occupato con un numero eccessivo di richieste di processi di invio. Attendere qualche minuto prima di riprovare l'operazione. Per configurare le richieste simultanee, fare riferimento alla configurazione Templeton. parallellism. job. Submit.  <br/><br/>Invio del processo Hadoop non riuscito. Processo: 161da5d4-6fa8-4EF4-A240-6b6428c5ae2f, cluster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Errore: {\"Error\":\"Java. io. IOException: org. Apache. Hadoop. Yarn. Exceptions. yarnexception: Non è stato possibile inviare application_1561147195099_3730 a YARN: org. Apache. Hadoop. Security. AccessControlException: Radice coda. joblauncher dispone già di 500 applicazioni, non è in grado di accettare l'invio dell'applicazione: application_1561147195099_3730 \ | È in corso l'invio di un numero eccessivo di processi a HDInsight nello stesso momento. | È consigliabile limitare il numero di processi simultanei inviati a HDInsight. Fare riferimento a Data Factory concorrenza di attività se i processi vengono inviati dalla stessa attività. Modificare i trigger in modo che le esecuzioni di pipeline simultanee vengano distribuite nel tempo. Vedere la documentazione di HDInsight per `templeton.parallellism.job.submit` modificare come suggerito dall'errore. |
| 2303,   2347 | Il processo Hadoop non è riuscito con codice di uscità 5'. Per ulterioriwasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderrinformazioni, vedere ''.  <br/><br/>L'esecuzione di hive non è riuscita con codice errore ' UserErrorHiveOdbcCommandExecutionFailure '.   Per ulterioriwasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.outinformazioni, vedere ''. | Il processo è stato inviato a HDInsight e non è riuscito in HDInsight. | Il processo è stato inviato a HDInsight correttamente. Errore nel cluster. Aprire il processo e i log nell'interfaccia utente di HDInsight Ambari o aprire il file dalla risorsa di archiviazione come suggerito dal messaggio di errore. Il file Mostra i dettagli dell'errore. |
| 2328         | Si è verificato un errore interno del server durante l'elaborazione della richiesta. Ripetere la richiesta o contattare il supporto tecnico. | Questo errore si verifica in HDInsight su richiesta.                              | Questo errore deriva dal servizio HDInsight quando il provisioning di HDInsight ha esito negativo. Contattare il team di HDInsight e specificare il nome del cluster su richiesta. |
| 2310         | java.lang.NullPointerException                               | Questo errore si verifica quando il processo viene inviato a un cluster Spark.      | Questa eccezione deriva da HDInsight. Nasconde il problema effettivo. Per assistenza, contattare il team di HDInsight. Specificare il nome del cluster e l'intervallo di tempo di esecuzione dell'attività. |
|              | Tutti gli altri errori                                             |                                                              | Vedere la pagina relativa alla [risoluzione dei problemi usando](../hdinsight/hdinsight-troubleshoot-guide.md) le [domande frequenti](https://hdinsight.github.io/)su HDInsight e HDInsight. |



## <a name="web-activity"></a>Attività Web

| Codice di errore | Messaggio di errore                                                | Descrizione                                                  | Recommendation                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | HttpMethod:'.. .' non valido.                                    | L'attività Web non supporta il metodo HTTP specificato nel payload dell'attività. | I metodi HTTP supportati sono PUT, POST, GET e DELETE. |
| 2108         | Errore server 500 non valido.                                     | Errore interno sull'endpoint.                               | Per verificare la funzionalità sull'URL, usare Fiddler o postazione. |
| 2108         | 401 non autorizzato.                                             | Manca l'autenticazione valida per la richiesta.                      | Il token potrebbe essere scaduto. Fornire un metodo di autenticazione valido. Per verificare la funzionalità sull'URL, usare Fiddler o postazione. |
| 2108         | Non consentito 403.                                                | Mancano le autorizzazioni necessarie.                                 | Controllare le autorizzazioni utente per la risorsa a cui si accede. Per verificare la funzionalità sull'URL, usare Fiddler o postazione.  |
| 2108         | Richiesta non valida 400.                                              | Richiesta HTTP non valida.                                         | Controllare l'URL, il verbo e il corpo della richiesta. Usare Fiddler o subpost per convalidare la richiesta.  |
| 2108         | 404 non trovato.                                                | La risorsa non è stata trovata.                                       | Usare Fiddler o subpost per convalidare la richiesta.  |
| 2108         | Il servizio non è disponibile.                                          | Il servizio non è disponibile.                                       | Usare Fiddler o subpost per convalidare la richiesta.  |
| 2108         | Tipo di supporto non supportato.                                       | Il tipo di contenuto non corrisponde al corpo dell'attività Web.           | Specificare il tipo di contenuto che corrisponde al formato del payload. Usare Fiddler o subpost per convalidare la richiesta. |
| 2108         | La risorsa che si sta cercando è stata rimossa, il nome è stato modificato o è temporaneamente non disponibile. | La risorsa non è disponibile.                                | Per controllare l'endpoint, usare Fiddler o postazione. |
| 2108         | Non è possibile visualizzare la pagina cercata perché è in uso un metodo non valido (verbo HTTP). | Nella richiesta è stato specificato un metodo di attività Web errato.   | Per controllare l'endpoint, usare Fiddler o postazione. |
| 2108         | invalid_payload                                              | Il corpo dell'attività Web non è corretto.                       | Per controllare l'endpoint, usare Fiddler o postazione. |

Per usare Fiddler per creare una sessione HTTP dell'applicazione Web monitorata:

1. Scaricare, installare e aprire [Fiddler](https://www.telerik.com/download/fiddler).

1. Se l'applicazione Web usa HTTPS, passare a **strumenti** > **Fiddler opzioni** > **https**. Selezionare **Acquisisci HTTPS connette** e decrittografare il **traffico HTTPS**. 
   
   ![Opzioni Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se l'applicazione usa certificati SSL, aggiungere il certificato Fiddler al dispositivo. Passare a **strumenti** > **Fiddler opzioni** > **azione** > HTTPS Esporta certificatoradicesuldesktop. > 

1. Per disattivare l'acquisizione, passare a **file** > **Acquisisci traffico**. Oppure premere **F12**.

1. Cancellare la cache del browser in modo che tutti gli elementi memorizzati nella cache vengano rimossi e che sia necessario scaricarli nuovamente.

1. Creare una richiesta: 

   a. Selezionare la scheda Composer.

   b. Impostare il metodo e l'URL HTTP.

   c. Aggiungere intestazioni e un corpo della richiesta, se necessario.

   d. Scegliere **Execute**(Esegui).

9. Riattivare l'acquisizione del traffico e completare la transazione problematica nella pagina.

10. Passare a **file** > **Salva** > **tutte le sessioni**.

Per ulteriori informazioni, vedere la pagina relativa all' [Introduzione a Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)



