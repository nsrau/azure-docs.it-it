---
title: Risolvere i problemi di esecuzione del pacchetto nel runtime di integrazione SSIS | Microsoft Docs
description: Questo articolo fornisce indicazioni sulla risoluzione dei problemi per l'esecuzione del pacchetto SSIS nel runtime di integrazione SSIS
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
ms.openlocfilehash: 05723a90725992e6b955524a2d35c82d3378ee3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621845"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Risolvere i problemi di esecuzione del pacchetto nel runtime di integrazione SSIS

Questo articolo include gli errori più comuni che potrebbero risultare durante l'esecuzione dei pacchetti di SQL Server Integration Services (SSIS) nel runtime di integrazione SSIS. Descrive le possibili cause e le azioni per risolvere gli errori.

## <a name="where-to-find-logs-for-troubleshooting"></a>Dove trovare i log per la risoluzione dei problemi

Usare il portale di Azure Data Factory per controllare l'output dell'attività di esecuzione del pacchetto di SSIS. L'output include il risultato dell'esecuzione, i messaggi di errore e ID operazione. Per informazioni dettagliate, vedere [monitorare la pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Usare il catalogo SSIS (SSISDB) per controllare i registri di dettaglio per l'esecuzione. Per informazioni dettagliate, vedere [monitorare l'esecuzione di pacchetti e altre operazioni](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Gli errori, cause e soluzioni comuni

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Messaggio di errore: "Timeout della connessione scaduto" o "il servizio ha rilevato un errore durante l'elaborazione della richiesta. Riprovare."

Ecco le possibili cause e le azioni consigliate:
* L'origine dati o la destinazione è sottoposto a overload. Controllare il carico di origine dati o di destinazione e verificare se dispone di una capacità sufficiente. Ad esempio, se si usa Database SQL di Azure, è consigliabile aumentare se il database è probabile che il timeout.
* La rete tra il runtime di integrazione SSIS e l'origine dati o la destinazione è instabile, soprattutto quando la connessione è tra aree o compreso tra da sito locale e Azure. Applicare il modello di ripetizione dei tentativi nel pacchetto SSIS, seguire questi passaggi:
  * Assicurarsi che i pacchetti SSIS possono eseguire di nuovo in caso di errore senza effetti collaterali (ad esempio, la perdita di dati o la duplicazione dei dati).
  * Configurare **ripetere** e **intervallo tra tentativi** dei **Esegui pacchetto SSIS** attività nel **generale** scheda. ![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Per un componente di origine o di destinazione ADO.NET e OLE DB, impostare **ConnectRetryCount** e **ConnectRetryInterval** nella gestione connessione nel pacchetto SSIS o attività SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Messaggio di errore: "Origine ADO NET non riesce ad acquisire la connessione '.'" con "un errore specifico dell'istanza o relativo alla rete si è verificato durante il tentativo di stabilire una connessione a SQL Server. Il server non è stato trovato o non è accessibile."

Questo problema in genere significa che l'origine dati o di destinazione non è accessibile dal runtime di integrazione SSIS. I motivi possono variare. Provare a eseguire queste azioni:
* Assicurarsi che si sta comunicando all'origine dati o alla destinazione IP/nome correttamente.
* Assicurarsi che il firewall sia impostato correttamente.
* Verificare che la rete virtuale è configurata correttamente se l'origine dati o la destinazione è in locale:
  * È possibile verificare se il problema è dalla configurazione della rete virtuale eseguendo il provisioning di una macchina virtuale di Azure nella stessa rete virtuale. Controllare quindi se l'origine dati o la destinazione sono accessibili dalla macchina virtuale di Azure.
  * È possibile trovare altre informazioni sull'uso di una rete virtuale con un runtime di integrazione SSIS nella [aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Messaggio di errore: "Origine ADO NET non riesce ad acquisire la connessione '.'" con "Impossibile creare una connessione gestita manager."

La causa potenziale è che il provider ADO.NET utilizzato nel pacchetto non è installato nel runtime di integrazione SSIS. È possibile installare il provider usando un'installazione personalizzata. È possibile trovare altre informazioni sull'installazione personalizzata in [personalizzare il programma di installazione per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Messaggio di errore: "La connessione '.' non viene trovato"

Questo errore è causato da un problema noto nelle versioni precedenti di SQL Server Management Studio (SSMS). Se il pacchetto contiene un componente personalizzato (ad esempio, i componenti di Feature Pack di SSIS Azure o partner) che non è installato nel computer in cui SQL Server Management Studio viene usato per eseguire la distribuzione, SQL Server Management Studio verrà rimosso il componente e causare l'errore. Eseguire l'aggiornamento [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) alla versione più recente con il risoluzione del problema.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Messaggio di errore: "codice di uscita di SSIS Executor: -1073741819."

* Causa potenziale & azione consigliata:
  * Questo errore potrebbe essere a causa della limitazione per l'origine Excel e destinazione quando più origini o destinazioni sono in esecuzione in parallelo in più thread. È possibile aggirare questa limitazione per modificare i componenti di Excel eseguite in sequenza o separarli in diversi pacchetti e i trigger tramite "Attività Esegui pacchetto" con la proprietà ExecuteOutOfProcess impostata come True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Messaggio di errore: "Non esiste spazio sufficiente sul disco"

Questo errore indica che si esaurisca nel disco locale nel nodo SSIS integration runtime. Controllare se il pacchetto o l'installazione personalizzata è utilizzare una grande quantità di spazio su disco:
* Se è stato elaborato dal pacchetto, il disco verrà liberata backup al termine dell'esecuzione del pacchetto.
* Se il disco viene utilizzato per l'installazione personalizzata, si sarà necessario arrestare il runtime di integrazione SSIS, modificare lo script e avviare nuovamente il runtime di integrazione. Il contenitore blob di Azure completamente specificata per il programma di installazione personalizzato verrà copiato nel nodo di runtime di integrazione SSIS, quindi, controllare se è presente qualsiasi contenuto non necessaria in tale contenitore.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Messaggio di errore: "Impossibile recuperare la risorsa dal master. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Descrizione: Carica file "*" non è riuscita. "

* Causa potenziale & azione consigliata:
  * Se l'attività SSIS è in esecuzione del pacchetto dal file system (file del pacchetto o file di progetto), si verificherà questo errore se il file di configurazione, di progetto o di pacchetto non è accessibile con le credenziali di accesso pacchetto fornito nell'attività di SSIS
    * Se si usa File di Azure:
      * Il percorso del file deve iniziare con \\ \\ \<nome account di archiviazione\>. file.core.windows.net\\\<percorso della condivisione file\>
      * Il dominio deve essere "Azure"
      * Il nome utente deve essere \<nome account di archiviazione\>
      * La password deve essere \<chiave di accesso di archiviazione\>
    * Se si sta utilizzando file in locale, verificare se sono configurati correttamente rete virtuale, le credenziali di accesso pacchetto e autorizzazioni in modo che il runtime di integrazione SSIS di Azure possa accedere alla condivisione file in locale

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Messaggio di errore: "Il nome del file '.' specificato nella connessione non valido "

* Causa potenziale & azione consigliata:
  * Viene specificato un nome di file non valido
  * Assicurarsi che si usa FQDN (Fully Qualified Domain Name) invece di breve periodo di tempo nella gestione connessione

### <a name="error-message-cannot-open-file-"></a>Messaggio di errore: "Impossibile aprire il file '.'"

Questo errore si verifica quando l'esecuzione del pacchetto non è possibile trovare un file nel disco locale nel runtime di integrazione SSIS. Provare a eseguire queste azioni:
* Non usare il percorso assoluto del pacchetto in esecuzione nel runtime di integrazione SSIS. Usare la directory di lavoro di esecuzione corrente (.) o la cartella temporanea (% TEMP %) In alternativa.
* Se si desidera mantenere alcuni file in nodi del runtime di integrazione SSIS, preparare i file come descritto in [personalizzare il programma di installazione](how-to-configure-azure-ssis-ir-custom-setup.md). Tutti i file nella directory di lavoro vengono eliminati al termine dell'esecuzione.
* Usare file di Azure invece di archiviare il file nel nodo SSIS integration runtime. Per informazioni dettagliate, vedere [condivisioni file di Azure Usa](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Messaggio di errore: "Il database 'SSISDB' ha raggiunto la quota delle dimensioni"

Una causa possibile è che il database SSISDB creato nel database SQL di Azure o un'istanza gestita quando si crea un runtime di integrazione SSIS, ha raggiunto la quota. Provare a eseguire queste azioni:
* Provare ad aumentare il numero di DTU del database. È possibile trovare i dettagli nella [limiti delle risorse del Database SQL per un server di Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Controllare se il pacchetto potrebbe generare molti log. In questo caso, è possibile configurare un processo elastico per pulire questi log. Per informazioni dettagliate, vedere [pulire i log SSISDB con processi di Database elastici Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Messaggio di errore: "Il limite di richieste per il database è... ed è stato raggiunto."

Se molti pacchetti vengono eseguiti in parallelo nel runtime di integrazione SSIS, questo errore può verificarsi perché SSISDB ha raggiunto il limite di richieste. Provare ad aumentare il DTC del database SSISDB per risolvere questo problema. È possibile trovare i dettagli nella [limiti delle risorse del Database SQL per un server di Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Messaggio di errore: "Operazione SSIS non è riuscita con stato operazione imprevisto:..."

L'errore è principalmente causato da un problema temporaneo, quindi provare a eseguire di nuovo l'esecuzione del pacchetto. Applicare il modello di ripetizione dei tentativi nel pacchetto SSIS, seguire questi passaggi:

* Assicurarsi che i pacchetti SSIS possono eseguire di nuovo in caso di errore senza effetti collaterali (ad esempio, la perdita di dati o la duplicazione dei dati).
* Configurare **ripetere** e **intervallo tra tentativi** dei **Esegui pacchetto SSIS** attività nel **generale** scheda. ![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Per un componente di origine o di destinazione ADO.NET e OLE DB, impostare **ConnectRetryCount** e **ConnectRetryInterval** nella gestione connessione nel pacchetto SSIS o attività SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Messaggio di errore: "Non c'è nessun lavoro attivo."

Questo errore indica in genere che il runtime di integrazione SSIS ha lo stato non integro. Controllare il portale di Azure per lo stato e gli errori dettagliati. Per altre informazioni, vedere [runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Messaggio di errore: "Il runtime di integrazione non può essere aggiornato e infine smetterà di funzionare, perché non è possibile accedere il contenitore Blob di Azure che è fornito per l'installazione personalizzata."

Questo errore si verifica quando il runtime di integrazione SSIS non è possibile accedere all'archiviazione configurato per l'installazione personalizzata. Controllare se la firma di accesso condiviso (SAS) quello specificato è valida e non sia scaduto.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Messaggio di errore: "Provider Microsoft OLE DB per Analysis Services. ' Hresult: 0x80004005 descrizione:' Errore COM: Errore COM: mscorlib; Eccezione generata dalla destinazione di una chiamata"

Una delle possibili cause è che il nome utente o la password con Azure multi-Factor Authentication abilitata sia configurata per l'autenticazione di Azure Analysis Services. Questa autenticazione non è supportata nel runtime di integrazione SSIS. Provare a usare un'entità servizio per l'autenticazione di Azure Analysis Services:
1. Preparare un'entità servizio, come descritto in [automazione con le entità servizio](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. In Gestione connessione, configurare **usare un nome utente specifico e una password**: impostare **AppID** come nome utente e **clientSecret** come password.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Messaggio di errore: "ADONET origine non è riuscita ad acquisire la connessione {GUID} con il messaggio di errore seguente: Accesso non riuscito per l'utente 'NT AUTHORITY\ANONYMOUS LOGON' "quando si usa un'identità gestita

Assicurarsi che non si configura il metodo di autenticazione della gestione connessione come **autenticazione della Password Active Directory** quando il parametro *ConnectUsingManagedIdentity* è **True** . È possibile configurarlo come **autenticazione di SQL Server** invece che viene ignorato se *ConnectUsingManagedIdentity* è impostata.

### <a name="package-execution-takes-too-long"></a>L'esecuzione del pacchetto è troppo lunga

Ecco le possibili cause e le azioni consigliate:
* Un numero eccessivo di esecuzioni di pacchetti sono state pianificate nel runtime di integrazione SSIS. Tutte queste esecuzioni dovrà attendere in una coda loro turno.
  * Determinare il massimo con questa formula: 
    
    Conteggio delle esecuzioni parallele massime per ogni runtime di integrazione = numero di nodi * esecuzione in parallelo Max per ogni nodo
  * Per informazioni su come impostare il numero di nodi e massima di esecuzione parallela per ogni nodo, vedere [creare un runtime di integrazione Azure-SSIS in Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Il runtime di integrazione SSIS è stato arrestato o è presente uno stato non integro. Per informazioni su come il controllo dello stato di runtime di integrazione SSIS e gli errori, vedere [runtime di integrazione Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

È inoltre consigliabile impostare un timeout per il **generale** scheda: ![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Una riduzione delle prestazioni nell'esecuzione del pacchetto

Provare a eseguire queste azioni:

* Assicurarsi che il runtime di integrazione SSIS sia nella stessa area come origine dati e di destinazione.

* Impostare il livello di registrazione dell'esecuzione del pacchetto per **prestazioni** per raccogliere informazioni sulla durata per ogni componente in esecuzione. Per informazioni dettagliate, vedere [la registrazione di Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Controllare le prestazioni del nodo di runtime di integrazione nel portale di Azure:
  * Per informazioni su come monitorare il runtime di integrazione SSIS, vedere [runtime di integrazione Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * È possibile trovare la cronologia di utilizzo di CPU/memoria per il runtime di integrazione SSIS, visualizzare le metriche della data factory nel portale di Azure.
    ![Monitorare le metriche del runtime di integrazione SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
