---
title: Risoluzione dei problemi di Azure FarmBeats
description: In questo articolo viene descritto come risolvere i problemi di Azure FarmBeats.This article describes how to troubleshoot Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 504dfb721c7b87d5497c73851d0694601b253d5c
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529685"
---
# <a name="troubleshoot"></a>Risolvere problemi

Questo articolo fornisce soluzioni ai problemi comuni di Azure FarmBeats.This article provides solutions to common Azure FarmBeats issues. Per ulteriore assistenza, contatta il farmbeatssupport@microsoft.comnostro Forum di [supporto](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) o inviaci un'e-mail all'indirizzo .

## <a name="install-issues"></a>Problemi di installazione

  > [!NOTE]
  > Se si riavvia l'installazione a causa di un errore, assicurarsi di eliminare il gruppo di risorse o tutte le risorse dal gruppo di risorse prima di riattivare l'installazione.

### <a name="invalid-sentinel-credentials"></a>Credenziali Sentinel non valide

Le credenziali Sentinel fornite durante l'installazione non sono corrette. Riavviare l'installazione con le credenziali corrette.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>È stata raggiunta la quota di account regionale degli account batch per la sottoscrizione specificata

Aumentare la quota o eliminare gli account batch inutilizzati e riavviare l'installazione.

### <a name="invalid-resource-group-location"></a>Percorso del gruppo di risorse non valido

Assicurarsi che il gruppo di risorse si trova nello stesso percorso dell'area specificata durante l'installazione.

### <a name="other-install-issues"></a>Altri problemi di installazione

Contattaci con i seguenti dettagli:

- Il tuo ID sottoscrizione
- Nome del gruppo di risorse
- Collegare il file di log per l'errore di distribuzione attenendosi alla procedura seguente:

    1. Passare al gruppo di risorse nel portale di Azure.Navigate to the Resource Group in the Azure portal.
    2. Selezionare Distribuzioni nella sezione Impostazioni sul lato sinistro.
    3. Per ogni distribuzione che mostra "Non riuscita", fare clic su per visualizzare i dettagli e scaricare i dettagli della distribuzione. Allegare il file al messaggio.

## <a name="sensor-telemetry"></a>Telemetria dei sensori

### <a name="cant-view-telemetry-data"></a>Impossibile visualizzare i dati di telemetria

**Sintomo:** i dispositivi o i sensori vengono distribuiti e FarmBeats è stato collegato al partner del dispositivo, ma non è possibile ottenere o visualizzare dati di telemetria in FarmBeats.

**Azione correttiva**:

1. Passare al gruppo di risorse FarmBeats Datahub.Go to your FarmBeats Datahub resource group.
2. Selezionare **l'hub eventi** (DatafeedEventHubNamespace) e quindi verificare il numero di messaggi in arrivo.
3. Effettuare una delle operazioni seguenti:
   - Se non sono presenti *messaggi in arrivo,* contattare il partner del dispositivo.  
   - Se sono presenti *messaggi in arrivo,* contattarci con i log Datahub e Accelerator e i dati di telemetria acquisiti.

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

**Azione correttiva**: Questo problema potrebbe derivare da un errore temporaneo nella pipeline di dati. Creare nuovamente il processo. Se l'errore persiste, contattaci con il messaggio di errore / registri.

## <a name="accelerator-troubleshooting"></a>Risoluzione dei problemi relativi agli acceleratori

### <a name="access-control"></a>Controllo di accesso

**Problema:** viene visualizzato un errore durante l'aggiunta di un'assegnazione di ruolo.

**Messaggio:**"Nessun utente corrispondente trovato".

**Azione correttiva**: Controllare l'ID di posta elettronica per il quale si sta tentando di aggiungere un'assegnazione di ruolo. L'ID di posta elettronica deve essere una corrispondenza esatta dell'ID, che viene registrato per tale utente in Active Directory. Se l'errore persiste, contattaci con il messaggio di errore / registri.

### <a name="unable-to-log-in-to-accelerator"></a>Impossibile accedere all'acceleratore

**Messaggio**: "Errore: Non si è autorizzati a chiamare il servizio. Contattare l'amministratore per l'autorizzazione."

**Azione correttiva**: Chiedere all'amministratore di autorizzare l'utente ad accedere alla distribuzione di FarmBeats. Questa operazione può essere eseguita eseguendo un POST delle API RoleAssignment o tramite il controllo di accesso nel riquadro **Impostazioni** in Accelerator.  

Se hai già ottenuto l'accesso e stai affrontando questo errore, riprova aggiornando la pagina. Se l'errore persiste, contattaci con il messaggio di errore / registri.

![Progetto FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemi dell'acceleratore  

**Problema:** si è verificato un errore di accelerazione di causa indeterminata.

**Messaggio:**"Errore: si è verificato un errore sconosciuto".

**Azione correttiva**: Questo errore si verifica se si lascia la pagina inattiva per troppo tempo. Aggiornare la pagina.  

Se l'errore persiste, contattaci con il messaggio di errore / registri.

**Problema:** FarmBeats Accelerator non mostra la versione più recente, anche dopo aver aggiornato FarmBeatsDeployment.

**Azione correttiva:** questo errore si verifica a causa della persistenza del service worker nel browser. Eseguire le operazioni seguenti:

1. Chiudere tutte le schede del browser con acceleratore aperto e chiudere la finestra del browser.
2. Avviare una nuova istanza del browser e ricaricare l'URI dell'acceleratore. Questa azione carica la nuova versione di Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problemi relativi alle immagini

### <a name="wrong-username-or-password"></a>Nome utente o password errati

**Messaggio di errore del processo:**"È necessaria l'autenticazione completa per accedere a questa risorsa".

**Azione correttiva**:

Eseguire una delle operazioni seguenti:

- Aggiornare FarmBeats con il nome utente/password corretto attenendosi alla procedura riportata di seguito e ripetere il processo.

    *Aggiorna nome utente Sentinel*
    1. Accedere al portale di [Azure](https://portal.azure.com).
    2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Datahub.
    3. Selezionare Archiviazione dell'account di archiviazione, ovvero -> Contenitori-> file di preparazione batch -> to_vm -> config.ini
    4. Fare clic su Edit
    5. Aggiornare il nome utente nella sezione sentinel_account

    *Aggiorna password Sentinel*
    1. Accedere al portale di [Azure](https://portal.azure.com).
    2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Datahub.
    3. Selezionare keyvault
    4. Selezionare Criteri di accesso in Impostazioni
    5. Fare clic su "Aggiungi criteri di accesso"
    6. Utilizzare "Gestione dei segreti" per Configurare da modello e aggiungere se stessi a Principal
    7. Fare clic su Aggiungi e quindi su Salva nella pagina Criteri di accesso
    8. Fare clic su Segreti in Impostazioni
    9. Fare clic su Sentinel-password
    10. Creare una nuova versione del valore e abilitarla

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

**Significato**: Se un processo non riesce perché è stato raggiunto il numero massimo di connessioni, lo stesso account Sentinel viene utilizzato in più processi.

**Azione correttiva**: Provare a eseguire una delle operazioni seguenti:

* Attendere il completamento degli altri processi prima di rieseguire il processo non riuscito.
* Creare un nuovo account Sentinel, quindi aggiornare il nome utente e la password Sentinel in FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Server Sentinel: connessione rifiutata

**Messaggio di errore del processo** http://172.30.175.69:8983/solr/dhus: "Server ha rifiutato la connessione in: ."

**Azione correttiva**: Questo problema può verificarsi se vengono eseguite attività di manutenzione sul server Sentinel.

1. Se un processo o una pipeline non riesce perché viene eseguita la manutenzione, inviare nuovamente il processo dopo un certo periodo di tempo.

   Per informazioni su eventuali attività di manutenzione di Sentinel pianificate o non pianificate, visitare il sito [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Eseguire nuovamente il processo non riuscito o eseguire un processo di indici satellite per un intervallo di date da 5 a 7 giorni, quindi verificare se il processo ha esito positivo.

### <a name="soil-moisture-map-has-white-areas"></a>Soil Mappa dell'umidità ha aree bianche

**Problema**: La mappa dell'umidità del suolo è stata generata, ma la mappa ha per lo più aree bianche.

**Azione correttiva**: Questo problema può verificarsi se gli indici satellite generati per il tempo per cui è stata richiesta la mappa ha valori NDVI inferiori a 0,3. Per ulteriori informazioni, [consultare la Guida tecnica di Sentinel.](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)
1. Eseguire nuovamente il processo per un intervallo di date diverso e verificare se i valori NDVI negli indici satellite sono più di 0,3

## <a name="collect-logs-manually"></a>Raccogliere i registri manualmente

[Installare e distribuire Azure Storage Explorer.]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Raccogliere log dei processi di Azure Data Factory o log del servizio app in DatahubCollect Azure Data Factory job logs or App Service logs in Datahub

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Datahub.
3. Nel dashboard **Del gruppo di risorse** cercare l'account di archiviazione *datahublogs.\* * Ad esempio, *datahublogsmvxmq*.  
4. Nella colonna **Nome** selezionare l'account di archiviazione per visualizzare il dashboard **dell'account di archiviazione.**
5. Nel riquadro datahubblogs selezionare **Apri in Esplora risorse** per visualizzare l'applicazione Apri Esplora archivi di Azure.In the **datahubblogs\* ** pane, select Open in Explorer to view the Open Azure Storage **Explorer** application.
6. Nel riquadro sinistro selezionare **Contenitori BLOB**e quindi selezionare **job-logs** per i log di Azure Data Factory o **appinsights-logs** per i log del servizio app.
7. Selezionare **Scarica** e scaricare i registri in una cartella locale nel computer.

    ![Progetto FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Raccogliere i log dei processi di Azure Data Factory o i log del servizio app per AcceleratorCollect Azure Data Factory job logs or App Service logs for Accelerator

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella **Cerca** cercare il gruppo di risorse FarmBeats Accelerator.
3. Nel dashboard **Gruppo di risorse** cercare l'account di *archiviazione.\* * Ad esempio, *storagedop4k\**.
4. Selezionare l'account di archiviazione nella colonna **Nome** per visualizzare il dashboard **Account di archiviazione.**
5. Nel riquadro di archiviazione selezionare **Apri in Esplora risorse** per aprire l'applicazione Esplora archivi di Azure.In the **storage\* ** pane, select Open in Explorer to open the Azure Storage Explorer application.
6. Nel riquadro sinistro selezionare **Contenitori BLOB**e quindi selezionare **job-logs** per i log di Azure Data Factory o **appinsights-logs** per i log del servizio app.
7. Selezionare **Scarica** e scaricare i registri in una cartella locale nel computer.

## <a name="high-cpu-usage"></a>Uso elevato della CPU

**Errore:** viene visualizzato un avviso tramite posta elettronica che fa riferimento a un avviso di **utilizzo elevato della CPU**.

**Azione correttiva**:

1. Passare al gruppo di risorse FarmBeats Datahub.Go to your FarmBeats Datahub resource group.
2. Selezionare il **servizio app**.  
3. Passare alla [pagina dei prezzi](https://azure.microsoft.com/pricing/details/app-service/windows/)del servizio app con scalabilità verticale e quindi selezionare un piano tariffario appropriato.
