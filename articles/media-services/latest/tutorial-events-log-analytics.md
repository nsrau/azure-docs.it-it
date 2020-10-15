---
title: Archiviare eventi di Servizi multimediali di Azure in Azure Log Analytics
titleSuffix: Azure Media Services
description: Informazioni su come archiviare eventi di Servizi multimediali di Azure in Azure Log Analytics.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: cc3060c9253b23b97089ea35625aceb26737baba
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016996"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Esercitazione: Archiviare eventi di Servizi multimediali di Azure in Azure Log Analytics

## <a name="azure-media-services-events"></a>Eventi di Servizi multimediali di Azure

Servizi multimediali di Azure v3 emette eventi in [Griglia di eventi di Azure](media-services-event-schemas.md). È possibile sottoscrivere eventi in molti modi e archiviarli negli archivi dati. In questa esercitazione verranno sottoscritti eventi di Servizi multimediali mediante un [flusso di app per la logica](https://azure.microsoft.com/services/logic-apps/). L'app per la logica verrà attivata per ogni evento e archivierà il corpo dell'evento in Azure Log Analytics. Quando gli eventi si trovano in Azure Log Analytics, è possibile usare altri servizi di Azure per creare un dashboard, monitorare e inviare un avviso per tali eventi, anche se queste operazioni non verranno illustrate in questa esercitazione.

> [!NOTE]
> Una familiarità con FFmpeg come codificatore locale può risultare utile,  ma non è obbligatoria. La riga di comando e le istruzioni per lo streaming di un video sono incluse più avanti.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un flusso di app per la logica senza codice
> * Sottoscrivere argomenti di eventi di Servizi multimediali di Azure
> * Analizzare eventi e archiviarli in Azure Log Analytics
> * Eseguire query su eventi da Azure Log Analytics

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

> * Una [sottoscrizione di Azure](how-to-set-azure-subscription.md)
> * Un account di [Servizi multimediali](create-account-howto.md) e un gruppo di risorse.
> * Un'installazione di [FFmpeg](https://ffmpeg.org/download.html) per il sistema operativo specifico.
> * Un'area di lavoro di [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md)

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Sottoscrivere un evento di Servizi multimediali con App per la logica

1. Se non è già stato fatto, nel portale di Azure creare un'area di lavoro [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Sarà necessario avere l'ID area di lavoro e una delle chiavi, quindi occorre lasciare aperta la finestra del browser. Aprire quindi il portale in un'altra scheda o finestra.

1. Passare all'account di Servizi multimediali di Azure e selezionare **Eventi**. Verranno visualizzati tutti gli altri metodi per sottoscrivere eventi di Servizi multimediali di Azure.
    > [!div class="mx-imgBorder"]
    > ![Portale di Servizi multimediali di Azure](media/tutorial-events-log-analytics/select-events-01a.png)

1. Selezionare l'**icona di App per la logica** per creare un'app per la logica. Verrà aperta la finestra di progettazione di App per la logica, in cui è possibile creare il flusso per acquisire gli eventi ed eseguirne il push in Log Analytics. 
    > [!div class="mx-imgBorder"]
    > ![Creare un'app per la logica](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Selezionare l'**icona +** , quindi il tenant da usare e infine Accedi. Verrà visualizzato un prompt di accesso Microsoft.
    > [!div class="mx-imgBorder"]
    > ![Connessione a Griglia di eventi di Azure](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
![Selezionare il tenant](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. Selezionare **Continua** per sottoscrivere gli eventi di Servizi multimediali.
    > [!div class="mx-imgBorder"]
    > ![Connessione a Griglia di eventi di Azure completata](media/tutorial-events-log-analytics/select-continue-04.png)

1. Nell'elenco **Tipo di risorsa**, individuare "Microsoft.Media.MediaServices".
    > [!div class="mx-imgBorder"]
    >![Eventi delle risorse di Servizi multimediali di Azure](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Selezionare l'**Event Type item** (Elemento tipo di evento). Verrà visualizzato un elenco di tutti gli eventi emessi da Servizi multimediali di Azure. È possibile selezionare gli eventi di cui tenere traccia. È possibile aggiungere più tipi di evento. In seguito verrà apportata una lieve modifica al flusso di app per la logica per archiviare ogni tipo di evento in un log distinto di Log Analytics e propagare il nome del tipo di evento in modo dinamico nel nome del log di Log Analytics.
    > [!div class="mx-imgBorder"]
    > ![Tipo di evento di Servizi multimediali di Azure](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Selezionare **Salva con nome**.

1. Assegnare un nome all'app per la logica.  Il gruppo di risorse è selezionato per impostazione predefinita. Lasciare invariate le altre impostazioni, quindi selezionare **Crea**.  Verrà visualizzata di nuovo la schermata iniziale di Azure.
    > [!div class="mx-imgBorder"]
    > ![Interfaccia di denominazione di App per la logica](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Creare un'azione

Dopo avere completato la sottoscrizione a uno o più eventi, creare un'azione.

1. Se il portale ha visualizzato di nuovo la schermata iniziale, tornare all'app per la logica appena creata cercando il nome dell'app in Tutte le risorse.

1. Selezionare l'app, quindi selezionare **Progettazione app per la logica**. Verrà visualizzato il riquadro della finestra di progettazione.

1. Selezionare **+ Nuovo passaggio**.

1. Poiché si vuole eseguire il push degli eventi nel servizio Azure Log Analytics, cercare "Azure Log Analytics" e selezionare "Azure Log Analytics Data Collector".
    > [!div class="mx-imgBorder"]
    > ![Agente di raccolta dati di Azure Log Analytics](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Per connettersi all'area di lavoro Log Analytics, è necessario avere un ID area di lavoro e una chiave agente. Aprire il portale di Azure in una nuova scheda o finestra, passare all'area di lavoro Log Analytics creata prima dell'inizio dell'esercitazione.
    > [!div class="mx-imgBorder"]
    > ![ID area di lavoro di Azure Log Analytics](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. Dal menu a sinistra individuare **Gestione agenti** e selezionare questa opzione. Verranno visualizzate le chiavi agente generate.
    > [!div class="mx-imgBorder"]
    > ![Gestione agenti di Azure Log Analytics](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Copiare l'*ID area di lavoro*.
    > [!div class="mx-imgBorder"]
    > ![Copiare l'ID area di lavoro](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. Nell'altra scheda o finestra del browser in Azure Log Analytics Data Collector selezionare **Invia dati**, assegnare un nome alla connessione, quindi incollare l'*ID area di lavoro* nel campo **ID area di lavoro**.

1. Tornare alla scheda o alla finestra del browser dell'area di lavoro e copiare la *Chiave dell'area di lavoro*.
    > [!div class="mx-imgBorder"]
    > ![Chiave primaria di Gestione agenti](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. Nell'altra scheda o finestra del browser incollare la *Chiave dell'area di lavoro* nel campo **Chiave dell'area di lavoro**.

1. Selezionare **Crea**. Si passerà ora alla creazione del corpo della richiesta JSON e del nome del log personalizzato.

1. Selezionare il campo **JSON Request body** (Corpo della richiesta JSON).  Verrà visualizzato un collegamento per **Aggiungi contenuto dinamico**.

1. Selezionare **Aggiungi contenuto dinamico** e quindi **Argomento**.

1. Seguire la stessa procedura per **Nome log personalizzato**.
    > [!div class="mx-imgBorder"]
    > ![Argomento selezionato](media/tutorial-events-log-analytics/topic-selected.png)

1. Selezionare **Visualizzazione codice** dell'app per la logica. Cercare le righe Inputs e Log-Type.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione codice di due righe](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Modificare il valore `body` da `"@triggerBody()?['topic']"` a `"@{triggerBody()}"`. Questa modifica consente di analizzare l'intero messaggio per Log Analytics.

1. Modificare il valore `Log-Type` da `"@triggerBody()?['topic']"` a `"@replace(triggerBody()?['eventType'],'.','')"`. Verranno sostituiti i caratteri "." perché non sono consentiti nei nomi dei log di Log Analytics.
    > [!div class="mx-imgBorder"]
    > ![Codice JSON di App per la logica dopo la modifica](media/tutorial-events-log-analytics/changed-lines.png)

1. Selezionare **Salva**.

1. Per eseguire la verifica, selezionare **Progettazione app per la logica**.
    > [!div class="mx-imgBorder"]
    > ![Passaggi di verifica del corpo e della funzione](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Quando si esaminano tutte le risorse nel gruppo di risorse, verranno elencati un'app per la logica e due connettori API per app per la logica, uno per gli eventi e uno per Log Analytics. Per altre informazioni su argomenti del sistema di Griglia di eventi, leggere [Argomenti di sistema di Griglia di eventi](../../event-grid/system-topics.md).
    > [!div class="mx-imgBorder"]
    > ![Visualizzare tutte le nuove risorse in un gruppo di risorse](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Test

Per testare il funzionamento effettivo, creare un evento live in Servizi multimediali di Azure. Creare un evento live RTMP e usare ffmpeg per eseguire il push di un flusso "live" basato su un file MP4 di esempio. Dopo la creazione dell'evento, ottenere l'URL di inserimento RTMP.

1. Dall'account di Servizi multimediali selezionare **Streaming live**.
    > [!div class="mx-imgBorder"]
    > ![Creare un evento live di Servizi multimediali di Azure](media/tutorial-events-log-analytics/live-event.png)

1. Selezionare **Aggiungi evento live**.

1. Immettere un nome nel campo **Nome evento live**. Il campo **Descrizione** è facoltativo.

1. Selezionare la codifica cloud **Standard**.

1. Selezionare **720p (valore predefinito)** per l'impostazione predefinita per la codifica.

1. Selezionare **RTMP** per il protocollo di input.

1. Selezionare **Sì** per l'URL di input permanente.

1. Selezionare **Sì** per avviare l'evento quando viene creato l'evento.

    > [!WARNING]
    > Se si seleziona Sì, verrà avviata la fatturazione.  Se si vuole attendere prima di avviare il flusso fino a *immediatamente prima* dell'avvio dello streaming con FFmpeg, selezionare **No** e ricordare di avviare l'evento live in quel momento specifico.

    > [!div class="mx-imgBorder"]
    > ![Impostazioni dell'evento live](media/tutorial-events-log-analytics/live-event-settings.png)

1. Selezionare la casella di controllo **I have all the rights to use the content/file...** (Sono disponibili tutti i diritti necessari per l'uso del contenuto/file).

1. Selezionare **Rivedi e crea**.

1. Rivedere le impostazioni e quindi selezionare **Crea**.  Verrà visualizzato l'elenco degli eventi live e verrà mostrato l'URL di inserimento dell'evento live.

1. Copiare l'**URL di inserimento** negli Appunti.

1. Selezionare l'**evento live** nell'elenco per passare alla Visualizzazione producer.

### <a name="stream-with-ffmpeg-cli"></a>Eseguire lo streaming con l'interfaccia della riga di comando di FFmpeg

1. Usare la riga di comando seguente.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Modificare `<ingestURL>` specificando l'URL di inserimento copiato negli Appunti.
1. Modificare `<localpathtovideo>` specificando il percorso locale del file di cui si vuole eseguire lo streaming da FFmpeg.
1. Aggiungere un nome univoco alla fine, ad esempio `mystream`.
1. Modificare la riga di comando per rispecchiare il file di origine del test ed eventuali altre variabili di sistema.
1. Eseguire il comando. Dopo qualche secondo, il lettore della "Visualizzazione producer" dovrebbe avviare lo streaming. Aggiornare il lettore se il video non viene visualizzato automaticamente.

    > [!div class="mx-imgBorder"]
    > ![Verificare che sia stato inserito il video appropriato nel lettore dell'anteprima di producer](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Verificare gli eventi

Grazie allo streaming live, Servizi multimediali di Azure emette diversi eventi che attivano il flusso di app per la logica. Per eseguire una verifica, passare all'app per la logica e determinare se eventuali altri trigger vengono attivati dagli eventi da Servizi multimediali.

1. Passare alla pagina di panoramica delle app per la logica. Dovrebbe essere visualizzata la pagina "Cronologia esecuzioni" che elenca i processi completati correttamente.
    > [!div class="mx-imgBorder"]
    > ![Verificare l'esecuzione corretta dei processi in App per la logica](media/tutorial-events-log-analytics/run-history.png)

1. Selezionare un processo riuscito. Vengono visualizzati i dettagli del processo durante la fase di esecuzione.
1. Selezionare **Invia dati** ed espandere la finestra. In questo caso l'evento `MicrosoftMediaLiveEventEncoderConnected` mostra la conferma dell'acquisizione e viene riportato il corpo analizzato. Questi sono gli elementi di cui viene eseguito il push nell'area di lavoro Azure Log Analytics.
    > [!div class="mx-imgBorder"]
    > ![Visualizzare l'opzione Invia dati](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>Verificare i log

1. Passare all'area di lavoro Log Analytics creata in precedenza.

1. Selezionare **Log**.
1. Chiudere il popup Query di esempio.
1. Verrà visualizzato un elenco Log personalizzati. Selezionare la freccia GIÙ per espandere l'elenco. Verrà visualizzato il nome dell'evento `MicrosoftMediaLiveEventEncoderConnected`.
1. Selezionare il nome dell'evento per espanderlo.
1. Quando si seleziona l'icona a forma di "occhio", verrà visualizzata un'anteprima del risultato della query.
1. Selezionare **Visualizza nell'editor di query** e quindi selezionare l'elemento nell'elenco **TimeGenerated UTC** per espanderlo e visualizzare i dati non elaborati.

![Visualizzare l'output dettagliato per gli eventi in Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Eliminare le risorse

Se non si vuole continuare a usare le risorse create durante questa esercitazione, assicurarsi di eliminare tutte le risorse nel gruppo di risorse. In caso contrario, continueranno a essere applicati addebiti.

## <a name="next-steps"></a>Passaggi successivi

È possibile creare query diverse e salvarle. Le query possono essere aggiunte a [Dashboard Azure](../../azure-monitor/learn/tutorial-logs-dashboards.md).