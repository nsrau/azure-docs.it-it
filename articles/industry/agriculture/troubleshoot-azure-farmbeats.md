---
title: Risoluzione dei problemi di Azure FarmBeats
description: In questo articolo viene descritto come risolvere i problemi di Azure FarmBeats.This article describes how to troubleshoot Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a31eece6629558b14b614853addce59642e698b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422732"
---
# <a name="troubleshoot"></a>Risolvere problemi

Questo articolo fornisce soluzioni ai problemi comuni di Azure FarmBeats.This article provides solutions to common Azure FarmBeats issues.

Per ulteriore assistenza, farmbeatssupport@microsoft.comcontattaci all'indirizzo . Assicurarsi di includere il file **deployer.log** nella posta elettronica.

Per scaricare il file **deployer.log,** eseguire le operazioni seguenti:

1. Accedere al **portale** di Azure e selezionare la sottoscrizione e il tenant di Azure AD.
2. Avviare Cloud Shell dal riquadro di spostamento in alto nel portale di Azure.
3. Selezionare **Bash** come esperienza di Cloud Shell preferita.
4. Selezionare l'icona evidenziata e quindi nell'elenco a discesa selezionare **Scarica**.

    ![Progetto FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. Nel riquadro successivo immettere il percorso del file **deployer.log.** Ad esempio, immettere **farmbeats-deployer.log**.

## <a name="sensor-telemetry"></a>Telemetria dei sensori

### <a name="cant-view-telemetry-data"></a>Impossibile visualizzare i dati di telemetria

**Sintomo:** i dispositivi o i sensori vengono distribuiti e FarmBeats è stato collegato al partner del dispositivo, ma non è possibile ottenere o visualizzare dati di telemetria in FarmBeats.

**Azione correttiva**:

1. Passare al gruppo di risorse FarmBeats Datahub.Go to your FarmBeats Datahub resource group.   

2. Selezionare **l'hub eventi** (DatafeedEventHubNamespace) e quindi verificare il numero di messaggi in arrivo.

3. Effettuare una delle operazioni seguenti:   
   - Se non sono presenti *messaggi in arrivo,* contattare il partner del dispositivo.  
   - Se sono presenti messaggi farmbeatssupport@microsoft.comin *arrivo,* contattare . Collegare i log di Datahub e Accelerator e i dati di telemetria acquisiti.

Per informazioni su come scaricare i log, passare alla sezione ["Raccogli log manualmente".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Impossibile visualizzare i dati di telemetria dopo l'inserimento di dati cronologici/in streaming dai sensori

**Sintomo:** i dispositivi o i sensori vengono distribuiti e i dispositivi/sensori sono stati creati in FarmBeats e sono stati ingeriti dati di telemetria in EventHub, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**:

1. Assicurati di aver fatto correttamente la registrazione del partner - puoi controllarlo andando al tuo datahub swagger, passare a /Partner API, Do a Get e controllare se il partner è registrato. In caso contrario, seguire i [passaggi qui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) per aggiungere partner.

2. Assicurarsi di aver utilizzato il formato corretto del messaggio di telemetria:Ensure that you have used the correct Telemetry message format:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Non è la stringa di connessione Hub eventi di Azure

**Azione correttiva**:

1. In Datahub Swagger passare all'API partner.
2. Selezionare **Get** > **Try it out** > **Execute**.

> [!NOTE]
> ID partner del partner sensore a cui sei interessato.

3. Tornare all'API del partner e selezionare **Ottieni/ID\<>**.
4. Specificare l'ID partner del passaggio 3, quindi selezionare **Esegui**.

   La risposta API deve avere la stringa di connessione Hub eventi.

### <a name="device-appears-offline"></a>Il dispositivo viene visualizzato offline

**Sintomi:** i dispositivi sono installati e FarmBeats è stato collegato al partner del dispositivo. I dispositivi sono online e inviano dati di telemetria, ma vengono visualizzati offline.

**Azione correttiva**: L'intervallo di segnalazione non è configurato per questo dispositivo. Per impostare l'intervallo di segnalazione, contattare il produttore del dispositivo. 

### <a name="error-deleting-a-device"></a>Errore durante l'eliminazione di un dispositivo

Durante l'eliminazione di un dispositivo, è possibile riscontrare uno dei seguenti scenari di errore comuni:  

**Messaggio**: "Il dispositivo viene fatto riferimento nei sensori: al dispositivo sono associati uno o più sensori. Eliminare i sensori e quindi il dispositivo."  

**Significato**: Il dispositivo è associato a più sensori distribuiti nella farm.   

**Azione correttiva**:  

1. Eliminare i sensori associati al dispositivo tramite Accelerator.  
2. Se si desidera associare i sensori a un dispositivo diverso, chiedere al partner del dispositivo di eseguire la stessa operazione.  
3. Eliminare il dispositivo `DELETE API` utilizzando una chiamata e impostare il parametro force su *true*.  

**Messaggio:**"Viene fatto riferimento a un dispositivo nei dispositivi come ParentDeviceId: sono presenti uno o più dispositivi associati a questo dispositivo come dispositivi figlio. Eliminarli, quindi eliminare il dispositivo."  

**Significato**: Al dispositivo sono associati altri dispositivi.  

**Azione correttiva**

1. Eliminare i dispositivi associati a questo dispositivo specifico.  
2. Eliminare il dispositivo specifico.  

    > [!NOTE]
    > Non è possibile eliminare un dispositivo se vi sono associati sensori. Per ulteriori informazioni su come eliminare i sensori associati, vedere la sezione **Eliminare i sensori** in Ottenere i dati dei sensori dai partner dei [sensori](get-sensor-data-from-sensor-partner.md).

    > I partner non hanno accesso per eliminare un dispositivo o un sensore. Solo gli amministratori hanno accesso per fare lo stesso.


## <a name="issues-with-jobs"></a>Problemi con i lavori

### <a name="farmbeats-internal-error"></a>Errore interno farmBeats

**Messaggio:**"Errore interno FarmBeats, vedere la guida alla risoluzione dei problemi per ulteriori dettagli".

**Azione correttiva**: Questo problema potrebbe derivare da un errore temporaneo nella pipeline di dati. Creare nuovamente il processo. Se l'errore persiste, aggiungere il messaggio di errore in FarmBeatsSupport@microsoft.comun post nel forum FarmBeats o contattare .

## <a name="accelerator-troubleshooting"></a>Risoluzione dei problemi relativi agli acceleratori

### <a name="access-control"></a>Controllo di accesso

**Problema:** viene visualizzato un errore durante l'aggiunta di un'assegnazione di ruolo.

**Messaggio:**"Nessun utente corrispondente trovato".

**Azione correttiva**: Controllare l'ID di posta elettronica per il quale si sta tentando di aggiungere un'assegnazione di ruolo. L'ID di posta elettronica deve essere una corrispondenza esatta dell'ID, che viene registrato per tale utente in Active Directory. Se l'errore persiste, aggiungere il messaggio di errore in FarmBeatsSupport@microsoft.comun post nel forum FarmBeats o contattare .

### <a name="unable-to-log-in-to-accelerator"></a>Impossibile accedere all'acceleratore

**Messaggio**: "Errore: Non si è autorizzati a chiamare il servizio. Contattare l'amministratore per l'autorizzazione."

**Azione correttiva**: Chiedere all'amministratore di autorizzare l'utente ad accedere alla distribuzione di FarmBeats. Questa operazione può essere eseguita eseguendo un POST delle API RoleAssignment o tramite il controllo di accesso nel riquadro **Impostazioni** in Accelerator.  

Se hai già ottenuto l'accesso e stai affrontando questo errore, riprova aggiornando la pagina. Se l'errore persiste, aggiungere il messaggio di errore in FarmBeatsSupport@microsoft.comun post nel forum FarmBeats o contattare .

![Progetto FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemi dell'acceleratore  

**Problema:** si è verificato un errore di accelerazione di causa indeterminata.

**Messaggio:**"Errore: si è verificato un errore sconosciuto".

**Azione correttiva**: Questo errore si verifica se si lascia la pagina inattiva per troppo tempo. Aggiornare la pagina.  

Se l'errore persiste, aggiungere il messaggio di errore in FarmBeatsSupport@microsoft.comun post nel forum FarmBeats o contattare .

**Problema:** FarmBeats Accelerator non mostra la versione più recente, anche dopo aver aggiornato FarmBeatsDeployment.

**Azione correttiva:** questo errore si verifica a causa della persistenza del service worker nel browser. Eseguire le operazioni seguenti:

1. Chiudere tutte le schede del browser con acceleratore aperto e chiudere la finestra del browser.
2. Avviare una nuova istanza del browser e ricaricare l'URI dell'acceleratore. Questa azione carica la nuova versione di Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problemi relativi alle immagini

### <a name="wrong-username-or-password"></a>Nome utente o password errati

**Messaggio di errore del processo:**"È necessaria l'autenticazione completa per accedere a questa risorsa".

**Azione correttiva**:

Eseguire una delle operazioni seguenti:

- Eseguire nuovamente il programma di installazione per l'aggiornamento di Datahub con il nome utente e la password corretti.
- Eseguire nuovamente il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date da 5 a 7 giorni, quindi verificare se il processo ha esito positivo.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub Sentinel: URL errato o sito non accessibile 

**Messaggio**di errore del processo : "Ops, qualcosa è andato storto. La pagina a cui si stava tentando di accedere è (temporaneamente) non disponibile." 

**Azione correttiva**:
1. Apri [Sentinel](https://scihub.copernicus.eu/dhus/) nel tuo browser per vedere se il sito web è accessibile. 
2. Se il sito Web non è accessibile, verificare se un firewall, una rete aziendale o un altro software di blocco impedisce l'accesso al sito Web e quindi eseguire le operazioni necessarie per consentire l'URL Sentinel. 
3. Eseguire nuovamente il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date da 5 a 7 giorni, quindi verificare se il processo ha esito positivo.  

### <a name="sentinel-server-down-for-maintenance"></a>Server Sentinel: inattivo per la manutenzione

**Messaggio**di errore del processo : "L'hub di accesso aperto Copernicus tornerà presto! Ci scusiamo per l'inconveniente, stiamo eseguendo qualche manutenzione al momento. Torneremo online a breve! 

**Azione correttiva**:

Questo problema può verificarsi se vengono eseguite attività di manutenzione sul server Sentinel.

1. Se un processo o una pipeline non riesce perché viene eseguita la manutenzione, inviare nuovamente il processo dopo un certo periodo di tempo. 

   Per informazioni su eventuali attività di manutenzione di Sentinel pianificate o non pianificate, visitare il sito [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Eseguire nuovamente il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date da 5 a 7 giorni, quindi verificare se il processo ha esito positivo.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: Numero massimo di connessioni raggiunte

Messaggio di **errore del processo:**"Numero massimo di\<due flussi simultanei ottenuti dall'utente 'nome utente>'".

**Significato**: Se un processo non riesce perché è stato raggiunto il numero massimo di connessioni, lo stesso account Sentinel viene utilizzato in un'altra distribuzione del software.

**Azione correttiva**: Provare a eseguire una delle operazioni seguenti:

* Creare un nuovo account Sentinel e quindi eseguire nuovamente il programma di installazione per aggiornare Datahub utilizzando un nuovo nome utente e password Sentinel.  
* Eseguire nuovamente il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date da 5 a 7 giorni, quindi verificare se il processo ha esito positivo.

### <a name="sentinel-server-refused-connection"></a>Server Sentinel: connessione rifiutata 

**Messaggio di errore del processo** http://172.30.175.69:8983/solr/dhus: "Server ha rifiutato la connessione in: ." 

**Azione correttiva**: Questo problema può verificarsi se vengono eseguite attività di manutenzione sul server Sentinel. 
1. Se un processo o una pipeline non riesce perché viene eseguita la manutenzione, inviare nuovamente il processo dopo un certo periodo di tempo. 

   Per informazioni su eventuali attività di manutenzione di Sentinel pianificate o non pianificate, visitare il sito [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Eseguire nuovamente il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date da 5 a 7 giorni, quindi verificare se il processo ha esito positivo.

### <a name="soil-moisture-map-has-white-areas"></a>Soil Mappa dell'umidità ha aree bianche 

**Problema**: La mappa dell'umidità del suolo è stata generata, ma la mappa ha per lo più aree bianche.

**Azione correttiva**: Questo problema può verificarsi se gli indici satellite generati per il tempo per cui è stata richiesta la mappa ha valori NDVI inferiori a 0,3. Per ulteriori informazioni, consultare la Guida Tecnica di [Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).
1. Eseguire nuovamente il processo per un intervallo di date diverso e verificare se i valori NDVI negli indici satellite sono più di 0,3

## <a name="collect-logs-manually"></a>Raccogliere i registri manualmente

[Installare e distribuire Azure Storage Explorer.]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Raccogliere i log dei processi di Azure Data Factory in DatahubCollect Azure Data Factory job logs in Datahub

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Datahub.

    > [!NOTE]
    > Selezionare il gruppo di risorse Datahub specificato durante l'installazione di FarmBeats.Select the Datahub resource group that you specified during FarmBeats installation.

3. Nel dashboard **Del gruppo di risorse** cercare l'account di archiviazione *datahublogs.\* * Ad esempio, cercare **datahublogsmvxmq**.  
4. Nella colonna **Nome** selezionare l'account di archiviazione per visualizzare il dashboard **dell'account di archiviazione.**
5. Nel riquadro datahubblogs selezionare **Apri in Esplora risorse** per visualizzare l'applicazione Apri Esplora archivi di Azure.In the **datahubblogs\* ** pane, select Open in Explorer to view the Open Azure Storage **Explorer** application.
6. Nel riquadro sinistro selezionare **Contenitori BLOB**e quindi selezionare **job-logs**.
7. Nel riquadro **dei log dei processi** selezionare **Scarica**.
8. Scaricare i registri in una cartella locale sul computer.
9. Invia il file .zip scaricato via e-mail a farmbeatssupport@microsoft.com.

    ![Progetto FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Raccogliere i log dei processi di Azure Data Factory in AcceleratorCollect Azure Data Factory job logs in Accelerator

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Accelerator.

    > [!NOTE]
    > Selezionare il gruppo di risorse Accelerator specificato durante l'installazione di FarmBeats.Select the Accelerator resource group that you specified during FarmBeats installation.

3. Nel dashboard **Gruppo di risorse** cercare l'account di *archiviazione.\* * Ad esempio, cercare **storagedop4k\***.
4. Selezionare l'account di archiviazione nella colonna **Nome** per visualizzare il dashboard **Account di archiviazione.**
5. Nel riquadro di archiviazione selezionare **Apri in Esplora risorse** per aprire l'applicazione Esplora archivi di Azure.In the **storage\* ** pane, select Open in Explorer to open the Azure Storage Explorer application.
6. Nel riquadro sinistro selezionare **Contenitori BLOB**e quindi selezionare **job-logs**.
7. Nel riquadro **dei log dei processi** selezionare **Scarica**.
8. Scaricare i registri in una cartella locale sul computer.
9. Invia il file .zip scaricato via e-mail a farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Raccogliere i log del servizio app DatahubCollect Datahub app service logs

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Datahub.

    > [!NOTE]
    > Selezionare il gruppo di risorse Datahub specificato durante l'installazione di FarmBeats.Select the Datahub resource group that you specified during FarmBeats installation.

3. Nel gruppo di risorse cercare l'account di archiviazione *datahublogs.\* * Ad esempio, cercare **fordatahublogsmvxmq\***.
4. Selezionare l'account di archiviazione nella colonna **Nome** per visualizzare il dashboard **Account di archiviazione.**
5. Nel riquadro datahubblogs selezionare **Apri in Esplora risorse** per aprire l'applicazione Azure Storage Explorer.In the **datahubblogs\* ** pane, select Open in Explorer to open the Azure Storage Explorer application.
6. Nel riquadro sinistro selezionare **Contenitori BLOB**e quindi **appinsights-logs**.
7. Nel riquadro **appinsights-logs** selezionare **Download**.
8. Scaricare i registri in una cartella locale sul computer.
9. Invia il file .zip scaricato via e-mail a farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>Raccogliere i log del servizio app acceleratoreCollect Accelerator app service logs

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Accelerator.

    > [!NOTE]
    > Selezionare il gruppo di risorse FarmBeats Accelerator fornito durante l'installazione di FarmBeats.Select the FarmBeats Accelerator resource group that was provided during FarmBeats installation.

3. Nel gruppo di risorse cercare l'account di *archiviazione.\* * Ad esempio, cercare **storagedop4k\***.
4. Selezionare l'account di archiviazione nella colonna **Nome** per visualizzare il dashboard **Account di archiviazione.**
5. Nel riquadro di archiviazione selezionare **Apri in Esplora risorse** per aprire l'applicazione Esplora archivi di Azure.In the **storage\* ** pane, select Open in Explorer to open the Azure Storage Explorer application.
6. Nel riquadro sinistro selezionare **Contenitori BLOB**e quindi **appinsights-logs**.
7. Nel riquadro **appinsights-logs** selezionare **Download**.
8. Scaricare i registri in una cartella locale sul computer.
9. Invia e-mail farmbeatssupport@microsoft.comla cartella scaricata a .

## <a name="known-issues"></a>Problemi noti

## <a name="batch-related-issues"></a>Problemi relativi al batch

**Messaggio di errore:**"È stata raggiunta la quota dell'account regionale degli account batch per la sottoscrizione specificata."

**Azione correttiva**: Aumentare la quota o eliminare gli account batch inutilizzati ed eseguire nuovamente la distribuzione.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problemi correlati ad Azure Active Directory (Azure AD)Azure Active Directory (Azure AD)-related issues

Messaggio di **errore:**"Impossibile aggiornare le impostazioni necessarie per l'app azure AD d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: privilegi insufficienti per completare l'operazione. Verificare che le impostazioni precedenti siano configurate correttamente per l'app Azure AD."

**Significato:** la configurazione di registrazione dell'app Azure AD non è stata completata correttamente.  

**Azione correttiva:** chiedere all'amministratore IT (la persona con accesso in lettura tenant) di usare [lo script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) per la creazione della registrazione dell'app Azure AD. Questo script si occupa automaticamente anche dei passaggi di configurazione.

**Messaggio di errore:**"Impossibile creare\<una\>nuova applicazione Active Directory ' nome applicazione ' in questo tenant: un altro oggetto con lo stesso valore per gli URI dell'identificatore di proprietà esiste già."

**Significato:** esiste già una registrazione dell'app Azure AD con lo stesso nome.

**Azione correttiva:** eliminare la registrazione dell'app Azure AD esistente o riutilizzarla per l'installazione. Se si riutilizza la registrazione dell'app Azure AD esistente, passare l'ID applicazione e il segreto client al programma di installazione e ridistribuire.

## <a name="issues-with-the-inputjson-file"></a>Problemi con il file input.json

**Errore:** errore durante la lettura dell'input dal file *input.json.*

**Azione correttiva**: Questo problema si verifica in genere a causa di un errore nella specifica del percorso del file *input.json* corretto o il nome del programma di installazione. Apportare le correzioni appropriate e ripetere la distribuzione.

**Errore:** errore durante l'analisi dei valori nel file *input.json.*

**Azione correttiva**: Questo problema si verifica principalmente a causa di valori non corretti all'interno del file *input.json.* Apportare le correzioni appropriate e ripetere la distribuzione.

## <a name="high-cpu-usage"></a>Uso elevato della CPU

**Errore:** viene visualizzato un avviso tramite posta elettronica che fa riferimento a un avviso di **utilizzo elevato della CPU**. 

**Azione correttiva**: 
1. Passare al gruppo di risorse FarmBeats Datahub.Go to your FarmBeats Datahub resource group.
2. Selezionare il **servizio app**.  
3. Passare alla [pagina dei prezzi](https://azure.microsoft.com/pricing/details/app-service/windows/)del servizio app con scalabilità verticale e quindi selezionare un piano tariffario appropriato.

## <a name="next-steps"></a>Passaggi successivi

Se continui ad affrontare i problemi di FarmBeats, contatta il nostro Forum di [supporto.](https://aka.ms/farmbeatssupport)
