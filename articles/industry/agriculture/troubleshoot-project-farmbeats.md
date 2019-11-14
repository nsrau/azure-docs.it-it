---
title: Risoluzione dei problemi
description: Come risolvere i problemi di Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 567110b2e0429930720abf6af5f2727e4ad9d3bb
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048268"
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

Le sezioni seguenti descrivono i problemi comuni di FarmBeats di Azure e come risolverli.

Per ulteriore assistenza, scrivi in farmbeatssupport@microsoft.com, Includi il file deployer. log in questo messaggio di posta elettronica.

 Seguire questa procedura per scaricare il file deployer. log:

1. Icona evidenziata e selezionare l'opzione di **download** dall'elenco a discesa.

    ![Beat Farm progetto](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. Nella schermata successiva immettere il percorso del file deployer. log. Ad esempio, farmbeats-deployer. log.

## <a name="sensor-telemetry"></a>Telemetria del sensore

### <a name="telemetry-not-seen"></a>Telemetria non visibile

**Sintomo**: i dispositivi e i sensori sono distribuiti ed è stato collegato FarmBeats al partner del dispositivo. Tuttavia, non è possibile ottenere/visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**: visitare il portale di Azure e seguire questa procedura:

1. Passare al gruppo di risorse dell'hub dati FarmBeats.   
2. Selezionare l' **Hub eventi** (DatafeedEventHubNamespace...)  e verificare il numero di messaggi in arrivo.   
3. Se non sono presenti **messaggi in ingresso**, contattare il partner del dispositivo.  
4. Se sono presenti **messaggi in ingresso**, contattare farmbeatssupport@microsoft.com con i log di hub dati e acceleratori e i dati di telemetria acquisiti.

Vedere la [sezione logs](#collect-logs-manually) del documento per informazioni su come scaricare i log.    

### <a name="device-appears-offline"></a>Il dispositivo viene visualizzato offline

**Sintomi**: i dispositivi sono installati ed è stato collegato FarmBeats al partner del dispositivo. I dispositivi sono online e inviano dati di telemetria, ma vengono visualizzati offline.

**Azione correttiva**: l'intervallo di Reporting non è configurato per questo dispositivo. Per impostare l'intervallo di Reporting, contattare il produttore del dispositivo. 

### <a name="error-deleting-a-resource"></a>Errore durante l'eliminazione di una risorsa

Di seguito sono riportati gli scenari di errore comuni durante l'eliminazione di un dispositivo:  

**Messaggio**: viene fatto riferimento al dispositivo nei sensori: uno o più sensori sono associati al dispositivo. Eliminare i sensori e quindi eliminare il dispositivo.  

**Significato**: il dispositivo è associato a più sensori distribuiti nella farm.   

**Azione correttiva**:  

1. Eliminare i sensori associati al dispositivo tramite il tasto di scelta rapida.  
2. Se si desidera associare i sensori a un dispositivo diverso, rivolgersi al partner del dispositivo.  
3. Eliminare il dispositivo usando una chiamata all'API DELETE impostando il parametro Force su' true '.  

**Messaggio**: viene fatto riferimento al dispositivo nei dispositivi come ParentDeviceId: uno o più dispositivi sono associati a questo dispositivo come dispositivi figlio. Eliminarli, quindi eliminare il dispositivo.  

**Significato**: al dispositivo sono associati altri dispositivi  

**Azione correttiva**

1. Eliminare i dispositivi associati al dispositivo specifico  
2. Eliminare il dispositivo specifico  

    > [!NOTE]
    > Non è possibile eliminare un dispositivo se sono associati sensori. Per altre informazioni su come eliminare i sensori associati, vedere [eliminare i sensori](get-sensor-data-from-sensor-partner.md) nel capitolo ottenere i dati del sensore.


## <a name="issues-with-jobs"></a>Problemi relativi ai processi

### <a name="farmbeats-internal-error"></a>Errore interno FarmBeats

**Messaggio**: errore interno di FarmBeats. per ulteriori informazioni, vedere la guida alla risoluzione dei problemi.

**Azione correttiva**: il problema potrebbe essere causato da un errore temporaneo nella pipeline di dati. Creare nuovamente il processo. Se l'errore è permanente, aggiungere l'errore in un post nel forum di FarmBeats o contattare FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Risoluzione dei problemi dell'acceleratore

### <a name="access-control"></a>Controllo dell’accesso

**Errore durante l'aggiunta dell'assegnazione di ruolo**

**Messaggio**: non sono stati trovati utenti corrispondenti

**Azione correttiva**: controllare l'ID di posta elettronica per cui si sta provando a eseguire un'assegnazione di ruolo. L'ID di posta elettronica deve corrispondere esattamente a quello registrato per l'utente nell'Active Directory. Se l'errore è permanente, aggiungere l'errore in un post nel forum di FarmBeats o contattare FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>Non è possibile accedere al tasto di scelta rapida

**Messaggio**: errore: non si è autorizzati a chiamare il servizio. Contattare l'amministratore per richiedere l'autorizzazione.

**Azione correttiva**: richiedere all'amministratore di autorizzare l'utente ad accedere alla distribuzione di FarmBeats. Questa operazione può essere eseguita eseguendo un POST delle API RoleAssignment o tramite il controllo di accesso nel riquadro impostazioni dell'acceleratore.  

Se è già stato aggiunto e si è verificato questo errore, riprovare aggiornando la pagina.  Se l'errore è permanente, aggiungere l'errore in un post nel forum di FarmBeats o contattare FarmBeatsSupport@microsoft.com.

![Beat Farm progetto](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Accelerator: si è verificato un errore sconosciuto  

**Messaggio**: errore: si è verificato un errore sconosciuto

**Azione correttiva**: questa situazione si verifica se si lascia inattiva la pagina troppo a lungo. Aggiornare la pagina.  

Se l'errore persiste, aggiungere l'errore in un post nel forum di FarmBeats o contattare FarmBeatsSupport@microsoft.com

**FarmBeats Accelerator non Visualizza la versione più recente anche dopo l'aggiornamento di FarmBeatsDeployment**

**Azione correttiva**: questo errore si verifica a causa della persistenza del servizio di lavoro nel browser.
Chiudere tutte le schede del browser con l'acceleratore aperto e chiudere la finestra del browser. Avviare una nuova istanza del browser e caricare nuovamente l'URI del tasto di scelta rapida. Verrà caricata la nuova versione del tasto di scelta rapida.

## <a name="sentinel-imagery-related-issues"></a>Problemi correlati alle immagini Sentinel

### <a name="sentinel-wrong-username-or-password"></a>Nome utente o password errata sentinella

**Messaggio di errore processo**; Per accedere a questa risorsa, è necessaria l'autenticazione completa.

**Azione di correzione**: rieseguire il programma di installazione per l'aggiornamento dell'hub dati con nome utente e password corretti.

**Azione correttiva**: rieseguire il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date di 5-7 giorni e verificare se il processo è stato completato.

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>URL errato dell'hub sentinella o non accessibile 

**Messaggio di errore del processo**: si è verificato un errore. La pagina a cui si sta tentando di accedere è temporaneamente non disponibile. 

**Azione correttiva**:
1.  Aprire l'URL di Sentinel (https://scihub.copernicus.eu/dhus/) nel browser e verificare se il sito Web è accessibile. 
2.  Se il sito Web non è accessibile, verificare se è presente un firewall/rete aziendale e così via. blocca il sito Web ed eseguire le operazioni necessarie per consentire l'URL precedente. 
3.  Eseguire di nuovo il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date di 5-7 giorni e verificare se il processo è stato completato correttamente.  

### <a name="sentinel-server-down-for-maintenance"></a>Server sentinella inattivo per manutenzione

**Messaggio di errore del processo**: l'hub Open Access di Copernico sarà presto disponibile. Ci scusiamo per l'inconveniente. stiamo eseguendo alcune operazioni di manutenzione al momento. Torniamo online a breve. 

**Azione correttiva**:

1.  Questo problema può verificarsi nel caso in cui vengano eseguite attività di manutenzione nel server Sentinel. 
2.  Se un processo o una pipeline ha esito negativo con il motivo precedente, inviare nuovamente il processo in un secondo momento. 
3.  L'utente può visitare https://scihub.copernicus.eu/news/ per verificare le informazioni sulle attività di manutenzione di Sentinel pianificate o non pianificate.  
4.  Eseguire di nuovo il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date di 5-7 giorni e verificare se il processo è stato completato correttamente.

### <a name="sentinel-maximum-number-of-connections-reached"></a>È stato raggiunto il numero massimo di connessioni Sentinel

**Messaggio di errore del processo**: numero massimo di due flussi simultanei ottenibili dall'utente "<username>" 

**Azione correttiva**
1.  Se un processo ha esito negativo con il motivo precedente, lo stesso account Sentinel viene usato in un'altra distribuzione/software. 
2.  L'utente può creare un nuovo account Sentinel ed eseguire di nuovo il programma di installazione per l'aggiornamento dell'hub dati con il nome utente e la password di Sentinel.  
3.  Eseguire di nuovo il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date di 5-7 giorni e verificare se il processo è stato completato.

### <a name="sentinel-server-refused-connection"></a>Il server Sentinel ha rifiutato la connessione 

**Messaggio di errore del processo**:

Connessione rifiutata al server: http://172.30.175.69:8983/solr/dhus 

**Azione correttiva**: questo problema può verificarsi se vengono eseguite attività di manutenzione nel server Sentinel. 
1.  Se un processo o una pipeline ha esito negativo con il motivo precedente, inviare nuovamente il processo in un secondo momento. 
2.  L'utente può visitare https://scihub.copernicus.eu/news/ per verificare le informazioni sulle attività di manutenzione di Sentinel pianificate o non pianificate.  
3.  Eseguire di nuovo il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date di 5-7 giorni e verificare se il processo è stato completato.


## <a name="collect-logs-manually"></a>Raccogli log manualmente

[Installare e distribuire]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in Azure Storage Explorer.

### <a name="how-to-collect-data-hub-adf-job-logs"></a>Come raccogliere i log del processo ADF dell'hub dati
1. Accedi a https://portal.azure.com
2. Nella casella di testo **Cerca** cercare il gruppo di risorse dell'hub dati FarmBeats.

    > [!NOTE]
    > Selezionare il gruppo di risorse dell'hub dati specificato al momento della distribuzione di FarmBeats.

Nel dashboard del gruppo di risorse cercare l'account di archiviazione (datahublogs...). Ad esempio, datahublogsmvxmq  

1.  Selezionare l'account di archiviazione nella colonna **nome** per visualizzare il dashboard dell' **account di archiviazione** .
2.  Nella pagina (datahubblogs...) selezionare **Apri in Esplora risorse** per visualizzare **Apri Azure Storage Explorer** applicazione.
3.  Nel pannello di sinistra (datahubblogs...), **contenitori BLOB**selezionare **processi-log**.
4.  Nella scheda **processi-log** selezionare **Scarica**.
5.  Selezionare la località in cui scaricare i log in una cartella locale del computer.
6.  Inviare tramite posta elettronica il file zip scaricato per farmbeatssupport@microsoft.com

    ![Beat Farm progetto](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Come raccogliere i log dei processi di Accelerator ADF

1.  Accedi a https://portal.azure.com
2.  Nella casella di testo **Cerca** cercare il gruppo di risorse FarmBeats Accelerator.

    > [!NOTE]
    > Consente di selezionare il gruppo di risorse Accelerator specificato al momento della distribuzione di FarmBeats.

3.  Nel dashboard del gruppo di risorse cercare storage.... account di archiviazione. Ad esempio, storagedop4k
4.  Selezionare l'account di archiviazione nella colonna **nome** per visualizzare il dashboard dell'account di archiviazione.
5.  Nella pagina (Storage...) selezionare **Apri in Esplora risorse** per visualizzare Apri Azure Storage Explorer applicazione.
6.  Nel pannello sinistro < storage....), i **contenitori BLOB**, selezionare **Job-logs**.
7.  Nella scheda **processi-log** selezionare **Scarica**.
8.  Selezionare la località in cui scaricare i log in una cartella locale del computer.
9.  Inviare tramite posta elettronica il file zip scaricato per farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>Come raccogliere i log del servizio app dell'hub dati

1.  Accedi a https://portal.azure.com
2.  Nella casella di testo **Cerca** cercare il gruppo di risorse dell'hub dati FarmBeats.

    > [!NOTE]
    > Selezionare il gruppo di risorse dell'hub dati specificato al momento della distribuzione di FarmBeats.

3.  Nel gruppo di risorse cercare (datahublogs....) account di archiviazione. Ad esempio, datahublogsmvxmq
4.  Selezionare l'account di archiviazione nella colonna **nome** per visualizzare il dashboard dell' **account di archiviazione** .
5.  Nella pagina (datahubblogs...) selezionare **Apri in Esplora risorse** per visualizzare **Apri Azure Storage Explorer** applicazione.
6.  Nel pannello sinistro (datahubblogs....), **contenitori BLOB**selezionare appinsights-logs.
7.  Nella scheda appinsights-logs selezionare **download**.
8.  Selezionare il percorso per scaricare i log in una cartella locale nel computer.
9.  Invia tramite posta elettronica la cartella scaricata farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>Come raccogliere i log del servizio App Accelerator

1.  Accedi a https://portal.azure.com
2.  Nella **ricerca**cercare il gruppo di risorse FarmBeats Accelerator.

    > [!NOTE]
    > Selezionare il gruppo di risorse Farmbeats Accelerator fornito al momento della distribuzione di FarmBeats.

3.  Nel gruppo di risorse cercare (Storage....) account di archiviazione. Ad esempio, storagedop4k
4.  Selezionare l'account di archiviazione nella colonna **nome** per visualizzare il dashboard dell' **account di archiviazione** .
5.  Nella pagina (Storage...) selezionare **Apri in Esplora risorse** per visualizzare **Apri Azure Storage Explorer** applicazione.
6.  Nel pannello sinistro (Storage...), **contenitori BLOB**selezionare appinsights-logs.
7.  Nella scheda appinsights-logs selezionare **download**.
8.  Selezionare la località in cui scaricare i log in una cartella locale del computer.
9.  Invia tramite posta elettronica la cartella scaricata farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Problemi noti

## <a name="batch-related-issues"></a>Problemi correlati a batch

**Errore**: è stata raggiunta la quota dell'account locale degli account batch per la sottoscrizione specificata.

**Azione correttiva**: aumentare la quota o eliminare gli account batch inutilizzati ed eseguire di nuovo la distribuzione.

### <a name="azure-active-directory-related-issues"></a>Problemi relativi a Azure Active Directory

**Errore**: non è stato possibile aggiornare le impostazioni necessarie per app Azure ad d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: privilegi insufficienti per completare l'operazione. Verificare che le impostazioni precedenti siano configurate correttamente per la App Azure AD.

**Significato**: la configurazione della registrazione dell'app Azure ad non è stata eseguita correttamente.  

**Azione correttiva**: chiedere all'amministratore IT (con accesso in lettura al tenant) di usare lo [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) per la creazione della registrazione dell'app Azure ad. Questo script si occuperà automaticamente anche dei passaggi di configurazione.

**Errore**: non è stato possibile creare la nuova applicazione Active Directory "fittiziname" in questo tenant. esiste già un altro oggetto con lo stesso valore per gli URI dell'identificatore di proprietà

**Significato**: Azure ad esiste già una registrazione dell'app con lo stesso nome.

**Azione correttiva**: eliminare la registrazione dell'app Azure ad esistente o riutilizzarla per l'installazione. Se si sta riutilizzando il Azure AD esistente, passare l'ID applicazione e il segreto client al programma di installazione e ridistribuirlo.

## <a name="inputjson-related-issues"></a>Problemi correlati a input. JSON

**Errore durante** la lettura dell'input dal file input. JSON

**Azione correttiva**: questo problema si verifica principalmente a causa della mancata specificazione del nome o del percorso JSON di input corretto per il programma di installazione. Apportare le correzioni appropriate e riprovare la distribuzione.

**Errore durante l'analisi dell'input JSON**

**Azione correttiva**: questo problema si verifica principalmente a causa di valori non corretti nel file JSON di input. Apportare le correzioni appropriate e riprovare la distribuzione.

## <a name="high-cpu-usage"></a>Uso elevato della CPU

**Errore**: si riceve un avviso di posta elettronica che fa riferimento a un avviso di utilizzo elevato della CPU. 

**Azione correttiva**: 
1.  Passare al gruppo di risorse dell'hub dati FarmBeats.
2.  Selezionare il servizio app.  
3.  Passare a scalabilità verticale (piano di servizio app) e selezionare un piano [tariffario](https://azure.microsoft.com/pricing/details/app-service/windows/) appropriato

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, contattaci nel [Forum di supporto](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
