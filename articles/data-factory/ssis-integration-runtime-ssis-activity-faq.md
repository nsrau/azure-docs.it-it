---
title: Risolvere i problemi di esecuzione del pacchetto nel runtime di integrazione SSISTroubleshoot package execution in the SSIS integration runtime
description: Questo articolo fornisce indicazioni per la risoluzione dei problemi per l'esecuzione del pacchetto SSIS nel runtime di integrazione SSISThis article provides troubleshooting guidance for SSIS package execution in the SSIS integration runtime
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 1c2db107302e4851641ef430db61ec9b29ee151f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187470"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Risolvere i problemi di esecuzione del pacchetto nel runtime di integrazione SSISTroubleshoot package execution in the SSIS integration runtime

Questo articolo include gli errori più comuni che possono essere rilevati durante l'esecuzione di pacchetti di SQL Server Integration Services (SSIS) nel runtime di integrazione SSIS. Descrive le potenziali cause e azioni per risolvere gli errori.

## <a name="where-to-find-logs-for-troubleshooting"></a>Dove trovare i registri per la risoluzione dei problemi

Usare il portale di Azure Data Factory per controllare l'output dell'attività di esecuzione del pacchetto SSIS. L'output include il risultato dell'esecuzione, i messaggi di errore e l'ID operazione. Per informazioni dettagliate, vedere [Monitorare la pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Utilizzare il catalogo SSIS (SSISDB) per controllare i registri di dettaglio per l'esecuzione. Per informazioni dettagliate, consultate [Monitorare i pacchetti in esecuzione e altre operazioni.](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solutions"></a>Errori comuni, cause e soluzioni

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Messaggio di errore: "Timeout di connessione scaduto" o "Il servizio ha rilevato un errore durante l'elaborazione della richiesta. Riprovare".

Di seguito sono riportate le cause potenziali e le azioni consigliate:Here are potential causes and recommended actions:
* L'origine dati o la destinazione è in overload. Controllare il carico sull'origine dati o sulla destinazione e verificare se dispone di capacità sufficiente. Ad esempio, se è stato usato il database SQL di Azure, valutare la scalabilità verticale se è probabile che si scada il database.
* The network between the SSIS integration runtime and the data source or destination is unstable, especially when the connection is cross-region or between on-premises and Azure. Applicare il modello di ripetizione dei tentativi nel pacchetto SSIS attenendosi alla procedura seguente:Apply the retry pattern in the SSIS package by following these steps:
  * Assicurati che i pacchetti SSIS possano essere rieseguiti in caso di errore senza effetti collaterali (ad esempio, perdita di dati o duplicazione dei dati).
  * Configurare **l'intervallo** tra **tentativi** e tentativi dell'attività **Esegui pacchetto SSIS** nella scheda **Generale.** ![](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Per un componente di origine o di destinazione OLE DB e ADO.NET e, impostare **ConnectRetryCount** e **ConnectRetryInterval** in Connection Manager nel pacchetto SSIS o nell'attività SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Messaggio di errore: "ADO NET Source non è riuscito ad acquisire la connessione '...'" con "Si è verificato un errore relativo alla rete o all'istanza durante la creazione di una connessione a SQL Server. Il server non è stato trovato o non era accessibile."

Generalmente questo errore significa che la destinazione o l'origine dati non è accessibile da SSIS Integration Runtime. I motivi possono essere di vario tipo. Provare a eseguire queste operazioni:
* Assicurarsi di passare correttamente il nome/IP dell'origine dati o di destinazione.
* Assicurarsi che il firewall sia impostato correttamente.
* Assicurarsi che la rete virtuale sia configurata correttamente se l'origine dati o la destinazione è locale:Make sure your virtual network is configured properly if your data source or destination is on-premises:
  * È possibile verificare se il problema proviene dalla configurazione della rete virtuale eseguendo il provisioning di una macchina virtuale di Azure nella stessa rete virtuale. Verificare quindi se è possibile accedere all'origine dati o alla destinazione dalla macchina virtuale di Azure.Then check whether the data source or destination can be accessed from the Azure VM.
  * Per altre informazioni sull'uso di una rete virtuale con un runtime di integrazione SSIS, vedere [Join an Azure-SSIS integration runtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Messaggio di errore: "ADO NET Source non è riuscito ad acquisire la connessione '...'" con "Impossibile creare una gestione connessione gestita".

La causa potenziale è che il provider di ADO.NET utilizzato nel pacchetto non è installato nel runtime di integrazione SSIS. È possibile installare il provider utilizzando un'installazione personalizzata. Per ulteriori informazioni sull'installazione personalizzata, vedere [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Messaggio di errore: "La connessione '...' non viene trovato"

L'errore può essere causato da un problema noto nelle versioni precedenti di SQL Server Management Studio (SSMS). Se il pacchetto contiene un componente personalizzato (ad esempio, SSIS Azure Feature Pack o componenti partner) non installato nel computer in cui è usato SSMS per la distribuzione, SSMS rimuoverà il componente causando l'errore. Aggiornare [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) alla versione più recente con il problema risolto.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Messaggio di errore: "Codice di uscita esecutore SSIS: -1073741819."

* Possibile causa e azione consigliata:
  * Questo errore può essere dovuto alla limitazione per l'origine e la destinazione di Excel quando più origini o destinazioni di Excel sono in esecuzione in parallelo in più thread. È possibile risolvere questa limitazione modificando i componenti di Excel in modo che vengano eseguiti in sequenza o separarli in pacchetti diversi e attivarli tramite "Esegui attività pacchetto" con la proprietà ExecuteOutOfProcess impostata su True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Messaggio di errore: "Spazio insufficiente sul disco"

Questo errore indica che il disco locale viene utilizzato nel nodo di runtime di integrazione SSIS. Verificare se il pacchetto o l'installazione personalizzata sta consumando molto spazio su disco:Check whether your package or custom setup is consuming a lot of disk space:
* Se il disco viene utilizzato dal pacchetto, verrà liberato al termine dell'esecuzione del pacchetto.
* Se il disco viene utilizzato dall'installazione personalizzata, è necessario arrestare il runtime di integrazione SSIS, modificare lo script e riavviare il runtime di integrazione. L'intero contenitore BLOB di Azure specificato per l'installazione personalizzata verrà copiato nel nodo di runtime di integrazione SSIS, pertanto verificare se in tale contenitore è presente contenuto non necessario.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Messaggio di errore: "Impossibile recuperare la risorsa dal master. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Codice:300004. Descrizione/ Descrizione:Caricamento del file "" non è riuscito."

* Possibile causa e azione consigliata:
  * Se l'attività SSIS esegue il pacchetto dal file system (file di pacchetto o file di progetto), questo errore si verifica se il progetto, il pacchetto o il file di configurazione non è accessibile con le credenziali di accesso al pacchetto fornite nell'attività SSIS
    * Se si usa File di Azure:If you are using Azure File:
      * Il percorso del \\ \\ \<file deve\>iniziare\\\<con il nome dell'account di archiviazione .file.core.windows.net percorso di condivisione fileThe file path should start with storage account name .file.core.windows.net file share path\>
      * Il dominio deve essere "Azure"The domain should be "Azure"
      * Il nome \<utente deve essere il nome dell'account di archiviazioneThe username should be storage account name\>
      * La password \<deve essere la chiave di accesso di archiviazione\>
    * Se si usa un file locale, verificare se la rete virtuale, le credenziali di accesso al pacchetto e l'autorizzazione sono configurate correttamente in modo che il runtime di integrazione Azure-SSIS possa accedere alla condivisione file locale

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Messaggio di errore: "Il nome del file '...' specificato nella connessione non valido"

* Possibile causa e azione consigliata:
  * È stato specificato un nome di file non valido
  * Assicurarsi di utilizzare FQDN (Fully Qualified Domain Name) anziché breve tempo nella gestione connessione

### <a name="error-message-cannot-open-file-"></a>Messaggio di errore: "Impossibile aprire il file '...'"

Questo errore si verifica quando l'esecuzione del pacchetto non riesce a trovare un file nel disco locale nel runtime di integrazione SSIS. Provare a eseguire queste operazioni:
* Non usare il percorso assoluto nel pacchetto che viene eseguito nel runtime di integrazione SSIS. Utilizzare la directory di lavoro di esecuzione corrente (.) o la cartella temporanea (%TEMP%) Invece.
* Se è necessario rendere persistenti alcuni file nei nodi di runtime di integrazione SSIS, preparare i file come descritto in [Customize setup](how-to-configure-azure-ssis-ir-custom-setup.md). Tutti i file nella directory di lavoro verranno puliti al termine dell'esecuzione.
* Usare File di Azure anziché archiviare il file nel nodo del runtime di integrazione SSISSSIS . Per informazioni dettagliate, vedere [Usare condivisioni file](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)di Azure.

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Messaggio di errore: "Il database 'SSISDB' ha raggiunto la quota di dimensione"

Una delle possibili cause è che il database SSISDB creato nel database SQL di Azure SQL, o un'istanza gestita quando si crea un SSIS Integration Runtime, ha raggiunto la sua quota. Provare a eseguire queste operazioni:
* Prendere in considerazione di aumentare la DTU del database. I dettagli sono disponibili in [Limiti delle risorse del database SQL per un server di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Controllare se il pacchetto genera molti log. In caso affermativo, è possibile configurare un processo elastico per pulire i log. Per conoscere i dettagli, vedere [Pulire i log SSISDB tramite processi di database elastico di Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Messaggio di errore: "Il limite di richieste per il database è ... ed è stato raggiunto.

Se molti pacchetti vengono eseguiti in parallelo nel runtime di integrazione SSIS, questo errore potrebbe verificarsi perché SSISDB ha raggiunto il limite di richieste. Considerare la possibilità di aumentare il DTC di SSISDB per risolvere questo problema. I dettagli sono disponibili in [Limiti delle risorse del database SQL per un server di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Messaggio di errore: "Operazione SSIS non riuscita con stato dell'operazione imprevisto: ..."

L'errore è causato principalmente da un problema temporaneo, quindi provare a eseguire nuovamente l'esecuzione del pacchetto. Applicare il modello di ripetizione dei tentativi nel pacchetto SSIS attenendosi alla procedura seguente:Apply the retry pattern in the SSIS package by following these steps:

* Assicurati che i pacchetti SSIS possano essere rieseguiti in caso di errore senza effetti collaterali (ad esempio, perdita di dati o duplicazione dei dati).
* Configurare **l'intervallo** tra **tentativi** e tentativi dell'attività **Esegui pacchetto SSIS** nella scheda **Generale.** ![](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Per un componente di origine o di destinazione OLE DB e ADO.NET e, impostare **ConnectRetryCount** e **ConnectRetryInterval** in Connection Manager nel pacchetto SSIS o nell'attività SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Messaggio di errore: "Non c'è nessun lavoratore attivo".

Questo errore indica in genere che il runtime di integrazione SSIS ha uno stato non integro. Controllare il portale di Azure per lo stato e gli errori dettagliati. Per altre informazioni, vedere Runtime di [integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Messaggio di errore: "Il runtime di integrazione non può essere aggiornato e alla fine smetterà di funzionare, poiché non è possibile accedere al contenitore BLOB di Azure fornito per l'installazione personalizzata".

Questo errore si verifica quando il runtime di integrazione SSIS non è in grado di accedere all'archiviazione configurata per l'installazione personalizzata. Verificare se l'URI della firma di accesso condiviso specificato è valido e non è scaduto.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Messaggio di errore: "Provider Microsoft OLE DB per Analysis Services. 'Hresult: 0x80004005 Descrizione:' Errore COM: errore COM: mscorlib; Eccezione generata dalla destinazione di una chiamata"

Una possibile causa è che il nome utente o la password con Azure Multi-Factor Authentication abilitato sia configurato per l'autenticazione di Azure Analysis Services.One potential cause is that the username or password with Azure Multi-Factor Authentication enabled is configured for Azure Analysis Services authentication. Questa autenticazione non è supportata nel runtime di integrazione SSIS. Provare a usare un'entità servizio per l'autenticazione di Azure Analysis Services:Try to use a service principal for Azure Analysis Services authentication:

1. Preparare un'entità servizio come descritto in [Automazione con entità servizio](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. In Connection Manager configurare **Usa un nome utente e una password specifici:** impostare **AppID** come nome utente e **clientSecret** come password.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Messaggio di errore: "ADONET origine non è riuscita ad acquisire la connessione GUID con il seguente messaggio di errore: Accesso non riuscito per l'utente 'NT AUTHORITY' ANONYMOUS LOGON'" quando si utilizza un'identità gestita

Assicurarsi di non configurare il metodo di autenticazione di Connection Manager come **autenticazione della password** di Active Directory quando il parametro *ConnectUsingManagedIdentity* è **True**. È possibile configurarlo come **autenticazione SQL,** che viene ignorato se *ConnectUsingManagedIdentity* è impostato.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Messaggio di errore: "0xC020801F in ..., Origine OData [...]: Impossibile acquisire una connessione gestita dalla gestione connessione di runtime"

Una possibile causa è che Transport Layer Security (TLS) non è abilitato nel runtime di integrazione SSIS richiesto dall'origine OData. È possibile abilitare TLS nel runtime di integrazione SSIS utilizzando l'installazione di Personalizza.You can enable TLS in SSIS integration runtime by using Customize setup. Ulteriori dettagli sono disponibili in [Impossibile connettere Project Online Odata da SSIS](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) e [Personalizzare l'installazione per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Messaggio di errore: "Richiesta di attività di gestione temporanea con guid operazione ... errore poi errore: Impossibile inviare l'operazione di gestione temporanea con messaggio di errore: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Impossibile caricare il proxy dati."

Assicurarsi che il runtime di integrazione Azure-SSIS sia configurato con il runtime di integrazione self-hosted. Per ulteriori dettagli, [vedere Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Messaggio di errore: "Stato dell'attività di gestione temporanea: non riuscito. Errore dell'attività di gestione temporanea: ErrorCode: 2010, ErrorMessage: The Self-hosted Integration Runtime ... è offline"

Assicurarsi che il runtime di integrazione Self-Hosted sia installato e avviato. Ulteriori dettagli sono disponibili in Creare e configurare un runtime di [integrazione self-hosted](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Messaggio di errore: "Errore dell'attività di gestione temporanea: ErrorCode: 2906, ErrorMessage: esecuzione del pacchetto non riuscita., Output: "OperationErrorMessages": "Errore: il provider OLE DB richiesto ... non è registrato. Se il driver a 64 bit non è installato, eseguire il pacchetto in modalità a 32 bit..."

Assicurarsi che il provider corrispondente utilizzato dai connettori OLE DB nel pacchetto sia installato correttamente nel computer runtime di integrazione self-hosted. Per ulteriori dettagli, vedere [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Messaggio di errore: "Errore dell'attività di gestione temporanea: ErrorCode: 2906, ErrorMessage: Esecuzione del pacchetto non riuscita., Output: "OperationErrorMessages": "Errore: System.IO.FileLoadException: Impossibile caricare il file o l'assembly 'Microsoft.WindowsAzure.Storage, Version ...., Culture, neutro, PublicKeyToken 31bf3856ad364e35' o una delle relative dipendenze. La definizione del manifesto dell'assembly individuato non corrisponde al riferimento all'assembly." ..."

Una causa potenziale è che il runtime di integrazione self-hosted non è installato o aggiornato correttamente. Suggerire di scaricare e reinstallare il runtime di integrazione Self-hosted più recente. Ulteriori dettagli sono disponibili in Creare e configurare un runtime di [integrazione self-hosted](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Messaggio di errore: "È necessaria una connessione quando si richiedono metadati. Se si lavora offline, deselezionare Offline nel menu SSIS per abilitare la connessione"

* Possibile causa e azione consigliata:
  * Se è presente anche un messaggio di avviso "Il componente non supporta l'utilizzo della gestione connessione con il valore ConnectByProxy impostazione true" nel log di esecuzione, significa che una gestione connessione viene utilizzata in un componente che non è ancora supportato "ConnectByProxy". I componenti supportati sono disponibili in [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Il log di esecuzione è disponibile nel [report SSMS](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) o nella cartella di log specificata nell'attività di esecuzione del pacchetto SSIS.
  * vNet può essere utilizzato anche per accedere ai dati locali come alternativa. Ulteriori dettagli sono disponibili in Aggiungere un runtime di [integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Messaggio di errore: "Stato dell'attività di gestione temporanea: non riuscito. Errore dell'attività di gestione temporanea: ErrorCode: 2906, ErrorMessage: Esecuzione del pacchetto non riuscita., Output: "OperationErrorMessages": "Codice di uscita dell'esecutore SSIS: -1. \\SSISTelemetry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "durationInQueue":

Assicurarsi che nel computer runtime di runtime di integrazione Self-Hosted sia installato il runtime di Visual C. Per ulteriori dettagli, vedere [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Più esecuzioni di pacchetti vengono attivate in modo imprevistoMultiple Package executions are triggered unexpectedly

* Possibile causa e azione consigliata:
  * L'attività o l'attività di ricerca della stored procedure ADF viene utilizzata per attivare l'esecuzione del pacchetto SSIS. Il comando t-sql può colpire un problema temporaneo e attivare la riesecuzione che causerebbe più esecuzioni di pacchetti.
  * Usare invece l'attività ExecuteSSISPackage che garantisce che l'esecuzione del pacchetto non venga rieseguita a meno che l'utente non sia impostato il conteggio dei tentativi nell'attività. Il dettaglio è disponibile all'a[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Perfezionare il comando t-sql per poter eseguire nuovamente controllando se un'esecuzione è già stata attivata

### <a name="package-execution-takes-too-long"></a>L'esecuzione del pacchetto richiede troppo tempo

Di seguito sono riportate le cause potenziali e le azioni consigliate:Here are potential causes and recommended actions:

* Sono state pianificate troppe esecuzioni di pacchetti nel runtime di integrazione SSIS. Tutte queste esecuzioni saranno in attesa in coda per il loro turno.
  * Determinare il valore massimo utilizzando questa formula:

    Numero massimo di esecuzioni parallele per IR - Conteggio nodi - esecuzione parallela massima per nodoMax Parallel Execution Count per IR - Node Count - Max Parallel Execution per Node
  * Per informazioni su come impostare il numero di nodi e l'esecuzione parallela massima per nodo, vedere Creare un runtime di [integrazione Azure-SSIS in Azure Data Factory.](create-azure-ssis-integration-runtime.md)
* Il runtime di integrazione SSIS è stato arrestato o ha uno stato non integro. Per informazioni su come controllare lo stato e gli errori del runtime di integrazione SSIS, vedere Runtime di [integrazione Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

È inoltre consigliabile impostare un timeout ![nella scheda](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) **Generale:** Impostare le proprietà nella scheda Generale .

### <a name="poor-performance-in-package-execution"></a>Riduzione delle prestazioni nell'esecuzione del pacchetto

Provare a eseguire queste operazioni:

* Assicurarsi che il runtime di integrazione SSIS si trova nella stessa area dell'origine dati e della destinazione.

* Impostare il livello di registrazione di esecuzione del pacchetto su **Prestazioni** per raccogliere informazioni sulla durata per ogni componente nell'esecuzione. Per informazioni dettagliate, vedere Registrazione di [Integration Services (SSIS).](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Controllare le prestazioni del nodo AIR nel portale di Azure:Check IR node performance in the Azure portal:
  * Per informazioni su come monitorare il runtime di integrazione SSIS, vedere Runtime di [integrazione Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * È possibile trovare la cronologia della CPU/memoria per il runtime di integrazione SSIS visualizzando le metriche della data factory nel portale di Azure.You can find CPU/memory history for the SSIS integration runtime by viewing the metrics of the data factory in the Azure portal.
    ![Monitorare le metriche del runtime di integrazione SSISMonitor metrics of the SSIS integration runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
