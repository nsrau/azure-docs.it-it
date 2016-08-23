<properties
	pageTitle="Azure Insights: configurazione avanzata del ridimensionamento automatico con modelli di Resource Manager per set di scalabilità di macchine virtuali | Microsoft Azure"
	description="Configurare il ridimensionamento automatico per i set di scalabilità di macchine virtuali in base a più regole e profili con le notifiche di posta elettronica e webhoook per le azioni di scalabilità."
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
	ms.date="08/04/2016"
	ms.author="ashwink"/>  

# Configurazione avanzata del ridimensionamento automatico con modelli di Resource Manager per set di scalabilità di macchine virtuali

È possibile aumentare e ridurre il numero di istanze dei set di scalabilità di macchine virtuali in base ai valori soglia per le metriche delle prestazioni, con una pianificazione ricorrente o in una determinata data. È anche possibile configurare notifiche di posta elettronica e webhook per le azioni di scalabilità. Questa procedura dettagliata illustra un esempio di configurazione di quanto elencato sopra con un modello di Resource Manager in un set di scalabilità di macchine virtuali.

>[AZURE.NOTE] Questa procedura dettagliata illustra i passaggi per i set di scalabilità di macchine virtuali, ma è possibile applicarla anche alla scalabilità automatica di servizi cloud e app Web. Per configurare una semplice impostazione di riduzione/aumento del numero di istanze in un set di scalabilità di macchine virtuali in base a una semplice metrica delle prestazioni, ad esempio la CPU, vedere i documenti relativi a [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) e [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



## Procedura dettagliata
In questa procedura dettagliata viene usato [Esplora risorse di Azure](https://resources.azure.com/) per configurare e aggiornare l'impostazione di ridimensionamento automatico per un set di scalabilità di macchine virtuali. Esplora risorse di Azure consente di gestire facilmente le risorse di Azure con i modelli di Resource Manager. Se non si ha esperienza con lo strumento Esplora risorse di Azure, vedere [questa introduzione](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Distribuire un nuovo set di scalabilità di macchine virtuali con l'impostazione di ridimensionamento automatico di base. Questo articolo usa quello della raccolta di guide introduttive di Azure che ha un set di scalabilità di macchine virtuali Windows con il modello di scalabilità automatica di base. Funziona allo stesso modo anche per le istanze del set di scalabilità di macchine virtuali Linux.

2. Una volta creato il set di scalabilità di macchine virtuali, passare alla risorsa set di scalabilità di macchine virtuali da Esplora risorse di Azure. Sotto il nodo Microsoft.Insights verrà visualizzato quanto segue.

	![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)  

	L'esecuzione del modello ha creato un'impostazione predefinita di ridimensionamento automatico con il nome **"autoscalewad"**. Sul lato destro è possibile visualizzare la definizione completa di questa impostazione di ridimensionamento automatico. In questo caso, l'impostazione di ridimensionamento automatico predefinita è inclusa in una regola di aumento e riduzione del numero di istanze basata sulla percentuale di CPU.

3. Ora è possibile aggiungere altri profili e regole basati sulla pianificazione o su specifici requisiti. Viene creata un'impostazione di ridimensionamento automatico con 3 profili. Per conoscere i profili e le regole nel ridimensionamento automatico, vedere [Procedure consigliate per la scalabilità automatica in Azure Insights](./insights-autoscale-best-practices.md).

    | Profili e regole | Descrizione |
	|---------|-------------------------------------|
	| **Profilo** | **Basato su prestazioni/metrica** |
	| Regola | Numero di messaggi della coda del bus di servizio > x |
	| Regola | Numero di messaggi della coda del bus di servizio < y |
	| Regola | % CPU < n |
	| Regola | % CPU < p |
	| **Profilo** | **Ore della mattina dei giorni feriali (nessuna regola)** |
	| **Profilo** | **Giorno di lancio del prodotto (nessuna regola)** |

4. Ecco uno scenario di ridimensionamento ipotetico che verrà usato ai fini di questa esercitazione.
	- _**Basato sul carico**: si vuole aumentare o ridurre il numero di istanze in base al carico sull'applicazione ospitata nel set di scalabilità di macchine virtuali._
	- _**Dimensioni della coda di messaggi**: si usa una coda del bus di servizio per i messaggi in arrivo nell'applicazione. Si usa il numero di messaggi della coda e la percentuale di CPU e si configura un profilo predefinito per attivare un'azione di scalabilità se il conteggio dei messaggi o la CPU raggiunge la soglia._
	- _**Ora della settimana e del giorno**: si vuole un profilo basato su un'"ora del giorno" con ricorrenza settimanale denominato "Ore della mattina dei giorni feriali". In base ai dati cronologici, si è stabilito che è meglio avere un certo numero di istanze di VM per gestire il carico dell'applicazione durante questo orario._
	- _**Date speciali**: si è aggiunto un profilo "Giorno di lancio del prodotto". Si pianificano in anticipo date specifiche in modo che l'applicazione sia pronta a gestire il carico derivante da annunci di marketing e dall'inserimento di un nuovo prodotto nell'applicazione._
	- _Agli ultimi due profili possono essere associate anche altre regole basate sulle metriche delle prestazioni, ma in questo caso si è deciso di non aggiungerne e di usare solo le regole basate sulle metriche delle prestazioni predefinite. Le regole sono facoltative per i profili ricorrenti e basati sulle date._

	La classificazione in ordine di priorità dei profili e delle regole con il motore di ridimensionamento automatico è illustrata anche nell'articolo [Procedure consigliate per la scalabilità automatica in Azure Insights](insights-autoscale-best-practices.md). Per un elenco di metriche comuni per il ridimensionamento automatico, vedere [Metriche comuni per la scalabilità automatica di Azure Insights](insights-autoscale-common-metrics.md).

5. Verificare che Esplora risorse sia in modalità **Lettura/Scrittura**.

	![Autoscalewad, impostazione di ridimensionamento automatico predefinita](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Fare clic su Modifica. **Sostituire** l'elemento "profiles" nell'impostazione di ridimensionamento automatico con il codice seguente:

	![Profili](./media/insights-advanced-autoscale-vmss/profiles.png)  

	```
	{
	        "name": "Perf_Based_Scale",
	        "capacity": {
	          "minimum": "2",
	          "maximum": "12",
	          "default": "2"
	        },
	        "rules": [
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 10
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 3
	            },
	            "scaleAction": {
	              "direction": "Decrease",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 85
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 60
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          }
	        ]
	      },
	      {
	        "name": "Weekday_Morning_Hours_Scale",
	        "capacity": {
	          "minimum": "4",
	          "maximum": "12",
	          "default": "4"
	        },
	        "rules": [],
	        "recurrence": {
	          "frequency": "Week",
	          "schedule": {
	            "timeZone": "Pacific Standard Time",
	            "days": [
	              "Monday",
	              "Tuesday",
	              "Wednesday",
	              "Thursday",
	              "Friday"
	            ],
	            "hours": [
	              6
	            ],
	            "minutes": [
	              0
	            ]
	          }
	        }
	      },
	      {
	        "name": "Product_Launch_Day",
	        "capacity": {
	          "minimum": "6",
	          "maximum": "20",
	          "default": "6"
	        },
	        "rules": [],
	        "fixedDate": {
	          "timeZone": "Pacific Standard Time",
	          "start": "2016-06-20T00:06:00Z",
	          "end": "2016-06-21T23:59:00Z"
	        }
	      }
	```
	Per i campi e i valori supportati, vedere la [documentazione sull'API REST per il ridimensionamento automatico](https://msdn.microsoft.com/it-IT/library/azure/dn931928.aspx).

	Ora l'impostazione di ridimensionamento automatico contiene i 3 profili illustrati sopra.

7. 	Verrà esaminata infine la sezione **notification** per il ridimensionamento automatico. Le notifiche di ridimensionamento automatico consentono di eseguire tre operazioni quando un'azione di aumento o riduzione del numero di istanze viene correttamente attivata.

	1. Inviare una notifica all'amministratore e ai coamministratori della sottoscrizione.

	2. Inviare un messaggio di posta elettronica a un set di utenti.

	3. Attivare una chiamata webhook. Se attivato, questo webhook invierà i metadati sulla condizione di scalabilità automatica e sulla risorsa set di scalabilità di macchine virtuali. Per altre informazioni sul payload del webhook di ridimensionamento automatico, vedere [Configurare notifiche webhook e di posta elettronica per il ridimensionamento automatico](./insights-autoscale-to-webhook-email.md).

	Aggiungere il codice seguente all'impostazione di ridimensionamento automatico sostituendo l'elemento **notification** il cui valore è null.

	```
	"notifications": [
	      {
	        "operation": "Scale",
	        "email": {
	          "sendToSubscriptionAdministrator": true,
	          "sendToSubscriptionCoAdministrators": false,
	          "customEmails": [
	              "user1@mycompany.com",
	              "user2@mycompany.com"
	              ]
	        },
	        "webhooks": [
	          {
	            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
	            "properties": {
	              "optional_key1": "optional_value1",
	              "optional_key2": "optional_value2"
	            }
	          }
	        ]
	      }
	    ]

	```

	Fare clic sul pulsante **Put** in Esplora risorse per aggiornare l'impostazione di scalabilità automatica.

È stata aggiornata un'impostazione di ridimensionamento automatico in un set di scalabilità di macchine virtuali per includere più profili di scalabilità e notifiche di scalabilità.

## Passaggi successivi

Per altre informazioni sulla scalabilità automatica, usare questi collegamenti.

[Metriche comuni per il ridimensionamento automatico](./insights-autoscale-common-metrics.md)

[Procedure consigliate per il ridimensionamento automatico di Azure](./insights-autoscale-best-practices.md)

[Gestire il ridimensionamento automatico con PowerShell](./insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Gestire il ridimensionamento automatico con l'interfaccia della riga di comando](./insights-cli-samples.md#autoscale)

[Configurare notifiche webhook e di posta elettronica per il ridimensionamento automatico](./insights-autoscale-to-webhook-email.md)

<!---HONumber=AcomDC_0810_2016-->