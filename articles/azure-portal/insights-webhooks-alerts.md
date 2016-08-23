<properties
	pageTitle="Come configurare gli avvisi di Azure per l'invio ad altri sistemi | Microsoft Azure"
	description="Reindirizzare gli avvisi di Azure ad altri sistemi non Azure"
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>  

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="ashwink"/>  

# Come configurare i webhook per gli avvisi

I webhook consentono all'utente di instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le notifiche personalizzate. Un esempio di questo scenario è rappresentato dall'instradamento degli avvisi a servizi in grado di gestire una richiesta Web in ingresso per inviare SMS, registrare bug, inviare notifiche a un team tramite chat e servizi di messaggistica e così via.

L'URI del webhook deve essere un endpoint HTTP o HTTPS valido. Il servizio di avviso di Azure eseguirà un'operazione POST in corrispondenza del webhook specificato e passerà un payload e uno schema JSON specifici.

## Configurazione di webhook tramite il portale

Nella schermata Creazione/aggiornamento avvisi nel [portale di Azure](https://portal.azure.com/) è possibile aggiungere o aggiornare l'URI del webhook.

![Aggiungere una regola di avviso](./media/insights-webhooks-alerts/Alertwebhook.png)


## Autenticazione

L'autenticazione può essere di due tipi:

1. **Autenticazione basata su token**: in questo caso l'URI del webhook verrà salvato con un ID token, ad esempio *https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue*
2.	**Autenticazione di base** (uso di ID utente e password): in questo caso l'URI del webhook verrà salvato come *https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar*

## Schema del payload

L'operazione POST conterrà il seguente payload e schema JSON per tutti gli avvisi basati su metriche.

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE] In occasione del prossimo aggiornamento, verrà aggiunto il supporto per gli avvisi relativi agli eventi ("conditionType": "Evento")


| Campo | Obbligatorio? | Set fisso di valori? | Note |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|S|"Activated", "Resolved"|Consente di capire di che tipo di avviso si tratta. Azure invia automaticamente gli avvisi attivati e risolti per la condizione impostata.|
|context| S | | Contesto dell'avviso|
|timestamp| S | | Ora in cui è stato attivato l'avviso. L'avviso viene attivato non appena la metrica viene letta dall'archiviazione della diagnostica.|
|id | S | | Ogni regola di avviso ha un ID univoco.|
|name|S | |
|description |S | |Descrizione dell'avviso.|
|conditionType |S |"Metric", "Event" |Sono supportati due tipi di avviso, uno basato sulla metrica e l'altro basato sull'evento. In futuro saranno supportati gli avvisi per gli eventi. Usare quindi questo valore ("Event") per controllare se l'avviso si basa sulla metrica o sull'evento.|
|condition |S | |Saranno disponibili campi specifici da verificare in base al valore di conditionType|
|metricName |Per avvisi relativi alle metriche | |Nome della metrica che definisce l'oggetto monitorato dalla regola.|
|metricUnit |Per avvisi relativi alle metriche |"Byte", "BytesPerSecond", "Count", "CountPerSecond", "Percent", "Seconds"|	 Unità consentita nella metrica. Valori consentiti: https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx|
|metricValue |Per avvisi relativi alle metriche | |Valore effettivo della metrica che ha generato l'avviso|
|threshold |Per avvisi relativi alle metriche | |Valore di soglia che attiva l'avviso|
|windowSize |Per avvisi relativi alle metriche | |Periodo di tempo usato per monitorare l'attività degli avvisi in base alla soglia. Deve essere compreso tra 5 minuti e 1 giorno. Il formato della durata è conforme a ISO 8601.|
|timeAggregation |Per avvisi relativi alle metriche |"Average", "Last" , "Maximum" , "Minimum" , "None", "Total" |	Definisce come i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". Valori consentiti: https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx|
|operator |Per avvisi relativi alle metriche | |Operatore usato per confrontare i dati e la soglia.|
|subscriptionId |S | |GUID della sottoscrizione di Azure|
|resourceGroupName |S | |Nome del gruppo di risorse della risorsa interessata|
|resourceName |S | |Nome della risorsa interessata|
|resourceType |S | |Tipo della risorsa interessata|
|resourceId |S | |URI dell'ID risorsa che identifica in modo univoco la risorsa|
|resourceRegion |S | |Area/posizione della risorsa interessata|
|portalLink |S | |Collegamento diretto al portale di Azure per la pagina di riepilogo delle risorse|
|properties |N |Facoltativo |È un set di coppie <Key, Value>, ad esempio Dictionary<String, String>, contenente i dettagli relativi all'evento. Il campo properties è facoltativo. In un flusso di lavoro basato su interfaccia utente personalizzata o app per la logica, gli utenti possono immettere una coppia chiave/valori che può essere passata tramite il payload. Il metodo alternativo per passare le proprietà personalizzate al webhook è rappresentato dall'URI del webhook stesso (sotto forma di parametri di query)|


>[AZURE.NOTE] Non è possibile usare il campo properties tramite il portale. Nella prossima versione di Insights SDK sarà possibile impostare le proprietà tramite l'API degli avvisi.

## Passaggi successivi

Per altre informazioni sugli avvisi di Azure e sui webhook, vedere il video [Integrare gli avvisi di Azure con PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)

Per informazioni su come creare a livello di programmazione un webhook, vedere [Creare un avviso con webhook tramite Azure Insights SDK (C#)](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a).

Dopo aver impostato i webhook e gli avvisi, esplorare le altre opzioni per avviare uno script di automazione.

[Eseguire gli script di Automazione di Azure (runbook)](http://go.microsoft.com/fwlink/?LinkId=627081)

Usare gli avvisi di Azure per inviare messaggi ad altri servizi. Usare i seguenti modelli di esempio per iniziare.

[Usare l'app per la logica per l'invio di SMS tramite l'API Twilio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[Usare l'app per la logica per l'invio di messaggi Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[Usare l'app per la logica per l'invio di messaggi a una coda di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0810_2016-->