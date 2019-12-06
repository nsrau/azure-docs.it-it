---
title: Risolvere i problemi di Azure FarmBeats
description: Questo articolo descrive come risolvere i problemi di Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0a4fb337adfb2f4e6b8edb86ac620103e929c3a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842136"
---
# <a name="troubleshoot-azure-farmbeats"></a>Risolvere i problemi di Azure FarmBeats

Questo articolo fornisce soluzioni ai problemi comuni di Azure FarmBeats.

Per ulteriore assistenza, contattaci all'farmbeatssupport@microsoft.com. Assicurarsi di includere il file *deployer. log* nel messaggio di posta elettronica.

Per scaricare il file *deployer. log* , eseguire le operazioni seguenti:

1. Selezionare l'icona evidenziata e quindi nell'elenco a discesa selezionare **Scarica**.

    ![FarmBeats progetto](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

1. Nel riquadro successivo immettere il percorso del file *deployer. log* . Ad esempio, immettere **farmbeats-deployer. log**.

## <a name="sensor-telemetry"></a>Telemetria del sensore

### <a name="cant-view-telemetry-data"></a>Non è possibile visualizzare i dati di telemetria

**Sintomo**: i dispositivi o i sensori sono distribuiti ed è stato collegato FarmBeats con il partner del dispositivo, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**: 

1. Passare al gruppo di risorse FarmBeats datahub.   
1. Selezionare l' **Hub eventi** (DatafeedEventHubNamespace), quindi controllare il numero di messaggi in arrivo.
1. Eseguire una di queste operazioni:   
   * Se non sono presenti *messaggi in ingresso*, contattare il partner del dispositivo.  
   * Se sono presenti *messaggi in ingresso*, contattare farmbeatssupport@microsoft.com. Alleghi i log datahub e Accelerator e i dati di telemetria acquisiti.

Per informazioni su come scaricare i log, vedere la sezione ["raccogliere i log manualmente"](#collect-logs-manually) .  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Non avere la stringa di connessione di hub eventi di Azure

**Azione correttiva**: 

1. In datahub spavalderia passare all'API partner.
1. Selezionare **Get** > **provare** > **Execute**.
1. Prendere nota dell'ID partner del partner dei sensori a cui si è interessati.
1. Tornare all'API partner e selezionare **Get/\<ID >** .
1. Specificare l'ID partner del passaggio 3, quindi selezionare **Esegui**.
   
   La risposta API deve avere la stringa di connessione di hub eventi.

### <a name="device-appears-offline"></a>Il dispositivo viene visualizzato offline

**Sintomi**: i dispositivi sono installati ed è stato collegato FarmBeats con il partner del dispositivo. I dispositivi sono online e inviano dati di telemetria, ma vengono visualizzati offline.

**Azione correttiva**: l'intervallo di Reporting non è configurato per questo dispositivo. Per impostare l'intervallo di Reporting, contattare il produttore del dispositivo. 

### <a name="error-deleting-a-device"></a>Errore durante l'eliminazione di un dispositivo

Durante l'eliminazione di un dispositivo, è possibile che si verifichi uno degli scenari di errore comuni seguenti:  

**Messaggio**: "viene fatto riferimento al dispositivo nei sensori: uno o più sensori sono associati al dispositivo. Eliminare i sensori e quindi eliminare il dispositivo ".  

**Significato**: il dispositivo è associato a più sensori distribuiti nella farm.   

**Azione correttiva**:  

1. Eliminare i sensori associati al dispositivo tramite l'acceleratore.  
1. Se si desidera associare i sensori a un dispositivo diverso, rivolgersi al partner del dispositivo.  
1. Eliminare il dispositivo usando una chiamata `DELETE API` e impostare il parametro Force su *true*.  

**Messaggio**: "viene fatto riferimento al dispositivo nei dispositivi come ParentDeviceId: uno o più dispositivi sono associati a questo dispositivo come dispositivi figlio. Eliminarli e quindi eliminare il dispositivo ".  

**Significato**: al dispositivo sono associati altri dispositivi.  

**Azione correttiva**

1. Eliminare i dispositivi associati a questo dispositivo specifico.  
1. Eliminare il dispositivo specifico.  

    > [!NOTE]
    > Non è possibile eliminare un dispositivo se sono associati sensori. Per altre informazioni su come eliminare i sensori associati, vedere la sezione "eliminare il sensore" in [ottenere i dati dei sensori dai partner di sensori](get-sensor-data-from-sensor-partner.md).


## <a name="issues-with-jobs"></a>Problemi relativi ai processi

### <a name="farmbeats-internal-error"></a>Errore interno FarmBeats

**Messaggio**: "errore interno di FarmBeats. per ulteriori informazioni, vedere la guida alla risoluzione dei problemi".

**Azione correttiva**: questo problema potrebbe essere causato da un errore temporaneo nella pipeline di dati. Creare nuovamente il processo. Se l'errore è permanente, aggiungere il messaggio di errore in un post nel forum di FarmBeats o contattare FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Risoluzione dei problemi dell'acceleratore

### <a name="access-control"></a>Controllo di accesso

**Problema**: viene visualizzato un errore durante l'aggiunta di un'assegnazione di ruolo.

**Messaggio**: "non sono stati trovati utenti corrispondenti".

**Azione correttiva**: controllare l'ID di posta elettronica per cui si sta provando ad aggiungere un'assegnazione di ruolo. L'ID di posta elettronica deve corrispondere esattamente all'ID registrato per l'utente nella Active Directory. Se l'errore è permanente, aggiungere il messaggio di errore in un post nel forum di FarmBeats o contattare FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Non è possibile accedere al tasto di scelta rapida

**Messaggio**: "errore: non si è autorizzati a chiamare il servizio. Contattare l'amministratore per richiedere l'autorizzazione ".

**Azione correttiva**: richiedere all'amministratore di autorizzare l'utente ad accedere alla distribuzione di FarmBeats. Questa operazione può essere eseguita eseguendo un POST delle API RoleAssignment o tramite il controllo di accesso nel riquadro **Impostazioni** in Accelerator.  

Se è già stato concesso l'accesso e si è verificato questo errore, riprovare aggiornando la pagina. Se l'errore è permanente, aggiungere il messaggio di errore in un post nel forum di FarmBeats o contattare FarmBeatsSupport@microsoft.com.

![FarmBeats progetto](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemi relativi all'acceleratore  

**Problema**: si è ricevuto un errore dell'acceleratore di causa indeterminata.

**Messaggio**: "errore: si è verificato un errore sconosciuto".

**Azione correttiva**: questo errore si verifica se si lascia inattiva la pagina troppo a lungo. Aggiornare la pagina.  

Se l'errore è permanente, aggiungere il messaggio di errore in un post nel forum di FarmBeats o contattare FarmBeatsSupport@microsoft.com.

**Problema**: FarmBeats Accelerator non Visualizza la versione più recente, anche dopo l'aggiornamento di FarmBeatsDeployment.

**Azione correttiva**: questo errore si verifica a causa della persistenza del servizio di lavoro nel browser. Eseguire le operazioni seguenti:
1. Chiudere tutte le schede del browser con tasto di scelta rapida aperto e chiudere la finestra del browser. 
1. Avviare una nuova istanza del browser e ricaricare l'URI del tasto di scelta rapida. Questa azione carica la nuova versione di Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: problemi correlati alle immagini

### <a name="wrong-username-or-password"></a>Nome utente o password errata

**Messaggio di errore del processo**: "è necessaria l'autenticazione completa per accedere a questa risorsa".

**Azione correttiva**: 

Eseguire una di queste operazioni:
* Eseguire di nuovo il programma di installazione per l'aggiornamento di datahub con il nome utente e la password corretti.
* Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi verificare se il processo ha avuto esito positivo.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub sentinella: URL errato o sito non accessibile 

**Messaggio di errore del processo**: "si è verificato un errore. La pagina a cui si sta tentando di accedere è temporaneamente non disponibile. " 

**Azione correttiva**:
1. Aprire [Sentinel](https://scihub.copernicus.eu/dhus/) nel browser per verificare se il sito Web è accessibile. 
1. Se il sito Web non è accessibile, verificare se qualsiasi firewall, rete aziendale o altro software di blocco impedisce l'accesso al sito Web e quindi eseguire i passaggi necessari per consentire l'URL di Sentinel. 
1. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi verificare se il processo ha avuto esito positivo.  

### <a name="sentinel-server-down-for-maintenance"></a>Server Sentinel: per la manutenzione

**Messaggio di errore del processo**: "l'hub Open Access Copernico sarà presto disponibile. Ci scusiamo per l'inconveniente. stiamo eseguendo alcune operazioni di manutenzione al momento. Torniamo online a breve! ". 

**Azione correttiva**:

Questo problema può verificarsi nel caso in cui vengano eseguite attività di manutenzione nel server Sentinel.

1. Se un processo o una pipeline ha esito negativo perché è in corso l'esecuzione della manutenzione, inviare nuovamente il processo tra qualche minuto. 

   Per informazioni sulle attività di manutenzione di Sentinel pianificate o non pianificate, visitare il sito di [notizie di Copernico Open Access Hub](https://scihub.copernicus.eu/news/) .  
1. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi verificare se il processo ha avuto esito positivo.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinella: è stato raggiunto il numero massimo di connessioni

**Messaggio di errore del processo**: "numero massimo di due flussi simultanei ottenibili dall'utente '\<nome utente >'".

**Significato**: se un processo ha esito negativo perché è stato raggiunto il numero massimo di connessioni, viene usato lo stesso account Sentinel in un'altra distribuzione software.

**Azione correttiva**: provare una delle seguenti operazioni:
* Creare un nuovo account Sentinel, quindi eseguire di nuovo il programma di installazione per aggiornare datahub usando un nuovo nome utente e una password di Sentinel.  
* Eseguire di nuovo il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi verificare se il processo ha avuto esito positivo.

### <a name="sentinel-server-refused-connection"></a>Server sentinella: la connessione è stata rifiutata 

**Messaggio di errore del processo**: "il server ha rifiutato la connessione in: http://172.30.175.69:8983/solr/dhus." 

**Azione correttiva**: questo problema può verificarsi se vengono eseguite attività di manutenzione nel server Sentinel. 
1. Se un processo o una pipeline ha esito negativo perché è in corso l'esecuzione della manutenzione, inviare nuovamente il processo tra qualche minuto. 

   Per informazioni sulle attività di manutenzione di Sentinel pianificate o non pianificate, visitare il sito di [notizie di Copernico Open Access Hub](https://scihub.copernicus.eu/news/) .  
1. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi verificare se il processo ha avuto esito positivo.

## <a name="collect-logs-manually"></a>Raccogli log manualmente

[Installare e distribuire Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Raccolta dei log dei processi di Azure Data Factory in datahub
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella casella di **ricerca** cercare il gruppo di risorse FarmBeats datahub.

    > [!NOTE]
    > Selezionare il gruppo di risorse datahub specificato durante la distribuzione di FarmBeats.

1. Nel dashboard del **gruppo di risorse** cercare *datahublogs\** account di archiviazione. Ad esempio, cercare **datahublogsmvxmq**.  

1. Nella colonna **nome** selezionare l'account di archiviazione per visualizzare il dashboard dell' **account di archiviazione** .
1. Nel riquadro **\*datahubblogs** selezionare **Apri in Esplora risorse** per visualizzare l'applicazione **Azure Storage Explorer aperta** .
1. Nel riquadro sinistro selezionare **contenitori BLOB**, quindi selezionare **processi-log**.
1. Nel riquadro **processi-log** selezionare **Scarica**.
1. Scaricare i log in una cartella locale nel computer.
1. Inviare tramite posta elettronica il file zip scaricato per farmbeatssupport@microsoft.com.

    ![FarmBeats progetto](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Raccolta dei log dei processi di Azure Data Factory in Accelerator 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella casella di **ricerca** cercare il gruppo di risorse FarmBeats Accelerator.

    > [!NOTE]
    > Selezionare il gruppo di risorse Accelerator specificato durante la distribuzione di FarmBeats.

1. Nel dashboard del **gruppo di risorse** cercare *archiviazione\** account di archiviazione. Ad esempio, cercare **storagedop4k\*** .
1. Selezionare l'account di archiviazione nella colonna **nome** per visualizzare il dashboard dell' **account di archiviazione** .
1. Nel riquadro **\*di archiviazione** selezionare **Apri in Esplora risorse** per aprire l'applicazione Azure Storage Explorer.
1. Nel riquadro sinistro selezionare **contenitori BLOB**, quindi selezionare **processi-log**.
1. Nel riquadro **processi-log** selezionare **Scarica**.
1. Scaricare i log in una cartella locale nel computer.
1. Inviare tramite posta elettronica il file zip scaricato per farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Raccolta dei log del servizio app datahub

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella casella di **ricerca** cercare il gruppo di risorse FarmBeats datahub.

    > [!NOTE]
    > Selezionare il gruppo di risorse datahub specificato durante la distribuzione di FarmBeats.

1. Nel gruppo di risorse cercare *datahublogs\** account di archiviazione. Ad esempio, cercare **fordatahublogsmvxmq\*** .
1. Selezionare l'account di archiviazione nella colonna **nome** per visualizzare il dashboard dell' **account di archiviazione** .
1. Nel riquadro **\*datahubblogs** selezionare **Apri in Esplora risorse** per aprire l'applicazione Azure Storage Explorer.
1. Nel riquadro sinistro selezionare **contenitori BLOB**e quindi selezionare **appinsights-logs**.
1. Nel riquadro **appinsights-logs** selezionare **download**.
1. Scaricare i log in una cartella locale nel computer.
1. Inviare tramite posta elettronica il file zip scaricato per farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>Raccolta dei log del servizio App Accelerator

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella casella di **ricerca** cercare il gruppo di risorse FarmBeats Accelerator.

    > [!NOTE]
    > Selezionare il gruppo di risorse FarmBeats Accelerator fornito durante la distribuzione di FarmBeats.

1. Nel gruppo di risorse cercare *archiviazione\** account di archiviazione. Ad esempio, cercare **storagedop4k\*** .
1. Selezionare l'account di archiviazione nella colonna **nome** per visualizzare il dashboard dell' **account di archiviazione** .
1. Nel riquadro **\*di archiviazione** selezionare **Apri in Esplora risorse** per aprire l'applicazione Azure Storage Explorer.
1. Nel riquadro sinistro selezionare **contenitori BLOB**e quindi selezionare **appinsights-logs**.
1. Nel riquadro **appinsights-logs** selezionare **download**.
1. Scaricare i log in una cartella locale nel computer.
1. Inviare tramite posta elettronica la cartella scaricata farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Problemi noti

## <a name="batch-related-issues"></a>Problemi correlati a batch

**Messaggio di errore**: "è stata raggiunta la quota dell'account locale degli account batch per la sottoscrizione specificata".

**Azione correttiva**: aumentare la quota o eliminare gli account batch inutilizzati e rieseguire la distribuzione.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problemi relativi a Azure Active Directory (Azure AD)

**Messaggio di errore**: "Impossibile aggiornare le impostazioni necessarie per app Azure ad d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: privilegi insufficienti per completare l'operazione. Verificare che le impostazioni precedenti siano configurate correttamente per la App Azure AD ".

**Significato**: la configurazione della registrazione dell'app Azure ad non è stata completata correttamente.  

**Azione correttiva**: chiedere all'amministratore IT (la persona con accesso in lettura al tenant) di usare lo [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) per creare la registrazione dell'app Azure ad. Questo script si occupa automaticamente anche dei passaggi di configurazione.

**Messaggio di errore**: "Impossibile creare una nuova applicazione Active Directory '\<nome applicazione\>' in questo tenant: esiste già un altro oggetto con lo stesso valore per gli URI dell'identificatore di proprietà".

**Significato**: esiste già una registrazione dell'app Azure ad con lo stesso nome.

**Azione correttiva**: eliminare la registrazione dell'app Azure ad esistente o riutilizzarla per l'installazione. Se si sta riutilizzando la registrazione dell'app Azure AD esistente, passare l'ID applicazione e il segreto client al programma di installazione e ridistribuirlo.

## <a name="issues-with-the-inputjson-file"></a>Problemi con il file input. JSON

**Errore**: si è verificato un errore durante la lettura dell'input dal file *input. JSON* .

**Azione correttiva**: questo problema si verifica in genere a causa di un errore nel specificare il percorso o il nome del file *input. JSON* corretto per il programma di installazione. Apportare le correzioni appropriate e ripetere la ridistribuzione.

**Errore**: si è verificato un errore durante l'analisi dei valori nel file *input. JSON* .

**Azione correttiva**: questo problema si verifica principalmente a causa di valori non corretti nel file *input. JSON* . Apportare le correzioni appropriate e ripetere la distribuzione.

## <a name="high-cpu-usage"></a>Uso elevato della CPU

**Errore**: si riceve un avviso di posta elettronica che fa riferimento a un *avviso di utilizzo elevato della CPU*. 

**Azione correttiva**: 
1. Passare al gruppo di risorse FarmBeats datahub.
1. Selezionare il servizio app.  
1. Passare alla [pagina dei prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/)con scalabilità verticale e quindi selezionare un piano tariffario appropriato.

## <a name="next-steps"></a>Passaggi successivi

Se sono ancora presenti problemi di FarmBeats, contattare il [Forum di supporto](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
