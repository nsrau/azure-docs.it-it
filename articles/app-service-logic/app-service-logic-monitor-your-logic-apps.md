<properties 
	pageTitle="Monitorare le app per la logica nel servizio app di Azure | Microsoft Azure" 
	description="Come verificare il funzionamento delle app per la logica" 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="jehollan"/>

# Monitorare le app per la logica

Dopo aver [creato un'app per la logica](app-service-logic-create-a-logic-app.md), è possibile visualizzare l'intera cronologia della relativa esecuzione nel portale di Azure. È anche possibile configurare servizi come Diagnostica di Azure e gli avvisi di Azure per monitorare gli eventi in tempo reale e ricevere avvisi per eventi come, ad esempio, 5 esecuzioni non riuscite in un'ora.

## Eseguire il monitoraggio nel portale di Azure

Per visualizzare la cronologia, selezionare **Esplora** e quindi **App per la logica**. Viene visualizzato un elenco di tutte le app per la logica incluse nella sottoscrizione. Selezionare l'app per la logica che si vuole monitorare. Verrà visualizzato un elenco di tutte le azioni e tutti i trigger che si sono verificati per tale app per la logica.

![Overview](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Il pannello include alcune sezioni utili:

- **Riepilogo** contiene **Tutte le esecuzioni** e **Cronologia trigger**
	- In **Tutte le esecuzioni** sono elencate le esecuzioni più recenti dell'app per la logica. È possibile fare clic su qualsiasi riga per visualizzare i dettagli dell'esecuzione oppure sul riquadro per estendere l'elenco ad altre esecuzioni.
	- In **Cronologia trigger** sono riportate tutte le attività dei trigger per l'app per la logica. Lo stato delle attività potrebbe essere "Ignorato" per una verifica della presenza di nuovi dati (ad esempio dell'eventuale aggiunta su FTP di un nuovo file), "Operazione riuscita" in relazione alla restituzione di dati per l'attivazione di un'app per la logica o "Non riuscito" per un errore nella configurazione.
- **Diagnostica** consente di visualizzare dettagli ed eventi della fase di esecuzione e di sottoscrivere [avvisi di Azure](#adding-azure-alerts)

>[AZURE.NOTE] Tutti i dettagli ed eventi di runtime vengono crittografati in fase inattiva all'interno del servizio app per la logica. Vengono decrittografati solo in caso di richiesta di visualizzazione da parte di un utente. L'accesso a questi eventi può essere anche controllato dal Controllo di accesso in base al ruolo (RBAC) di Azure.

### Visualizzare i dettagli delle esecuzioni

L'elenco delle esecuzioni mostra **Stato**, **Ora di inizio** e **Durata** delle specifica esecuzione. Selezionare una riga qualsiasi per visualizzare informazioni dettagliate relative a tale esecuzione.

La visualizzazione di monitoraggio mostra ogni passaggio dell'esecuzione, gli input, gli output e i messaggi di errore eventualmente generati.

![Esecuzione e azioni](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

Se sono necessari dettagli aggiuntivi come l'**ID correlazione**, che può essere usato per l'API REST, è possibile fare clic sul pulsante **Dettagli esecuzione**. I dettagli includono tutti i passaggi, lo stato e gli input/output dell'esecuzione.

## Diagnostica e avvisi di Azure

In aggiunta ai dettagli descritti sopra forniti dal portale di Azure e dall'API REST, è possibile configurare l'app per la logica in modo da usare Diagnostica di Azure per ottenere dettagli più completi e il debug.

1. Fare clic sulla sezione **Diagnostica** del pannello dell'app per la logica
1. Fare clic per configurare **Impostazioni di diagnostica**
1. Configurare un hub eventi o un account di archiviazione a cui inviare i dati

	![Impostazioni di diagnostica di Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### Aggiunta di avvisi di Azure

Dopo aver configurato la diagnostica, è possibile aggiungere avvisi di Azure da attivare quando vengono superate determinate soglie. Nel pannello **Diagnostica** selezionare il riquadro **Avvisi** e quindi **Aggiungi avviso**. È così possibile configurare un avviso in base a diverse soglie e metriche.

![Metriche per gli avvisi di Azure](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

È possibile configurare **Condizione**, **Soglia** e **Periodo** in base alle esigenze. Si può infine configurare un indirizzo di posta elettronica a cui inviare una notifica oppure un webhook. È possibile usare il [trigger di richiesta](../connectors/connectors-native-reqres.md) di un'app per la logica in modo che venga eseguito anche in un avviso, ad esempio per la [pubblicazione su un canale slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), l'[invio di un SMS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) o l'[aggiunta di un messaggio a una coda](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

### Impostazioni di diagnostica di Azure

Ogni evento contiene dettagli relativi all'app per la logica e all'evento, come lo stato. Di seguito è riportato un esempio di un evento *ActionCompleted*:

```javascript
{
			"time": "2016-07-09T17:09:54.4773148Z",
			"workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
			"resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
			"category": "WorkflowRuntime",
			"level": "Information",
			"operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
			"properties": {
				"$schema": "2016-06-01",
				"startTime": "2016-07-09T17:09:53.4336305Z",
				"endTime": "2016-07-09T17:09:53.5430281Z",
				"status": "Succeeded",
				"code": "OK",
				"resource": {
					"subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
					"resourceGroupName": "MyResourceGroup",
					"workflowId": "cff00d5458f944d5a766f2f9ad142553",
					"workflowName": "MyLogicApp",
					"runId": "08587361146922712057",
					"location": "eastus",
					"actionName": "Http"
				},
				"correlation": {
					"actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
					"clientTrackingId": "my-custom-tracking-id"
				},
				"trackedProperties": {
					"myProperty": "<value>"
				}
			}
		}
```

Le due proprietà particolarmente utili per il rilevamento e il monitoraggio sono *clientTrackingId* e *trackedProperties*.

#### ID rilevamento client

L'ID rilevamento client è un valore che correla gli eventi nell'esecuzione di un'app per la logica, includendo gli eventuali flussi di lavoro annidati chiamati nell'ambito nell'app. Se non viene fornito, questo ID verrà generato automaticamente. È tuttavia possibile specificare manualmente l'ID rilevamento client da un trigger passando un'intestazione `x-ms-client-tracking-id` con il valore dell'ID nella richiesta del trigger (trigger HTTP, di webhook o di richiesta).

#### Proprietà rilevate

Le proprietà rilevate possono essere aggiunte alle azioni nella definizione del flusso di lavoro per rilevare gli input o gli output nei dati di diagnostica. Ciò può risultare utile se si vogliono rilevare dati come un "ID ordine" nella telemetria. Per aggiungere una proprietà rilevata, includere la proprietà `trackedProperties` in un'azione. Le proprietà rilevate possono tenere traccia solo di singoli input e output di azioni, ma è possibile usare le proprietà `correlation` degli eventi per correlare più azioni in un'esecuzione.

```javascript
{
	"myAction": {
		"type": "http",
		"inputs": {
			"uri": "http://uri",
			"headers": {
				"Content-Type": "application/json"
			},
			"body": "@triggerBody()"
		},
		"trackedProperties":{
			"myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
			"myActionHTTPValue": "@action()['outputs']['body']['foo']",
			"transactionId": "@action()['inputs']['body']['bar']"
		}
	}
}
```

### Estensione della soluzione

I dati di telemetria di Hub eventi o Archiviazione possono essere sfruttati in altri servizi come [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) e [Power BI](https://powerbi.com) per ottenere il monitoraggio in tempo reale dei flussi di lavoro di integrazione.

## Passaggi successivi
- [Esempi e scenari comuni per le app per la logica](app-service-logic-examples-and-scenarios.md)
- [Creazione di un modello di distribuzione di app per la logica](app-service-logic-create-deploy-template.md)
- [Funzionalità di Enterprise Integration](app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0928_2016-->