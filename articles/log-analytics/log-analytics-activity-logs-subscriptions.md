---
title: "Raccogliere log attività di Azure in Log Analytics tra sottoscrizioni | Microsoft Docs"
description: "Usare hub eventi e app per la logica per raccogliere dati dal log attività di Azure ed eseguirne l'invio a un'area di lavoro di Azure Log Analytics in un altro tenant."
services: log-analytics, logic-apps, event-hubs
documentationcenter: 
author: richrundmsft
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: richrund; bwren
ms.openlocfilehash: ded0b4cdcbac747d52435023a24b5719f3c58758
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="collect-azure-activity-logs-into-log-analytics-across-subscriptions"></a>Raccogliere log attività di Azure in Log Analytics tra sottoscrizioni

Questo articolo descrive un metodo per raccogliere i log attività di Azure in un'area di lavoro di Azure Log Analytics tramite il connettore Agente di raccolta dati di Log Analytics per app per la logica. Usare il processo in questo articolo quando è necessario inviare i log a un'area di lavoro in un'altra istanza di Azure Active Directory. Ad esempio, è possibile che i provider di servizi gestiti intendano raccogliere log attività da una sottoscrizione del cliente e archiviarli in un'area di lavoro di Log Analytics nella propria sottoscrizione.

Se l'area di lavoro di Log Analytics si trova nella stessa sottoscrizione di Azure o in una sottoscrizione diversa ma nella stessa istanza di Azure Active Directory, attenersi ai passaggi nella [soluzione per i log attività di Azure](../log-analytics/log-analytics-activity.md) per raccogliere i log attività di Azure.

## <a name="overview"></a>Panoramica

In base alla strategia usata in questo scenario, il log attività di Azure invia eventi a un [hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md), quindi l'[app per la logica](../logic-apps/logic-apps-overview.md) li invia all'area di lavoro di Log Analytics. 

![immagine del flusso di dati dal log attività a log analytics](media/log-analytics-activity-logs-subscriptions/data-flow-overview.png)

I vantaggi di questo approccio includono:
- Bassa latenza, in quanto il log attività di Azure viene inviato in flusso nell'hub eventi.  L'app per la logica viene quindi attivata e invia i dati a Log Analytics. 
- Quantità minima di codice richiesta e nessuna infrastruttura server da distribuire.

Questo articolo illustra come:
1. Creare un hub eventi. 
2. Esportare i log attività in un hub eventi usando il profilo di esportazione del log attività di Azure.
3. Creare un'app per la logica per eseguire la lettura dall'hub eventi e inviare eventi a Log Analytics.

## <a name="requirements"></a>Requisiti
Di seguito sono riportati i requisiti per le risorse di Azure usate in questo scenario.

- Lo spazio dei nomi dell'hub eventi non deve essere nella stessa sottoscrizione dei log di emissione della sottoscrizione. L'utente che configura l'impostazione deve disporre delle autorizzazioni di accesso appropriate per entrambe le sottoscrizioni. Se si dispone di più sottoscrizioni nella stessa istanza di Azure Active Directory, è possibile inviare i log attività per tutte le sottoscrizioni a un singolo hub eventi.
- L'app per la logica può trovarsi in una sottoscrizione diversa rispetto all'hub eventi e non deve trovarsi nella stessa istanza di Azure Active Directory. L'app per la logica esegue la lettura dall'hub eventi usando la stessa chiave di accesso condiviso dell'hub eventi.
- L'area di lavoro di Log Analytics può trovarsi in una sottoscrizione e un'istanza di Azure Active Directory diverse rispetto all'app per la logica, ma è consigliabile la presenza nella stessa sottoscrizione, per semplicità. L'app per la logica esegue l'invio a Log Analytics tramite la chiave e l'ID dell'area di lavoro di Log Analytics.



## <a name="step-1---create-an-event-hub"></a>Passaggio 1: Creare un hub eventi

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Nel portale di Azure selezionare **Nuovo**> **Internet delle cose** > **Hub eventi**.

   ![Nuovo hub eventi nel marketplace](media/log-analytics-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. In **Crea spazio dei nomi** immettere un nuovo spazio dei nomi o selezionarne uno esistente. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.

   ![immagine della finestra di dialogo di creazione dell'hub eventi](media/log-analytics-activity-logs-subscriptions/create-event-hub1.png)

4. Scegliere il piano tariffario (Standard o Basic), una sottoscrizione di Azure, un gruppo di risorse e una posizione per la nuova risorsa.  Fare clic su **Crea** per creare lo spazio dei nomi. Per il provisioning completo delle risorse da parte del sistema, potrebbero essere necessari alcuni minuti.
6. Fare clic sullo spazio dei nomi appena creato nell'elenco visualizzato.
7. Selezionare **Criteri di accesso condiviso** e quindi **RootManageSharedAccessKey**.

   ![immagine dei criteri di accesso condiviso dell'hub eventi](media/log-analytics-activity-logs-subscriptions/create-event-hub7.png)
   
8. Fare clic sul pulsante di copia per copiare la stringa di connessione **RootManageSharedAccessKey** negli Appunti. 

   ![immagine della chiave di accesso condiviso dell'hub eventi](media/log-analytics-activity-logs-subscriptions/create-event-hub8.png)

9. In una posizione temporanea, ad esempio in Blocco note, conservare una copia del nome dell'hub eventi e la stringa di connessione primaria o secondaria dell'hub eventi. Questi valori sono necessari per l'app per la logica.  È possibile usare **RootManageSharedAccessKey** come stringa di connessione dell'hub eventi o crearne una distinta.  La stringa di connessione usata deve iniziare con `Endpoint=sb://` ed essere adatta a criteri con **Gestisci** come criteri di accesso.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Passaggio 2: Esportare log di attività nell'hub eventi

Per abilitare il flusso del log attività, scegliere uno spazio dei nomi dell'hub eventi e criteri di accesso condiviso per tale spazio dei nomi. Un hub eventi viene creato nello spazio dei nomi selezionato quando si verifica il primo evento del log attività. 

È possibile usare uno spazio dei nomi dell'hub eventi che non si trova nella stessa sottoscrizione dei log di emissione della sottoscrizione, tuttavia le sottoscrizioni devono trovarsi nella stessa istanza di Azure Active Directory. L'utente che configura l'impostazione deve disporre del controllo degli accessi in base al ruolo appropriato per accedere a entrambe le sottoscrizioni. 

1. Nel portale di Azure selezionare **Monitoraggio** > **Log attività**.
3. Fare clic sul pulsante **Esporta** nella parte superiore della pagina.

   ![immagine della finestra di monitoraggio di Azure nel riquadro di spostamento](media/log-analytics-activity-logs-subscriptions/activity-log-blade.png)

4. Selezionare la **sottoscrizione** da cui eseguire l'esportazione e quindi fare clic su **Seleziona tutto** nell'elenco a discesa **Aree** per selezionare gli eventi per le risorse in tutte le aree. Fare clic sulla casella di controllo **Esporta in un hub eventi**.
7. Fare clic su **Spazio dei nomi del bus di servizio** e quindi selezionare la **sottoscrizione** con l'hub eventi, lo **spazio dei nomi dell'hub eventi** e un **nome criteri dell'hub eventi**.

    ![immagine della pagina di esportazione nell'hub eventi](media/log-analytics-activity-logs-subscriptions/export-activity-log2.png)

11. Fare clic su **OK** e quindi su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Passaggio 3: Creare l'app per la logica

Durante la scrittura dei log attività nell'hub eventi, creare un'app per la logica per raccogliere i log dall'hub eventi e scriverli in Log Analytics.

L'app per la logica include gli elementi seguenti:
- Un trigger del [connettore dell'hub eventi](https://docs.microsoft.com/connectors/eventhubs/) per la lettura dall'hub eventi.
- Un'[azione Analizza JSON](../logic-apps/logic-apps-content-type.md) per estrarre gli eventi JSON.
- Un'[azione di composizione](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) (Compose) per convertire la stringa JSON in un oggetto.
- Un [connettore di invio dei dati a Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) per inviare i dati a Log Analytics.

   ![immagine dell'aggiunta del trigger dell'hub eventi nelle app per la logica](media/log-analytics-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Requisiti dell'app per la logica
Prima di creare l'app per la logica, verificare di disporre delle informazioni seguenti relative ai passaggi precedenti:
- Nome dell'hub eventi
- Stringa di connessione (primaria o secondaria) dell'hub eventi per lo spazio dei nomi dell'hub eventi
- ID dell'area di lavoro di Log Analytics
- Chiave condivisa di Log Analytics

Per ottenere la stringa di connessione e il nome dell'hub eventi, seguire i passaggi in [Controllare le autorizzazioni dello spazio dei nomi di Hub eventi e trovare la stringa di connessione](../connectors/connectors-create-api-azure-event-hubs.md#check-event-hubs-namespace-permissions-and-find-the-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Creare una nuova app per la logica vuota

1. Nel portale di Azure scegliere **Crea una risorsa** > **Integrazione aziendale** > **App per la logica**.

    ![Nuova app per la logica nel marketplace](media/log-analytics-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Immettere le impostazioni nella tabella seguente.

    ![Creare l'app per la logica](media/log-analytics-activity-logs-subscriptions/create-logic-app.png)

   |Impostazione | DESCRIZIONE  |
   |:---|:---|
   | NOME           | Nome univoco per l'app per la logica. |
   | Sottoscrizione   | Selezionare la sottoscrizione di Azure che conterrà l'app per la logica. |
   | Gruppo di risorse | Selezionare un gruppo di risorse di Azure esistente o crearne uno nuovo per l'app per la logica. |
   | Località       | Selezionare l'area del data center per la distribuzione dell'app per la logica. |
   | Log Analytics  | Selezionare se si desidera registrare lo stato di ogni esecuzione dell'app per la logica in Log Analytics.  |

    
3. Selezionare **Create**. Quando viene visualizzata la notifica **Distribuzione riuscita**, fare clic su **Vai alla risorsa** per aprire l'app per la logica.

4. In **Modelli** scegliere **App per la logica vuota**. 

In Progettazione app per la logica vengono ora mostrati i connettori disponibili e i relativi trigger, che è possibile usare per avviare il flusso di lavoro dell'app per la logica.

<!-- Learn [how to create a logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Aggiungere il trigger dell'hub eventi

1. Nella casella di ricerca di Progettazione app per la logica digitare*hub eventi* come filtro. Selezionare il trigger **Event Hubs - When events are available in Event Hub** (Hub eventi - Quando sono disponibili eventi nell'hub eventi).

   ![immagine dell'aggiunta del trigger dell'hub eventi nelle app per la logica](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Quando viene richiesto di immettere le credenziali, connettersi allo spazio dei nomi dell'hub eventi. Immettere un nome per la connessione e quindi la stringa di connessione copiata.  Selezionare **Create**.

   ![immagine dell'aggiunta della connessione dell'hub eventi nelle app per la logica](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Dopo aver creato la connessione, modificare le impostazioni per il trigger. Iniziare selezionando **insights-operational-logs** nell'elenco a discesa **Nome hub eventi**.

   ![Finestra di dialogo When events are available (Quando sono disponibili eventi)](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Espandere **Mostra opzioni avanzate** e modificare **Tipo di contenuto** in *application/json*

   ![Finestra di dialogo di configurazione dell'hub eventi](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Aggiungere l'azione Analizza JSON

L'output dell'hub eventi contiene un payload JSON con una matrice di record. L'azione [Analizza JSON](../logic-apps/logic-apps-content-type.md) consente solo di estrarre la matrice di record per l'invio a Log Analytics.

1. Fare clic su **Nuovo passaggio** > **Aggiungi un'azione**.
2. Nella casella di ricerca digitare *analizza json* come filtro. Selezionare l'azione **Operazioni dati - Analizza JSON**.

   ![Aggiunta dell'azione di analisi json nelle app per la logica](media/log-analytics-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Fare clic sul campo **Contenuto** e quindi selezionare *Corpo* (Body).

4. Copiare e incollare lo schema seguente nel campo **Schema**.  Questo schema corrisponde all'output dell'azione dell'hub eventi.  

   ![Finestra di dialogo Analizza JSON](media/log-analytics-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> È possibile ottenere un payload di esempio facendo clic su **Esegui** ed esaminando l'**output non elaborato** dell'hub eventi.  È quindi possibile usare questo output con **Usare il payload di esempio per generare lo schema** nell'attività **Analizza JSON** per generare lo schema.

### <a name="add-compose-action"></a>Aggiungere l'azione Componi
L'azione [Componi](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) accetta l'output JSON e crea un oggetto che può essere usato dall'azione di Log Analytics.

1. Fare clic su **Nuovo passaggio** > **Aggiungi un'azione**.
2. Digitare *componi* come filtro e quindi selezionare l'azione **Operazioni dati - Componi**.

    ![Aggiungere l'azione Componi](media/log-analytics-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Fare clic sul campo **Input** e quindi selezionare **Corpo** (Body) nell'attività **Analizza JSON**.


### <a name="add-log-analytics-send-data-action"></a>Aggiungere l'azione di invio dati di Log Analytics
L'azione dell'[agente di raccolta dati di Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) accetta l'oggetto dall'azione Componi e lo invia a Log Analytics.

1. Fare clic su **Nuovo passaggio** > **Aggiungi un'azione**.
2. Digitare *log analytics* come filtro e quindi selezionare l'azione **Azure Log Analytics Data Collector - Send Data** (Agente di raccolta dati di Azure Log Analytics - Invia dati).

   ![Aggiunta dell'azione di invio dati di Log Analytics nelle app per la logica](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Immettere un nome per la connessione e incollare **ID area di lavoro** e **Chiave dell'area di lavoro** dell'area di lavoro di Log Analytics.  Fare clic su **Crea**.

   ![Aggiunta della connessione di Log Analytics alle app per la logica](media/log-analytics-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Dopo aver creato la connessione, modificare le impostazioni nella tabella seguente. 

    ![Azione di configurazione dell'invio dei dati](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Impostazione        | Valore           | DESCRIZIONE  |
   |---------------|---------------------------|--------------|
   |JSON Request body (Corpo della richiesta JSON)  | **Output** dell'azione **Componi** | Recupera i record dal corpo dell'azione Componi. |
   | Nome log personalizzato | AzureActivity | Nome della tabella log personalizzata da creare in Log Analytics per contenere i dati importati. |
   | Time-generated-field (campo generato in base a time) | time | Non selezionare il campo JSON per **time**: digitare solo la parola time. Se si seleziona il campo JSON, la finestra di progettazione inserisce l'azione **Invia dati** in un ciclo *For Each*, che non è ciò che si vuole. |




10. Fare clic su **Salva** per salvare le modifiche apportate all'app per la logica.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Passaggio 4: Testare l'app per la logica e risolverne i problemi
Quando il flusso di lavoro è completato, è possibile eseguire test nella finestra di progettazione per verificare l'assenza di errori di funzionamento.

In Progettazione app per la logica fare clic su **Esegui** per testare l'app per la logica. Per ogni passaggio nell'app per la logica viene visualizzata un'icona di stato, con un segno di spunta bianco in un cerchio verde che indica l'esito positivo.

   ![Test dell'app per la logica](media/log-analytics-activity-logs-subscriptions/test-logic-app.png)

Per informazioni dettagliate su ogni passaggio, fare clic sul nome del passaggio per espanderlo. Fare clic su **Mostra input non elaborati** e **Mostra output non elaborati** per visualizzare altre informazioni sui dati ricevuti e inviati a ogni passaggio.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Passaggio 5: Visualizzare il log attività di Azure in Log Analytics
Il passaggio finale consiste per controllare l'area di lavoro di Log Analytics per assicurarsi che i dati vengano raccolti come previsto.

1. Nel portale di Azure selezionare **Log Analytics**.
2. Selezionare l'area di lavoro e quindi il riquadro **Ricerca log**.
3. Nella barra delle query di ricerca digitare `AzureActivity_CL` e fare clic sul pulsante di ricerca. Se il log personalizzato non è stato denominato *AzureActivity*, digitare il nome scelto e aggiungere `_CL`.

>[!NOTE]
> Quando un nuovo log personalizzato viene inviato per la prima volta a Log Analytics, potrebbe essere necessario attendere fino a un'ora affinché diventi disponibile per la ricerca.

>[!NOTE]
> I log attività vengono scritti in una tabella personalizzata e non vengono mostrati nella [soluzione per i log attività](./log-analytics-activity.md).


![Test dell'app per la logica](media/log-analytics-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata un'app per la logica per leggere i log attività di Azure da un hub eventi e inviarli a Log Analytics per l'analisi. Per altre informazioni sulla visualizzazione dei dati in Log Analytics, inclusa la creazione di dashboard, esaminare l'esercitazione per la visualizzazione dei dati.

> [!div class="nextstepaction"]
> [Esercitazione per la visualizzazione dei dati di ricerca log](./log-analytics-tutorial-dashboards.md)
