---
title: Risoluzione dei problemi relativi a Data Factory di Azure | Microsoft Docs
description: Risoluzione dei problemi di Data Factory di Azure. Documento comune per tutti i esterno, le attività di controllo.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshoot
ms.subservice: troubleshoot
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 8d6ab565098e1ea40ede5c650f05e670a1edc7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452683"
---
# <a name="troubleshooting-azure-data-factory"></a>Risoluzione dei problemi relativi a Data Factory di Azure
Questo articolo elenca domande frequenti sulla risoluzione dei problemi.

- [Azure Databricks (notebook, file con estensione jar, python)](#azure-databricks)
- [Azure Data Lake Analitica (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Funzioni di Azure](#azure-functions)
- [Personalizzato (Batch di Azure)](#custom-azure-batch)
- [HDInsight (Spark, Hive, MapReduce, Pig, Hadoop Streaming)](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Attività Web](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| Codice di errore | Messaggio di errore                                          | Descrizione del problema                             | Correzione di possibili / azione consigliata                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Errore 403                                                    | Token di accesso di Databricks è scaduto.                         | Per impostazione predefinita, il token di accesso di Databricks è valido per 90 giorni.  Creare un nuovo token e aggiornare il servizio collegato. |
| 3201           | Campo richiesto mancante: settings.task.notebook_task.notebook_path | Cattivi creazione: Percorso del notebook non è specificato correttamente. | Specificare percorso del notebook nell'attività di Databricks. |
| 3201           | grappolo... non esiste                                 | Errore di creazione: Il cluster Databricks non esiste o è stato eliminato. | Verificare che esista nel cluster Databricks. |
| 3201           | URI del file python non è valido:... Visitare Databricks manuale dell'utente per gli schemi URI supportati. | Cattivi authoring                                                | Specificare i percorsi assoluti per gli schemi di indirizzamento dell'area di lavoro o "dbfs:/folder/subfolder/foo.py" per i file archiviati DBFS. |
| 3201           | {0}   Servizio collegato deve avere accessToken e dominio come proprietà obbligatorie | Cattivi authoring                                                | Verificare che [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}   Servizio collegato deve specificare Id di cluster esistenti o nuove informazioni del cluster per la creazione | Cattivi authoring                                                | Verificare che [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Tipo di nodo Standard_D16S_v3 non è supportato. Tipi di nodo supportati:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2 | Cattivi authoring                                                | Fare riferimento a messaggio di errore                                          |
| 3201           | Non è valido notebook_path:... Sono attualmente supportati solo i percorsi assoluti. I percorsi devono iniziare con '/'. | Cattivi authoring                                                | Fare riferimento a messaggio di errore                                          |
| 3202           | Esistevano già 1000 processi creati nelle ultime 3600 secondi, che supera il limite di velocità:   1000 le creazioni di processi per ogni 3600 secondi. | Viene eseguito un numero eccessivo di Databricks in un'ora.                         | Controllare tutte le pipeline che usano questa area di lavoro di Databricks per la frequenza di creazione processo.  Se le pipeline avviata troppi esecuzioni di Databricks in forma aggregata, eseguire la migrazione di alcune pipeline per una nuova area di lavoro. |
| 3202           | Impossibile analizzare l'oggetto richiesta: Previsto 'key' e 'value' da impostare per JSON mappa campo base_parameters, ricevuto ' chiave: ""... ' In alternativa. | Errore di creazione: Nessun valore specificato per il parametro         | Analizzare json della pipeline e verificare che tutti i parametri nel Notebook baseParameters hanno specificato un valore non vuoto. |
| 3202           | Utente: SimpleUserContext{userId=..., name=user@company.com, orgId=…} non è autorizzato ad accedere a cluster | Utente che ha generato il token di accesso non è autorizzato per accedere al cluster Databricks specificato nel servizio collegato. | Verificare che l'utente abbia le autorizzazioni necessarie nell'area di lavoro.   |
| 3203           | Il cluster è stato terminato, non disponibile per ricevere i processi. . Correggere il cluster o riprovare più tardi. | Cluster è stato terminato.    Per un cluster interattivo, potrebbe trattarsi di una race condition. | Il modo migliore per evitare questo problema consiste nell'utilizzare i cluster di processo.             |
| 3204           | Esecuzione del processo non riuscito. Può esistere un numero qualsiasi di messaggi di errore dallo stato cluster imprevisto al messaggio attività specifiche.  La maggior parte dei comuni non sono affatto alcun messaggio di errore. | N/D                                                          | N/D                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analitica (U-SQL)

| Codice di errore         | Messaggio di errore                                                | Descrizione del problema                                          | Correzione di possibili / azione consigliata                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Il token di accesso proviene da tenant sbagliato                    | Tenant AAD non corretto                                         | A un altro tenant AAD appartiene l'entità di servizio utilizzato per l'accesso di Azure Data Lake Analytics. Creare nuove entità di servizio nello stesso tenant come account di Azure Data Lake Analytics. |
| 2711,   2705,   2704 | Forbidden. Verifica di ACL non riuscita. La risorsa non esiste o l'utente non è autorizzato a eseguire l'operazione richiesta<br/><br/>Utente non è in grado di accedere a data Lake store  <br/><br/>Utente non è autorizzato a analitica di data lake | L'entità servizio o il certificato fornito non ha accesso al file in archiviazione | Assicurarsi che l'entità servizio o certificato che forniscono per i processi di Azure Data Lake Analytics ha accesso all'account Azure Data Lake Analytics e l'archiviazione di Azure Data Lake Store predefinito per tale dalla cartella radice. |
| 2711                 | Non è possibile trovare il file 'Azure Data Lake Store' o una cartella       | Il percorso di file u-SQL è errato o il servizio collegato non hanno accesso le credenziali | Verificare che il percorso e le credenziali fornite nel servizio collegato |
| 2707                 | Non è possibile risolvere l'account di AzureDataLakeAnalytics. . Controllare 'AccountName' e 'Con l'URI'. | L'account Azure Data Lake Analytics nel servizio collegato non è corretto                  | Verificare che venga fornito l'account giusto             |
| 2703                 | Id errore: E_CQO_SYSTEM_INTERNAL_ERROR o qualsiasi errore inizia con "Id errore:" | Errore proviene da Azure Data Lake Analytics                                    | Qualsiasi errore che permette di ottenere l'esempio indica che il processo è stato inviato da Azure Data Lake Analytics e lo script presente non riuscita. In Azure Data Lake Analytics, è necessario eseguire l'analisi. Se si apre il portale e passare all'account di Azure Data Lake Analytics, cercare il processo con Id (non esecuzione della pipeline Id) di esecuzione dell'attività di Azure Data factory. Il processo vi sarà disponibili altre informazioni sull'errore e aiuterà a risolvere i problemi. Se la risoluzione non è chiara, contattare il team di supporto di Azure Data Lake Analytics e fornire l'URL del processo, che include il nome dell'account e l'ID processo. |
| 2709                 | In questo momento non possiamo accettare il processo. Il numero massimo di processi in coda per l'account è 200. | La limitazione delle richieste in Azure Data Lake Analytics                                           | Ridurre il numero di processi inviati a Azure Data Lake Analytics modificando i trigger di Azure Data factory e le impostazioni di concorrenza delle attività o aumentare i limiti in Azure Data Lake Analytics |
| 2709                 | Questo processo è stato rifiutato perché richiede 24 unità di analisi e questo account dispone di un criterio definito dall'amministratore che impedisce che un processo con più di 5 unità di analisi. | La limitazione delle richieste in Azure Data Lake Analytics                                           | Ridurre il numero di processi inviati a Azure Data Lake Analytics modificando i trigger di Azure Data factory e le impostazioni di concorrenza delle attività o aumentare i limiti in Azure Data Lake Analytics |



## <a name="azure-functions"></a>Funzioni di Azure

| Codice di errore | Messaggio di errore                           | Descrizione                                                  | Correzione di possibili / azione consigliata                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Contenuto della risposta non è valido JObject | Ciò significa che la funzione di Azure che è stata chiamata non ha restituito un Payload JSON nella risposta. Attività della funzione di Azure di Azure Data factory supporta solo il contenuto della risposta JSON. | Aggiornare la funzione di Azure per restituire un Payload JSON valido, ad esempio un C# funzione può restituire (ActionResult) new < OkObjectResult ("{`\"Id\":\"123\"`}"); |
| 3600         | Metodo HTTP non è valido: '.. '.               | Ciò significa che il metodo Http specificato nel payload attività non è supportato dall'attività della funzione di Azure. | I metodi Http supportati sono:  <br/>PUT, POST, GET, DELETE, OPZIONI, HEAD, TRACCIA |



## <a name="custom-azure-batch"></a>Personalizzato (Batch di Azure)
| Codice di errore | Messaggio di errore                                                | Descrizione                                                  | Correzione di possibili / azione consigliata                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Eccezione imprevista riscontri e l'esecuzione non riuscita.             | Impossibile avviare il comando o il programma ha restituito un codice di errore. | Verificare che sia presente il file eseguibile. Se viene avviato, controllare di stdout e stderr caricato nell'account di archiviazione. È buona norma creare log abbondanti nel codice per il debug. |
| 2501         | Non è stato possibile accedere account utente di batch, verificare le impostazioni dell'account batch. | Nome non corretto Batch accesso chiave o il pool specificato.            | È necessario verificare il nome del pool e la chiave di accesso Batch al servizio collegato. |
| 2502         | Può non accesso utente account di archiviazione, per controllare le impostazioni di account di archiviazione. | Corretto chiave account di archiviazione nome o l'accesso specificato.       | È necessario verificare account accesso e il nome chiave di archiviazione nel servizio collegato. |
| 2504         | Operazione ha restituito un codice di stato non valido 'Richiesta non valida'     | Numero eccessivo di file in folderPath se l'attività personalizzata.  (Dimensione totale dei resourceFiles non può essere più di 32768 caratteri). | Rimuovere i file non necessari, o comprimerli e aggiungere un comando unzip da estrarre, ad esempio: - nologo - noprofile powershell.exe-comando "& {Add-Type - A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;  $folder\yourProgram.exe |
| 2505         | Non è possibile creare una firma di accesso condiviso a meno che non vengono utilizzate le credenziali di chiave dell'Account. | Le attività personalizzate supportano solo gli account di archiviazione che usano una chiave di accesso. | Fare riferimento descrizione                                            |
| 2507         | Il percorso della cartella non esiste o è vuoto:...            | Nessun file nell'account di archiviazione nel percorso specificato.       | FolderPath deve contenere i file eseguibili che si desidera eseguire. |
| 2508         | Sono presenti file duplicati nella cartella delle risorse.               | Sono presenti più file con lo stesso nome nelle sottocartelle diverse della folderPath. | Le attività personalizzate di rendere flat la struttura di cartelle in folderPath.  Se dovrà essere preservate struttura di cartelle, i file zip ed estrarli in Azure Batch con un comando unzip, ad esempio: - nologo - noprofile powershell.exe-comando "& {Add-Type - A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;   $folder\yourProgram.exe |
| 2509         | Url del batch... è valido, deve essere nel formato dell'Uri.         | URL di batch deve essere simile a https://mybatchaccount.eastus.batch.azure.com | Fare riferimento descrizione                                            |
| 2510         | Si è verificato un errore durante l'invio della richiesta.               | URL del batch non è valido                                         | Verificare l'URL del batch.                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight (Spark, Hive, MapReduce, Pig, Hadoop Streaming)

| Codice di errore | Messaggio di errore                                                | Descrizione                                                  | Correzione di possibili / azione consigliata                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Non è riuscita l'invio di processi Hadoop. Errore: che indica che è impossibile risolvere il nome remoto. <br/><br/>Il cluster non trovato. | URI del cluster specificato non è valido                              | Assicurarsi che il cluster non è stato eliminato, e l'URI fornito sia corretto. È possibile aprire l'URI in qualsiasi browser e verrà visualizzato l'UI Ambari. Se il cluster è in una rete virtuale, quindi l'URI deve essere l'URI privato e tentare di aprirlo da una macchina virtuale che fa parte della stessa rete virtuale. Altre informazioni per [rete virtuale di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | Non è riuscita l'invio di processi Hadoop. Processo:..., Cluster:... /. Errore: Un'attività è stata annullata. | Timeout durante l'invio del processo.                         | Potrebbe trattarsi di problemi di connettività generali HDInsight o problema di connettività di rete. Verificare che l'interfaccia utente di Ambari HDInsight è disponibile tramite qualsiasi browser e le credenziali sono ancora valide. Assicurarsi di eseguire questa operazione dal computer o VM in cui è installato Runtime di integrazione self-hosted se si usa runtime di integrazione self-hosted. Quindi provare a inviare nuovamente il processo da Azure Data factory. Se il problema persiste, contattare il team di Azure Data factory per il supporto. |
| 2300         | Operazione non autorizzata:   Nome utente di Ambari o password non corretta  <br/><br/>Operazione non autorizzata:   Utente amministratore è bloccato in Ambari   <br/><br/>403 - accesso negato: Accesso negato | Credenziali fornite per il HDInsight non sono corrette o sono scadute | . Correggerli e ridistribuire il servizio collegato. Assicurarsi che le credenziali di lavoro in HDInsight prima di tutto, aprire l'URI del cluster in qualsiasi browser e tentare l'accesso. Se non funzionano, è possibile reimpostarle dal portale di Azure. |
| 2300,   2310 | 502 - il server web ha ricevuto una risposta non valida nonostante agisca come gateway o proxy server       <br/>Gateway non valido | Errore provenga da HDInsight                               | L'errore proviene dal cluster HDInsight. Fare riferimento [risoluzione dei problemi di HDInsight](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) con gli errori più comuni.    <br/>Per i cluster Spark potrebbe essere causato anche a causa dell'errore [ciò](https://hdinsight.github.io/spark/spark-thriftserver-errors.html). <br/><br/>[Collegamento aggiuntivo](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2300         | Non è riuscita l'invio di processi Hadoop. Processo:..., Cluster:... Errore: {\"errore\":\"non è possibile soddisfare la richiesta di processo di invio servizio templeton è occupato con troppe richieste di processo di invio. Attendere qualche minuto prima di ritentare l'operazione. Consultare il templeton.parallellism.job.submit config per configurare le richieste simultanee. \  <br/><br/>Non è riuscita l'invio di processi Hadoop. Processo: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster: https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/.   Errore: {\"errore\":\"IOException: org.apache.hadoop.yarn.exceptions.YarnException: Non è riuscito a inviare application_1561147195099_3730 a YARN: org.apache.hadoop.security.AccessControlException: Coda root.joblauncher già dispone di 500 applicazioni, non può accettare l'invio dell'applicazione: application_1561147195099_3730\ | Troppi processi vengono inviati a HDInsight allo stesso tempo | È consigliabile limitare il numero di processi simultanei da inviare per il HDI. Fare riferimento alla concorrenza attività di Azure Data factory se vengono inviati dall'attività stessa. Modificare il trigger in modo che le esecuzioni di pipeline simultanee sono distribuite nel corso del tempo. Per modificare il "templeton.parallellism.job.submit" come errore suggerisce anche fare riferimento alla documentazione di HDInsight. |
| 2303,   2347 | Processo Hadoop non è riuscita con codice di uscita '5'. Vedere 'wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' per altri dettagli.  <br/><br/>Esecuzione di processi hive non è riuscita con codice errore 'UserErrorHiveOdbcCommandExecutionFailure'.   Vedere 'wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' per altri dettagli | HDInsight è stato inviato il processo e non è riuscito in HDInsight | Il processo è stato inviato per HDInsight. Non è riuscito nel cluster. Per aprire il processo nell'interfaccia utente di Ambari, HDInsight e aprire i log non esiste o aprire il file da un archivio come i punti di messaggio di errore out. I dettagli dell'errore sarà in tale file. |
| 2328         | Errore interno del server si è verificato durante l'elaborazione della richiesta. Ripetere la richiesta o contattare il supporto tecnico. | Si verifica su HDInsight su richiesta.                              | Questo errore è provenienti dal servizio HDInsight durante il provisioning di HDInsight ha esito negativo. Contattare il team di HDInsight e fornire loro il nome del cluster su richiesta. |
| 2310         | java.lang.NullPointerException                               | Si è verificato errore durante l'invio di processi al cluster Spark      | Questa eccezione deriva da HDInsight e sta nascondendo il problema effettivo.   Contattare il team di HDInsight per il supporto e fornire loro il nome del cluster e l'intervallo di tempo di esecuzione dell'attività. |
|              | Tutti gli altri errori                                             |                                                              | Consultare [risoluzione dei problemi di HDInsight](../hdinsight/hdinsight-troubleshoot-guide.md) e [domande frequenti su HDInsight](https://hdinsight.github.io/) |



## <a name="web-activity"></a>Attività Web

| Codice di errore | Messaggio di errore                                                | Descrizione                                                  | Correzione di possibili / azione consigliata                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Metodo HTTP non è valido: '.. '.                                    | Ciò significa che il metodo Http specificato nel payload attività non è supportato dall'attività Web. | I metodi Http supportati sono: <br/>PUT, POST, GET, DELETE |
| 2108         | Errore del Server non è valido 500                                     | Errore interno nell'endpoint                               | Controllare le funzionalità nell'URL (con Fiddler o Postman): [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 401 non autorizzato                                             | Autenticazione valido manca nella richiesta                      | Fornire il metodo di autenticazione valido (token potrebbero essere scaduti).   <br/><br/>Controllare le funzionalità nell'URL (con Fiddler o Postman): [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Forbidden 403                                                | Mancano le autorizzazioni necessarie                                 | Controllare le autorizzazioni utente per la risorsa a cui si accede.   <br/><br/>Controllare le funzionalità nell'URL (con Fiddler o Postman): [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 400 di richiesta non valida                                              | Richiesta Http non è valido                                         | Controllare l'URL, un verbo e un corpo della richiesta.   <br/><br/>Utilizzare Fiddler o Postman per convalidare la richiesta: [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 404 non trovato                                                | Risorsa non trovata                                       | Utilizzare Fiddler o Postman per convalidare la richiesta: [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Servizio non disponibile                                          | Il servizio non è disponibile                                       | Utilizzare Fiddler o Postman per convalidare la richiesta: [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Tipo di supporto non supportato                                       | Mancata corrispondenza Content-Type con il corpo di attività Web           | Specificare il Content-Type corretto che corrisponda al formato di payload usare Fiddler o Postman per convalidare la richiesta: [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | La risorsa desiderata per è stata rimossa, rinominata o temporaneamente non disponibile. | La risorsa non è disponibile                                | Usare Fiddler o Postman per controllare l'endpoint: [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Impossibile visualizzare la pagina desiderata perché è utilizzato un metodo non valida (verbo HTTP). | Metodo attività Web non corretto è stato specificato nella richiesta   | Usare Fiddler o Postman per controllare l'endpoint: [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | Il corpo per attività Web non è corretto                       | Usare Fiddler o Postman per controllare l'endpoint: [Come usare Fiddler per creare una sessione HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>Come usare Fiddler per creare una sessione HTTP dell'applicazione web monitorata

1. Scaricare e installare [Fiddler](https://www.telerik.com/download/fiddler)

2. Se l'applicazione web Usa il protocollo HTTPS:

   1. Aprire Fiddler

   2. Passare a **strumenti > Opzioni Fiddler** e selezionare come mostrato nella schermata seguente. 

      ![Opzioni Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. Se l'applicazione usa i certificati SSL, è necessario aggiungere anche il certificato di Fiddler per il dispositivo.

4. Per aggiungere il certificato di Fiddler per il dispositivo, passare **degli strumenti** > **Fiddler Options** > **HTTPS**  >   **Le azioni** > **esportazione del certificato radice per Desktop** per ottenere il certificato di Fiddler.

5. Disattivare l'acquisizione, in modo che può essere cancellata la cache del browser per avviare una nuova traccia.

6. 1. Passare a **File** > **Capture Traffic** oppure premere **F12**.
   2. Cancellare la cache del browser in modo che tutti gli elementi memorizzati nella cache vengono rimossi e devono essere scaricati di nuovo.

7. Crea richiesta: 

8. 1. Fare clic sulla scheda Composer
   2. Impostare il metodo Http e URL
   3. Aggiungere intestazioni e corpo della richiesta, se necessario
   4. Fare clic su Esegui

9. Avvio dell'acquisizione del traffico nuovamente e completare la transazione problematica nella pagina.

10. Una volta completato, passare a **File** > **salvare** > **tutte le sessioni**.

Altre informazioni su Fiddler [qui](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>Passaggi successivi

Se non si riesce a trovare una soluzione al problema, ecco alcune altre risorse che è possibile provare.

*  [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità](https://feedback.azure.com/forums/270578-data-factory)
*  [Video](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



