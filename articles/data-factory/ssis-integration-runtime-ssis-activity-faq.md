---
title: Risolvere i problemi di esecuzione del pacchetto in SSIS Integration Runtime | Microsoft Docs
description: Questo articolo fornisce indicazioni per la risoluzione dei problemi relativi all'esecuzione di pacchetti SSIS in SSIS Integration Runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: a7ad0f3be754029c654b04d19750aab7bbcd210d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933645"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Risolvere i problemi di esecuzione del pacchetto in SSIS Integration Runtime

Questo articolo include gli errori più comuni che è possibile riscontrare durante l'esecuzione di pacchetti di SQL Server Integration Services (SSIS) in SSIS Integration Runtime. Vengono descritte le possibili cause e le azioni per la risoluzione degli errori.

## <a name="where-to-find-logs-for-troubleshooting"></a>Dove trovare i log per la risoluzione dei problemi

Usare il portale di Azure Data Factory per controllare l'output dell'attività di esecuzione del pacchetto SSIS. L'output include il risultato dell'esecuzione, i messaggi di errore e l'ID operazione. Per informazioni dettagliate, vedere [monitorare la pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Usare il catalogo SSIS (SSISDB) per controllare i log di dettaglio per l'esecuzione. Per informazioni dettagliate, vedere [monitorare i pacchetti in esecuzione e altre operazioni](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Errori, cause e soluzioni comuni

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Messaggio di errore: "Timeout connessione scaduto" o "il servizio ha rilevato un errore durante l'elaborazione della richiesta. Riprovare ".

Ecco le possibili cause e le azioni consigliate:
* L'origine dati o la destinazione è sottoposto a overload. Controllare il carico sull'origine dati o sulla destinazione e verificare se la capacità è sufficiente. Ad esempio, se è stato usato il database SQL di Azure, valutare la possibilità di aumentare le prestazioni in caso di timeout del database.
* La rete tra il runtime di integrazione SSIS e l'origine dati o la destinazione è instabile, soprattutto quando la connessione è tra più aree o tra l'ambiente locale e Azure. Applicare il modello di ripetizione dei tentativi nel pacchetto SSIS attenendosi alla procedura seguente:
  * Assicurarsi che i pacchetti SSIS possano essere rieseguiti in caso di errore senza effetti collaterali, ad esempio perdita di dati o duplicazione dei dati.
  * Configurare l'intervallo tra tentativi e **tentativi** per l'attività **Esegui pacchetto SSIS** nella scheda **generale** . ![Impostare le proprietà nella scheda generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Per un ADO.NET e OLE DB un componente di origine o destinazione, impostare **ConnectRetryCount** e **ConnectRetryInterval** nella gestione connessione nel pacchetto SSIS o nell'attività SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Messaggio di errore: "L'origine ADO NET non è stata in grado di acquisire la connessione '.. .'" con "si è verificato un errore specifico dell'istanza o relativo alla rete durante il tentativo di stabilire una connessione a SQL Server. Il server non è stato trovato o non è accessibile. "

Generalmente questo errore significa che la destinazione o l'origine dati non è accessibile da SSIS Integration Runtime. I motivi possono essere di vario tipo. Provare a eseguire queste operazioni:
* Assicurarsi di passare l'origine dati o il nome/IP di destinazione correttamente.
* Verificare che il firewall sia impostato correttamente.
* Verificare che la rete virtuale sia configurata correttamente se l'origine dati o la destinazione è locale:
  * È possibile verificare se il problema riguarda la configurazione della rete virtuale eseguendo il provisioning di una macchina virtuale di Azure nella stessa rete virtuale. Controllare quindi se è possibile accedere all'origine dati o alla destinazione dalla macchina virtuale di Azure.
  * Per altre informazioni sull'uso di una rete virtuale con un runtime di integrazione SSIS, vedere [aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Messaggio di errore: "L'origine ADO NET non è stata in grado di acquisire la connessione '.. .'" con "Impossibile creare una gestione connessione gestita".

La potenziale cause è che il provider ADO.NET usato nel pacchetto non è installato in SSIS Integration Runtime. È possibile installare il provider utilizzando una configurazione personalizzata. Per altre informazioni sull'installazione personalizzata, vedere [personalizzare il programma di installazione per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Messaggio di errore: "Connection '.. .' non trovato "

L'errore può essere causato da un problema noto nelle versioni precedenti di SQL Server Management Studio (SSMS). Se il pacchetto contiene un componente personalizzato (ad esempio, SSIS Azure Feature Pack o componenti partner) non installato nel computer in cui è usato SSMS per la distribuzione, SSMS rimuoverà il componente causando l'errore. Aggiornare [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) alla versione più recente in cui è stato risolto il problema.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Messaggio di errore: "codice di uscita di SSIS Executor:-1073741819".

* Causa potenziale & azione consigliata:
  * Questo errore può essere causato dalla limitazione dell'origine e della destinazione di Excel quando più origini o destinazioni Excel sono in esecuzione in parallelo in più thread. Per ovviare a questa limitazione, modificare i componenti di Excel in modo che vengano eseguiti in sequenza oppure separarli in pacchetti diversi e attivarli tramite l'attività Esegui pacchetto con la proprietà ExecuteOutOfProcess impostata su true.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Messaggio di errore: "Lo spazio su disco non è sufficiente"

Questo errore indica che il disco locale viene utilizzato nel nodo del runtime di integrazione SSIS. Controllare se il pacchetto o il programma di installazione personalizzato sta consumando molto spazio su disco:
* Se il disco viene utilizzato dal pacchetto, verrà liberato al termine dell'esecuzione del pacchetto.
* Se il disco viene usato dalla configurazione personalizzata, è necessario arrestare il runtime di integrazione SSIS, modificare lo script e avviare di nuovo il runtime di integrazione. L'intero contenitore BLOB di Azure specificato per l'installazione personalizzata verrà copiato nel nodo di runtime di integrazione SSIS, quindi controllare se nel contenitore è presente contenuto superfluo.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Messaggio di errore: "Non è stato possibile recuperare la risorsa dal master. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Codice: 300004. Descrizione: caricamento del file "* * *" non riuscito. "

* Causa potenziale & azione consigliata:
  * Se l'attività SSIS sta eseguendo il pacchetto da file system (file di pacchetto o di progetto), questo errore si verificherà se il progetto, il pacchetto o il file di configurazione non è accessibile con le credenziali di accesso del pacchetto fornite nell'attività SSIS
    * Se si usa file di Azure:
      * Il percorso del file deve iniziare \\con il nome\>dell'account\\di \\ \<archiviazione. percorso condivisione file file.Core.Windows.NET\<\>
      * Il dominio deve essere "Azure"
      * Il nome utente deve \<essere il nome dell'account di archiviazione\>
      * La password deve essere \<una chiave di accesso alla risorsa di archiviazione\>
    * Se si usa un file locale, verificare se VNet, le credenziali di accesso al pacchetto e le autorizzazioni sono configurate correttamente in modo che il runtime di integrazione SSIS di Azure possa accedere alla condivisione file locale

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Messaggio di errore: "Nome file"... " specificato nella connessione non valido "

* Causa potenziale & azione consigliata:
  * È stato specificato un nome file non valido
  * Assicurarsi di usare il nome di dominio completo (FQDN) invece di un breve intervallo di tempo nella gestione connessione

### <a name="error-message-cannot-open-file-"></a>Messaggio di errore: "Impossibile aprire il file '.. .'"

Questo errore si verifica quando l'esecuzione del pacchetto non riesce a trovare un file nel disco locale nel runtime di integrazione SSIS. Provare a eseguire queste operazioni:
* Non usare il percorso assoluto nel pacchetto che viene eseguito nel runtime di integrazione SSIS. Utilizzare la directory di lavoro di esecuzione corrente (.) o la cartella temporanea (% TEMP%) invece.
* Se è necessario salvare in modo permanente alcuni file nei nodi di runtime di integrazione SSIS, preparare i file come descritto in [personalizzare il programma di installazione](how-to-configure-azure-ssis-ir-custom-setup.md). Al termine dell'esecuzione, tutti i file nella directory di lavoro verranno puliti.
* Usare File di Azure anziché archiviare il file nel nodo di runtime di integrazione SSIS. Per informazioni dettagliate, vedere [usare le condivisioni file di Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Messaggio di errore: "Il database ' SSISDB ' ha raggiunto la quota di dimensioni"

Una delle possibili cause è che il database SSISDB creato nel database SQL di Azure SQL, o un'istanza gestita quando si crea un SSIS Integration Runtime, ha raggiunto la sua quota. Provare a eseguire queste operazioni:
* Prendere in considerazione di aumentare la DTU del database. I dettagli sono disponibili in [Limiti delle risorse del database SQL per un server di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Controllare se il pacchetto genera molti log. In caso affermativo, è possibile configurare un processo elastico per pulire i log. Per conoscere i dettagli, vedere [Pulire i log SSISDB tramite processi di database elastico di Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Messaggio di errore: "Il limite della richiesta per il database è... è stato raggiunto il termine ".

Se molti pacchetti sono in esecuzione in parallelo nel runtime di integrazione SSIS, questo errore potrebbe verificarsi perché SSISDB ha raggiunto il limite di richieste. Prendere in considerazione l'aumento del DTC di SSISDB per risolvere questo problema. I dettagli sono disponibili in [Limiti delle risorse del database SQL per un server di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Messaggio di errore: "Operazione SSIS non riuscita con stato operazione non prevista:..."

L'errore è causato principalmente da un problema temporaneo, quindi provare a eseguire di nuovo l'esecuzione del pacchetto. Applicare il modello di ripetizione dei tentativi nel pacchetto SSIS attenendosi alla procedura seguente:

* Assicurarsi che i pacchetti SSIS possano essere rieseguiti in caso di errore senza effetti collaterali, ad esempio perdita di dati o duplicazione dei dati.
* Configurare l'intervallo tra tentativi e **tentativi** per l'attività **Esegui pacchetto SSIS** nella scheda **generale** . ![Impostare le proprietà nella scheda generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Per un ADO.NET e OLE DB un componente di origine o destinazione, impostare **ConnectRetryCount** e **ConnectRetryInterval** nella gestione connessione nel pacchetto SSIS o nell'attività SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Messaggio di errore: "Non è presente alcun lavoro attivo".

Questo errore indica in genere che lo stato del runtime di integrazione SSIS non è integro. Controllare la portale di Azure per lo stato e gli errori dettagliati. Per altre informazioni, vedere [runtime di integrazione SSIS di Azure](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Messaggio di errore: "Il runtime di integrazione non può essere aggiornato e smetterà di funzionare, perché non è possibile accedere al contenitore BLOB di Azure fornito per l'installazione personalizzata".

Questo errore si verifica quando SSIS Integration Runtime non è in grado di accedere alla risorsa di archiviazione configurata per l'installazione personalizzata. Controllare se l'URI di firma di accesso condiviso specificato è valido e non è scaduto.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Messaggio di errore: "Provider Microsoft OLE DB per Analysis Services. HRESULT Descrizione di 0x80004005:' Errore COM: Errore COM: mscorlib; Eccezione generata dalla destinazione di una chiamata "

Una causa potenziale è che il nome utente o la password con Azure Multifactor Authentication abilitato è configurato per l'autenticazione Azure Analysis Services. Questa autenticazione non è supportata in SSIS Integration Runtime. Provare a usare un'entità servizio per l'autenticazione Azure Analysis Services:
1. Preparare un'entità servizio come descritto in [automazione con entità servizio](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. In gestione connessione configurare **Usa nome utente e password specifici**: impostare **AppID** come nome utente e **clientSecret** come password.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Messaggio di errore: "L'origine ADONET non è riuscita ad acquisire la connessione {GUID} con il seguente messaggio di errore: Accesso non riuscito per l'utente ' NT AUTHORITY\ANONYMOUS LOGON ' "quando si usa un'identità gestita

Assicurarsi di non configurare il metodo di autenticazione di gestione connessione come **Active Directory autenticazione della password** quando il parametro *ConnectUsingManagedIdentity* è **true**. In alternativa, è possibile configurarlo come **autenticazione SQL** , che viene ignorato se è impostato *ConnectUsingManagedIdentity* .

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Sono state attivate più esecuzioni di pacchetti in modo imprevisto

* Causa potenziale & azione consigliata:
  * L'attività stored procedure di ADF viene utilizzata per attivare l'esecuzione del pacchetto SSIS. Il comando t-SQL può raggiungere un problema temporaneo e attivare la riesecuzione che provocherebbe più esecuzioni di pacchetti.
  * Usare invece l'attività ExecuteSSISPackage per garantire che l'esecuzione del pacchetto non venga rieseguita a meno che l'utente non riscontri il numero di tentativi I dettagli sono disponibili all'indirizzo[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

### <a name="package-execution-takes-too-long"></a>L'esecuzione del pacchetto richiede troppo tempo

Ecco le possibili cause e le azioni consigliate:

* Troppe esecuzioni di pacchetti pianificate nel runtime di integrazione SSIS. Tutte queste esecuzioni saranno in attesa in una coda per il loro turno.
  * Determinare il valore massimo utilizzando la formula seguente:

    Numero massimo di esecuzioni parallele per IR = numero di nodi * numero massimo di esecuzioni parallele per nodo
  * Per informazioni su come impostare il numero di nodi e l'esecuzione parallela massima per nodo, vedere [creare un runtime di integrazione SSIS di Azure in Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Il runtime di integrazione SSIS è stato arrestato o presenta uno stato non integro. Per informazioni su come controllare lo stato e gli errori di runtime di integrazione SSIS, vedere [runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Si consiglia inoltre di impostare un timeout sulla scheda **generale** : ![Impostare le proprietà nella scheda](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)generale.

### <a name="poor-performance-in-package-execution"></a>Riduzione delle prestazioni nell'esecuzione del pacchetto

Provare a eseguire queste operazioni:

* Verificare che SSIS Integration runtime si trovi nella stessa area dell'origine dati e della destinazione.

* Impostare il livello di registrazione dell'esecuzione del pacchetto su **prestazioni** per raccogliere informazioni sulla durata per ogni componente nell'esecuzione. Per informazioni dettagliate, vedere la pagina relativa alla [registrazione di Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Controllare le prestazioni del nodo IR nel portale di Azure:
  * Per informazioni su come monitorare il runtime di integrazione SSIS, vedere [runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * È possibile trovare la cronologia della CPU/memoria per il runtime di integrazione SSIS visualizzando le metriche del data factory nel portale di Azure.
    ![Monitorare le metriche del runtime di integrazione SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
