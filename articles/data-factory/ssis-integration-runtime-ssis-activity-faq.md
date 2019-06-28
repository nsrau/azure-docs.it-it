---
title: Risoluzione dei problemi di esecuzione del pacchetto nel runtime di integrazione SSIS | Microsoft Docs
description: Questo articolo fornisce le indicazioni sulla risoluzione dei problemi per l'esecuzione del pacchetto SSIS nel Runtime di integrazione SSIS
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
ms.openlocfilehash: a018a383de855a05b14aa6e1f1c465f8868f672d
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312158"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>Risoluzione dei problemi di esecuzione del pacchetto nel runtime di integrazione SSIS

Questo articolo include gli errori più comuni che si potrebbero raggiunge quando l'esecuzione di SSIS di pacchetti nel Runtime di integrazione SSIS, le potenziali cause e le azioni per risolvere gli errori.

## <a name="where-can-i-find-logs-for-troubleshoot"></a>Dove trovare i log per risolvere il problema

* Il portale di Azure Data factory può essere usato per controllare l'output dell'attività di esecuzione del pacchetto di SSIS tra cui il risultato dell'esecuzione, i messaggi di errore e ID operazione. Informazioni dettagliate, vedere [monitorare la pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* Il catalogo SSIS (SSISDB) utilizzabile per controllare i registri di dettaglio per l'esecuzione. Dettaglio è reperibile in [monitorare l'esecuzione di pacchetti e altre operazioni](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>Gli errori, cause e soluzioni comuni

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Messaggio di errore: `"Connection Timeout Expired."` o `"The service has encountered an error processing your request. Please try again."`

* Causa potenziale & azione consigliata:
  * Origine/destinazione dei dati è in overload. Controllare il carico sui dati di origine/destinazione e verificare se dispone di una capacità sufficiente. Ad esempio, se viene utilizzato SQL di Azure, è consigliabile per valutare le dimensioni backup se il Database è probabile che il timeout.
  * La rete tra il Runtime di integrazione SSIS e i dati di origine o di destinazione è instabile, soprattutto quando la connessione è tra aree o tra un'istanza locale e azure. È consigliabile per applicare i criteri di ripetizione dei tentativi in un pacchetto SSIS eseguendo i passaggi seguenti:
    * Assicurarsi che i pacchetti SSIS può eseguire di nuovo in caso di errore senza effetti collaterali (ad esempio. perdita di dati, dati dup...)
    * Configurare il **ripetere** e **intervallo tra tentativi** dell'esecuzione di attività del pacchetto SSIS nella scheda Generale ![impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Per il componente ADO.NET e OLE DB. origine/destinazione, ConnectRetryCount e ConnectRetryInterval possono essere impostati nella gestione connessione in un pacchetto SSIS o un'attività SSIS

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Messaggio di errore: `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* Causa potenziale & azione consigliata:
  * Questo problema in genere significa che i dati di origine o di destinazione non è accessibile dal Runtime di integrazione SSIS, che può essere causato da diversi motivi:
    * Assicurarsi che si sta comunicando correttamente i dati di origine/destinazione IP/nome
    * Assicurarsi che il firewall è configurato correttamente
    * Assicurarsi che la vNet è configurata correttamente se i dati di origine o di destinazione si trovano in locale.
      * È possibile verificare se il problema è dalla configurazione della rete virtuale eseguendo il provisioning di una macchina virtuale di Azure nella stessa rete virtuale. E quindi controllare se l'origine/destinazione dei dati è accessibile dalla macchina virtuale di Azure
      * È possibile trovare altre informazioni sull'uso della rete virtuale con il Runtime di integrazione SSIS nella [aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>Messaggio di errore: "`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* Causa potenziale & azione consigliata:
  * Nel Runtime di integrazione SSIS non è installato il provider ADO.NET utilizzato nel pacchetto. È possibile installare il provider usando il programma di installazione personalizzato. Altre informazioni sull'installazione personalizzata sono reperibile [personalizzare il programma di installazione per il runtime di integrazione SSIS di Azure](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Messaggio di errore: "`The connection '...' is not found`"

* Causa potenziale & azione consigliata:
  * Questo errore è possibile che un problema noto nella versione precedente di SQL Server Management Studio. Se il pacchetto contiene un componente personalizzato (ad esempio, Azure Feature Pack di SSIS o componenti di terze parti 3rd) che non è installato nel computer in cui SQL Server Management Studio viene usato per eseguire la distribuzione, il componente verrà rimosso da SSMS e causare l'errore. Eseguire l'aggiornamento [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) alla versione più recente con il risoluzione del problema.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Messaggio di errore: "Non esiste spazio sufficiente sul disco"

* Causa potenziale & azione consigliata:
  * Questo errore indica che si esaurisca nel disco locale nel nodo di Runtime di integrazione SSIS. Controllare se il pacchetto o il programma di installazione personalizzato comporta l'utilizzo di spazio disponibile su molti.
    * Se è stato elaborato dal pacchetto, il disco verrà liberata backup al termine dell'esecuzione del pacchetto.
    * Se il disco viene utilizzato per l'installazione personalizzata, è necessario arrestare il Runtime di integrazione SSIS, modificare lo Script e avviare il Runtime di integrazione SSIS di nuovo. L'intero contenitore di Blob di Azure specificata per il programma di installazione personalizzato verrà copiato nel nodo di runtime di integrazione SSIS, quindi verificare se esiste qualsiasi contenuto non necessaria in tale contenitore.

### <a name="error-message-cannot-open-file-"></a>Messaggio di errore: "Impossibile aprire il file '.'"

* Causa potenziale & azione consigliata:
  * Questo errore si verifica quando l'esecuzione del pacchetto non è possibile trovare i file sul disco locale nel Runtime di integrazione SSIS.
    * Non è consigliabile usare un percorso assoluto nel pacchetto in esecuzione in Runtime di integrazione SSIS. Usare directory di lavoro esecuzione corrente (.) o una cartella temporanea (% TEMP %) In alternativa.
    * Se è necessaria per alcuni file in nodi del Runtime di integrazione SSIS di salvare in modo permanente, si consiglia di preparare i file tramite [personalizzare il programma di installazione](how-to-configure-azure-ssis-ir-custom-setup.md). Vengono eliminati tutti i file nella directory di lavoro di esecuzione al termine dell'esecuzione.
    * Un'altra opzione consiste nell'usare File di Azure invece di archiviare il file nel nodo di Runtime di integrazione SSIS. Altri dettagli, vedere [ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Messaggio di errore: "Il database 'SSISDB' ha raggiunto la quota delle dimensioni"

* Causa potenziale & azione consigliata:
  * Il database SSISDB creato nella SQL di Azure o istanza gestita quando si crea il Runtime di integrazione SSIS ha raggiunto la quota.
    * Provare ad aumentare il numero di DTU del Database per risolvere il problema. Informazioni dettagliate, vedere [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * Controllare se il pacchetto potrebbe generare molti log. In questo caso, processi elastici possono essere configurato per pulire questi log. Fare riferimento a [pulire i log SSISDB con processi di Database elastici Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) per informazioni dettagliate.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Messaggio di errore: "Il limite di richieste per il database è... ed è stato raggiunto."

* Causa potenziale & azione consigliata:
  * Se molti pacchetti vengono eseguiti in parallelo nel Runtime di integrazione SSIS, questo errore può verificarsi perché viene raggiunto il limite di richieste di SSISDB. Provare ad aumentare il servizio DTC del database SSISDB per risolvere il problema. Informazioni dettagliate, vedere [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Messaggio di errore: "Operazione SSIS non è riuscita con stato operazione imprevisto:..."

* Causa potenziale & azione consigliata:
  * L'errore è principalmente causato da un errore temporaneo, quindi provare a eseguire di nuovo l'esecuzione del pacchetto. È consigliabile per applicare i criteri di ripetizione dei tentativi in un pacchetto SSIS eseguendo i passaggi seguenti:
    * Assicurarsi che i pacchetti SSIS può eseguire di nuovo in caso di errore senza effetti collaterali (ad esempio, la perdita di dati, dati dup...)
    * Configurare il **ripetere** e **intervallo tra tentativi** dell'esecuzione di attività del pacchetto SSIS nella scheda Generale ![impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Per il componente ADO.NET e OLE DB. origine/destinazione, ConnectRetryCount e ConnectRetryInterval possono essere impostati nella gestione connessione in un pacchetto SSIS o un'attività SSIS

### <a name="error-message-there-is-no-active-worker"></a>Messaggio di errore: "Non c'è nessun lavoro attivo."

* Causa potenziale & azione consigliata:
  * Questo errore indica in genere che il Runtime di integrazione SSIS è in uno stato non integro. Controllare gli errori dello stato e i dettagli del portale di Azure: [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Messaggio di errore: "Il runtime di integrazione non può essere aggiornato e infine smetterà di funzionare, perché non è possibile accedere il contenitore Blob di Azure che è fornito per l'installazione personalizzata."

* Questo errore si verifica quando il Runtime di integrazione SSIS non è possibile accedere all'archiviazione configurato per l'installazione personalizzata. Controllare se l'Uri di firma di accesso condiviso immessa sia valido e non sia scaduto.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Messaggio di errore: "Provider Microsoft OLE DB per Analysis Services. ' Hresult: 0x80004005 descrizione:' Errore COM: Errore COM: mscorlib; Eccezione generata dalla destinazione di una chiamata"

* Causa potenziale & azione consigliata:
  * Una possibile causa è che nome utente/password con autenticazione a più fattori abilitata è configurata per l'autenticazione di Azure Analysis Services, che non è supportato nel runtime di integrazione SSIS con ancora. Provare a usare l'entità servizio per l'autenticazione di Azure Analysis Services:
    1. Preparazione dell'entità servizio per AAS [https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
    2. Nella console di gestione connessione, configurare "Usa un nome utente specifico e una password": impostare "AppID" come nome utente e "clientSecret" come password

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-managed-identity"></a>Messaggio di errore: "ADONET origine non è riuscita ad acquisire la connessione {GUID} con il messaggio di errore seguente: Accesso non riuscito per l'utente 'NT AUTHORITY\ANONYMOUS LOGON' "quando si usa identità gestite

* Causa potenziale & azione consigliata:
  * Assicurarsi che non si configura il metodo di autenticazione della gestione connessione come "Active Directory Password Authentication" quando il parametro "ConnectUsingManagedIdentity" è True. È possibile configurarla come "Autenticazione SQL" invece che vengono ignorati se è impostato "ConnectUsingManagedIdentity"

### <a name="package-takes-unexpected-long-time-to-execute"></a>Pacchetto accetta imprevisto lungo tempo di esecuzione

* Causa potenziale & azione consigliata:
  * Un numero eccessivo di esecuzioni di pacchetti sono state pianificate nel Runtime di integrazione SSIS. In questo caso, tutte le esecuzioni in attesa in una coda del loro turno di esecuzione.
    * Conteggio delle esecuzioni parallele massime per ogni runtime di integrazione = numero di nodi * esecuzione in parallelo Max per ogni nodo
    * Fare riferimento a [crea Runtime di integrazione Azure-SSIS in Azure Data Factory](create-azure-ssis-integration-runtime.md) per informazioni su come impostare il numero di nodi e l'esecuzione parallela di Max per ogni nodo.
  * Runtime di integrazione SSIS viene arrestata o in uno stato non integro. Controllare [runtime di integrazione Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime) per verificare lo stato di Runtime di integrazione SSIS e gli errori.
  * È consigliabile impostare il timeout se si conosce che l'esecuzione del pacchetto deve essere completata in un certo periodo di tempo: ![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>Una riduzione delle prestazioni nell'esecuzione del pacchetto

* Causa potenziale & azione consigliata:

  * Verificare se il Runtime di integrazione SSIS nella stessa area come origine dati e di destinazione.

  * Abilitare il livello di registrazione di "Prestazioni"

      È possibile impostare il livello di registrazione dell'esecuzione del pacchetto "Prestazioni" per raccogliere ulteriori informazioni sulla durata di dettaglio per ogni componente in esecuzione. I dettagli sono disponibili in: [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * Controllare le prestazioni del nodo di runtime di integrazione nella pagina di monitoraggio di runtime di integrazione nel portale di Azure.
    * Come monitorare il Runtime di integrazione SSIS: [Runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * La cronologia dell'utilizzo di CPU/memoria del Runtime di integrazione SSIS è disponibile all'indirizzo le metriche della Data Factory nel portale di Azure ![monitorare le metriche di Runtime di integrazione SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
