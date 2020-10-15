---
title: Risoluzione dei problemi di Azure FarmBeats
description: Questo articolo descrive come risolvere i problemi di Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c66d3d6fd3ee0bcba01db61183f40cd3ccf3f39d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797939"
---
# <a name="troubleshoot-azure-farmbeats"></a>Risoluzione dei problemi di Azure FarmBeats

Questo articolo offre soluzioni ai problemi comuni di Azure FarmBeats. Per ulteriore assistenza, contattare il [Q&un forum di supporto](https://aka.ms/farmbeatssupport) o inviare un messaggio di posta elettronica all'indirizzo farmbeatssupport@microsoft.com .

> [!NOTE]
  > Se FarmBeats è stato installato nel mese di aprile e i processi non riescono e producono un messaggio di errore vuoto, è possibile che non sia stata allocata alcuna quota batch all'installazione per dare la priorità al supporto di organizzazioni sanitarie e di sicurezza essenziali. Per altre informazioni, fare clic [qui](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) . Per eseguire correttamente i processi, è necessario richiedere l'allocazione delle macchine virtuali all'account Batch.

## <a name="install-issues"></a>Problemi di installazione

  > [!NOTE]
  > Se si sta riavviando l'installazione a causa di un errore, assicurarsi di eliminare il **gruppo di risorse** o di eliminare tutte le risorse dal gruppo di risorse prima di riattivare l'installazione.

### <a name="invalid-sentinel-credentials"></a>Credenziali Sentinel non valide

Le credenziali Sentinel specificate durante l'installazione non sono corrette. Riavviare l'installazione con le credenziali corrette.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>È stata raggiunta la quota di account dell'area per gli account Batch relativi alla sottoscrizione specificata

Aumentare la quota o eliminare gli account Batch non usati e riavviare l'installazione.

### <a name="invalid-resource-group-location"></a>Località del gruppo di risorse non valida

Verificare che il **Gruppo di risorse** si trovi nello stesso percorso dell'**Area** specificata durante l'installazione.

### <a name="other-install-issues"></a>Altri problemi di installazione

Contattare Microsoft con i dettagli seguenti:

- ID sottoscrizione personale
- Nome del gruppo di risorse
- Per allegare il file di log dell'errore di distribuzione, seguire questa procedura:

    1. Passare al **Gruppo di risorse** nel portale di Azure.

    2. Selezionare **Distribuzioni** nella sezione **Impostazioni** sul lato sinistro.

    3. Per ogni distribuzione che visualizza **Non riuscita** esaminare i dettagli e scaricare i dettagli della distribuzione. Allegare questo file al messaggio di posta elettronica.

## <a name="sensor-telemetry"></a>Telemetria del sensore

### <a name="cant-view-telemetry-data"></a>Non è possibile visualizzare i dati di telemetria

**Sintomo**: I dispositivi o i sensori sono stati distribuiti e FarmBeats è stato collegato con il partner per i dispositivi, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**

1. Passare al gruppo di risorse FarmBeats.
2. Selezionare lo spazio dei nomi dell' **Hub eventi** ("Sensor-partner-eh-namespace-xxxx"), fare clic su "hub eventi" e quindi verificare il numero di messaggi in arrivo nell'hub eventi assegnato al partner.
3. Effettuare una delle operazioni seguenti:

   - Se non sono presenti *messaggi in arrivo*, contattare il partner per i dispositivi.  
   - Se sono presenti *messaggi in arrivo*, contattare Microsoft con i log di Datahub e Accelerator e i dati di telemetria acquisiti.

Per informazioni su come scaricare i log, vedere la sezione ["Raccogliere i log manualmente"](#collect-logs-manually).  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Non è possibile visualizzare i dati di telemetria dopo l'inserimento dei dati cronologici/di streaming dai sensori

**Sintomo**: i dispositivi o i sensori sono stati distribuiti e sono stati creati in FarmBeats e i dati di telemetria sono stati inseriti in EventHub, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**

1. Assicurarsi che la registrazione del partner sia stata eseguita correttamente. Per verificarlo passare allo swagger dell'hub di dati, passare all'API Partner, eseguire un'operazione get e verificare se il partner è registrato. Se non è registrato, seguire questa [procedura](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) per aggiungere il partner.

2. Assicurarsi di aver usato il formato di messaggio di telemetria corretto:

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
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Non si ha la stringa di connessione a Hub eventi di Azure

**Azione correttiva**

1. In Datahub Swagger passare all'API Partner.
2. Selezionare **Ottieni** > **Prova** > **Esegui**.

> [!NOTE]
> ID partner del partner per i sensori a cui si è interessati.

3. Tornare all'API partner e selezionare **get/ \<ID> **.
4. Specificare l'ID partner del passaggio 3, quindi selezionare **Esegui**.

   La risposta dell'API includerà la stringa di connessione di Hub eventi.

### <a name="device-appears-offline"></a>Il dispositivo risulta offline

**Sintomi**: i dispositivi sono installati e FarmBeats è stato collegato con il partner per i dispositivi. I dispositivi sono online e inviano dati di telemetria, ma vengono visualizzati come offline.

**Azione correttiva** L'intervallo dei report non è configurato per questo dispositivo. Per impostare l'intervallo di report, contattare il produttore del dispositivo. 

### <a name="error-deleting-a-device"></a>Errore nell'eliminazione di un dispositivo

Durante l'eliminazione di un dispositivo, è possibile che si verifichi uno dei seguenti scenari di errore comuni:  

**Messaggio**: "Device is referenced in sensors: There are one or more sensors associated with the device. Delete the sensors and then delete the device." (Nei sensori è presente un riferimento al dispositivo: uno o più sensori sono associati al dispositivo. Eliminare i sensori e quindi eliminare il dispositivo).  

**Significato**: Il dispositivo è associato a più sensori distribuiti nella farm.

**Azione correttiva**  

1. Eliminare i sensori associati al dispositivo tramite Accelerator.  
2. Per associare i sensori a un dispositivo diverso, rivolgersi al partner produttore del dispositivo.  
3. Eliminare il dispositivo usando una chiamata `DELETE API` e impostare il parametro force su *true*.  

**Messaggio**: "Device is referenced in devices as ParentDeviceId: There are one or more devices that are associated with this device as child devices. Delete them, and then delete this device." (In devices è presente un riferimento al dispositivo come ParentDeviceId: uno o più dispositivi sono associati al dispositivo come dispositivi figlio. Eliminarli, quindi eliminare il dispositivo corrente).  

**Significato**: Al dispositivo sono associati altri dispositivi.  

**Azione correttiva**

1. Eliminare i dispositivi associati a questo dispositivo specifico.  
2. Eliminare il dispositivo specifico.  

    > [!NOTE]
    > Non è possibile eliminare un dispositivo se ha dei sensori associati. Per altre informazioni su come eliminare i sensori associati, vedere la sezione **Elimina il sensore** in [Recuperare dati dai sensori](get-sensor-data-from-sensor-partner.md).
    > I partner non hanno le autorizzazioni per eliminare un dispositivo o un sensore. Solo gli amministratori hanno le autorizzazioni per l'eliminazione.

## <a name="issues-with-jobs"></a>Problemi relativi ai processi

### <a name="farmbeats-internal-error"></a>Errore interno di FarmBeats

**Messaggio**: "FarmBeats internal error, see troubleshooting guide for more details" (Errore interno di FarmBeats, per altri dettagli vedere la guida alla risoluzione dei problemi).

**Azione correttiva** Questo problema potrebbe essere causato da un errore temporaneo nella pipeline di dati. Creare di nuovo il processo. Se l'errore persiste, contattare Microsoft con il messaggio di errore e i log.

## <a name="accelerator-troubleshooting"></a>Risoluzione dei problemi di Accelerator

### <a name="access-control"></a>Controllo di accesso

**Problema**: Viene visualizzato un errore durante l'aggiunta di un'assegnazione di ruolo.

**Messaggio**: "No matching users found" (Nessun utente corrispondente trovato).

**Azione correttiva** Controllare l'ID di posta elettronica per il quale si sta aggiungendo un'assegnazione di ruolo. L'ID di posta elettronica deve corrispondere esattamente all'ID che è stato registrato per l'utente in Active Directory. Se l'errore persiste, contattare Microsoft con il messaggio di errore e i log.

### <a name="unable-to-log-in-to-accelerator"></a>Non è possibile accedere ad Accelerator

**Messaggio**: "Error: You are not authorized to call the service. Contact the administrator for authorization" (Errore: non si è autorizzati a chiamare il servizio. Contattare l'amministratore per richiedere l'autorizzazione).

**Azione correttiva** Chiedere all'amministratore l'autorizzazione per accedere alla distribuzione di FarmBeats. Questa operazione può essere eseguita con un'operazione POST delle API RoleAssignment o tramite Controllo di accesso nel riquadro **Impostazioni** in Accelerator.  

Se è già stato ottenuto l'accesso e si è verificato questo errore, riprovare aggiornando la pagina. Se l'errore persiste, contattare Microsoft con il messaggio di errore e i log.

![Progetto FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemi con Accelerator  

**Problema**: Si è ricevuto un errore di Accelerator con causa non determinata.

**Messaggio**: "Error: An unknown error occurred" (Errore: si è verificato un errore sconosciuto).

**Azione correttiva** Questo errore si verifica se si lascia inattiva la pagina troppo a lungo. Aggiornare la pagina. Se l'errore persiste, contattare Microsoft con il messaggio di errore e i log.

**Problema**: FarmBeats Accelerator non visualizza la versione più recente, anche dopo l'aggiornamento FarmBeatsDeployment.

**Azione correttiva** Questo errore si verifica a causa della persistenza del processo di lavoro dei servizi nel browser. Eseguire le operazioni seguenti:

1. Chiudere tutte le schede del browser in cui è aperto Accelerator e chiudere la finestra del browser.
2. Avviare una nuova istanza del browser e ricaricare l'URI di Accelerator. Questa azione carica la nuova versione di Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problemi relativi alle immagini

### <a name="wrong-username-or-password"></a>Nome utente o password non validi

**Messaggio di errore processo**: "Full authentication is required to access this resource" (Per accedere a questa risorsa è necessaria l'autenticazione completa).

**Azione correttiva**: Eseguire una delle operazioni seguenti:

- Aggiornare FarmBeats con il nome utente e la password corretti usando la procedura seguente, quindi ripetere il processo.

  **Aggiornare il nome utente di Sentinel**

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Datahub.
    3. Selezionare l'archiviazione Account di archiviazione***** > **Contenitori** > **batch-prep-files** > **to_vm** > **config.ini**
    4. Selezionare **Modifica**.
    5. Aggiornare il nome utente nella sezione sentinel_account

  **Aggiornare la password di Sentinel**

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Datahub.
    3. Selezionare keyvault-*****
    4. Selezionare Criteri di accesso in Impostazioni
    5. Selezionare **Aggiungi criterio di accesso**.
    6. Usare **Gestione dei segreti** in Configura dal modello e aggiungere se stessi a Principale
    7. Selezionare **Aggiungi** e quindi selezionare **Salva** nella pagina **Criteri di accesso**
    8. Selezionare **Segreti** in **Impostazioni**
    9. Selezionare **Sentinel-password**
    10. Creare una nuova versione del valore e abilitarla.

- Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi verificare se il processo ha esito positivo.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub Sentinel: URL errato o sito non accessibile

**Messaggio di errore processo**: "Oops, something went wrong. The page you were trying to access is (temporarily) unavailable" (Si è verificato un errore. La pagina a cui si sta provando ad accedere non è attualmente disponibile).

**Azione correttiva**:

1. Aprire [Sentinel](https://scihub.copernicus.eu/dhus/) nel browser per verificare se il sito Web è accessibile.
2. Se il sito Web non è accessibile, verificare se qualsiasi firewall, rete aziendale o altro software di blocco impedisce l'accesso al sito Web, quindi prendere le misure necessarie per autorizzare l'URL di Sentinel. 
3. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi controllare se il processo ha avuto esito positivo.  

### <a name="sentinel-server-down-for-maintenance"></a>Server Sentinel: non disponibile per manutenzione

**Messaggio di errore processo**: "The Copernicus Open Access Hub will be back soon! Sorry for the inconvenience, we're performing some maintenance at the moment. We'll be back online shortly!" (L'hub Copernicus Open Access tornerà disponibile quanto prima. Ci scusiamo per il disagio. Stiamo eseguendo alcune operazioni di manutenzione. Il componente tornerà online a breve). 

**Azione correttiva**:

questo problema può verificarsi quando vengono eseguite attività di manutenzione nel server Sentinel.

1. Se un processo o una pipeline ha esito negativo perché è in corso la manutenzione, inviare di nuovo il processo dopo un certo tempo. 

   Per informazioni su tutte le attività di manutenzione pianificate o non pianificate di Sentinel, visitare il sito [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/).  

2. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi controllare se il processo ha avuto esito positivo.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: numero massimo di connessioni raggiunto

**Messaggio di errore del processo**: "numero massimo di due flussi simultanei ottenibili dall'utente" " \<username> .

**Significato**: Se un processo ha esito negativo perché è stato raggiunto il numero massimo di connessioni, lo stesso account Sentinel viene usato in più processi.

**Azione correttiva**: provare a eseguire una delle operazioni seguenti:

* Attendere il completamento degli altri processi prima di eseguire di nuovo il processo non riuscito.
* Creare un nuovo account Sentinel, quindi aggiornare il nome utente e la password di Sentinel in FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Server Sentinel: connessione rifiutata

**Messaggio di errore processo**: "Server refused connection at: http://172.30.175.69:8983/solr/dhus." (Il server ha rifiutato la connessione in: http://172.30.175.69:8983/solr/dhus ).

**Azione correttiva**: questo problema può verificarsi quando vengono eseguite attività di manutenzione nel server Sentinel.

1. Se un processo o una pipeline ha esito negativo perché è in corso la manutenzione, inviare di nuovo il processo dopo un certo tempo.

   Per informazioni su tutte le attività di manutenzione pianificate o non pianificate di Sentinel, visitare il sito [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/).  

2. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi controllare se il processo ha avuto esito positivo.

### <a name="soil-moisture-map-has-white-areas"></a>La mappa dell'umidità del suolo presenta aree bianche

**Problema**: è stata generata la mappa dell'**umidità del suolo**, ma la mappa è composta principalmente da aree bianche.

**Azione correttiva**: questo problema può verificarsi se gli indici satellite generati per il periodo di tempo per cui è stata richiesta la mappa hanno valori NDVI inferiori a 0,3. Per altre informazioni, vedere la [Guida tecnica di Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Eseguire di nuovo il processo per un intervallo di date diverso e verificare se i valori NDVI negli indici satellite sono maggiori di 0,3.

## <a name="collect-logs-manually"></a>Raccogliere i log manualmente

[Installare e distribuire Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Raccogliere i log dei processi di Azure Data Factory o i log del servizio app in Datahub

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Datahub.
3. Nel dashboard **Gruppo di risorse** cercare l'account di archiviazione *datahublogs\** . Ad esempio *datahublogsmvxmq*.  
4. Nella colonna **Nome** selezionare l'account di archiviazione per visualizzare il dashboard **Account di archiviazione**.
5. Nel riquadro **datahubblogs\*** selezionare **Apri in Explorer** per visualizzare l'applicazione **Apri Azure Storage Explorer**.
6. Nel riquadro sinistro selezionare **Contenitori BLOB**, quindi selezionare **job-logs** per i log di Azure Data Factory o **appinsights-logs** per i log del servizio app.
7. Selezionare **Scarica** e scaricare i log in una cartella locale nel computer.

    ![Progetto FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Raccogliere i log dei processi di Azure Data Factory o i log del servizio app per Accelerator

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Accelerator.
3. Nel dashboard **Gruppo di risorse** cercare l'account di archiviazione *storage\** . Ad esempio, *storagedop4k\** .
4. Nella colonna **Nome** selezionare l'account di archiviazione per visualizzare il dashboard **Account di archiviazione**.
5. Nel riquadro **storage\*** selezionare **Apri in Explorer** per aprire l'applicazione Azure Storage Explorer.
6. Nel riquadro sinistro selezionare **Contenitori BLOB**, quindi selezionare **job-logs** per i log di Azure Data Factory o **appinsights-logs** per i log del servizio app.
7. Selezionare **Scarica** e scaricare i log in una cartella locale nel computer.

## <a name="high-cpu-usage"></a>Uso elevato della CPU

**Errore**: Si riceve un avviso di posta elettronica che fa riferimento a un **avviso di utilizzo della CPU elevato**.

**Azione correttiva**:

1. Passare al gruppo di risorse Datahub FarmBeats.
2. Selezionare il **servizio app**.  
3. Scorrere la [pagina dei prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/) e selezionare un piano tariffario appropriato.

## <a name="weather-data-job-failures"></a>Errori dei processi di dati meteorologici

**Errore**: si eseguono i processi per ottenere i dati meteorologici ma il processo non riesce

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Raccogliere i log per risolvere gli errori relativi ai processi di dati meteorologici

1. Passare al gruppo di risorse FarmBeats nel portale di Azure.
2. Fare clic sul servizio Data Factory che fa parte del gruppo di risorse. Il servizio avrà un tag "SKU: datahub"

> [!NOTE]
> Per visualizzare i tag dei servizi nel gruppo di risorse, fare clic su "modifica colonne" e aggiungere "tag" alla visualizzazione del gruppo di risorse.

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Progetto FarmBeats":::

3. Nella pagina Panoramica della data factory fare clic su **autore e monitoraggio**. Verrà aperta una nuova scheda nel browser. Fare clic su **monitoraggio**

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Progetto FarmBeats":::

4. Verrà visualizzato un elenco di esecuzioni di pipeline che fanno parte dell'esecuzione del processo Meteo. Fare clic sul processo per cui si vogliono raccogliere i log
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Progetto FarmBeats":::

5. Nella pagina Panoramica pipeline verrà visualizzato l'elenco delle esecuzioni di attività. Prendere nota degli ID di esecuzione delle attività per cui si vogliono raccogliere i log
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Progetto FarmBeats":::

6. Tornare al gruppo di risorse FarmBeats in portale di Azure e fare clic sull'account di archiviazione con il nome **datahublogs-xxxx**
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Progetto FarmBeats":::

7. Fare clic su **contenitori**  ->  **adfjobs**. Nella casella di ricerca immettere l'ID di esecuzione del processo annotato nel passaggio 5 riportato sopra.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="Progetto FarmBeats":::

8. Il risultato della ricerca conterrà la cartella che contiene i log relativi al processo. Scaricare i log e inviarli a farmbeatssupport@microsoft.com per assistenza nel debug del problema.
