<properties
	pageTitle="Trasmettere il log attività di Azure a Hub eventi | Microsoft Azure"
	description="Informazioni su come trasmettere il log attività di Azure a Hub eventi."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Trasmettere il log attività di Azure a Hub eventi
Il [**log attività di Azure**](./monitoring-overview-activity-logs.md) può essere trasmesso quasi in tempo reale a qualsiasi applicazione con l'opzione "Esporta" incorporata nel portale oppure abilitando l'ID regola del bus di servizio in un profilo di log tramite i cmdlet di Azure PowerShell o l'interfaccia della riga di comando di Azure.

## Che cosa si può fare con il log attività e Hub eventi
Ecco alcuni esempi di come è possibile usare la funzionalità di trasmissione per il log attività:

- **Trasmettere a sistemi di telemetria e registrazione di terze parti**: in futuro, la funzionalità di trasmissione di Hub eventi diventerà il meccanismo di invio del log attività a soluzioni di analisi di log e SIEM di terze parti.
- **Compilare una piattaforma di registrazione e telemetria personalizzata**: se è disponibile una piattaforma di telemetria personalizzata o si intende crearne una, le caratteristiche di pubblicazione-sottoscrizione altamente scalabili di Hub eventi offrono grande flessibilità per l'inserimento del log attività. [Vedere la guida all'uso di Hub eventi in una piattaforma di telemetria su scala globale di Dan Rosanova.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## Abilitare la trasmissione del log attività
È possibile abilitare la trasmissione del log attività a livello di codice o tramite il portale. In entrambi i casi, si sceglie uno spazio dei nomi del bus di servizio. Se non esiste, crearne uno. Se si trasmette il log attività per la prima volta, nello spazio dei nomi selezionato viene creato l'hub eventi. Se il log attività è già stato trasmesso a questo spazio dei nomi, l'hub eventi vi viene trasmesso. I criteri definiscono le autorizzazioni del meccanismo di trasmissione. Al momento, per trasmettere a un hub eventi sono necessarie autorizzazioni di **gestione**, **lettura** e **invio**. Per creare o modificare i criteri di accesso condiviso dello spazio dei nomi del bus di servizio nel portale classico è possibile usare la scheda "Configura" dello spazio dei nomi del bus di servizio. Per aggiornare il profilo di log del log attività e includere la trasmissione, il client deve avere l'autorizzazione ListKey nella regola di autorizzazione del bus di servizio.

### Tramite il portale di Azure 
1. Passare al pannello **Log attività** usando il menu sul lato sinistro del portale.

    ![Passare al log attività nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Fare clic sul pulsante **Esporta** nella parte superiore del pannello.

    ![Pulsante Esporta nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Nel pannello visualizzato è possibile selezionare le aree per cui trasmettere gli eventi e lo spazio dei nomi del bus di servizio in cui creare un hub eventi per la trasmissione degli eventi.

    ![Pannello Esporta log di controllo](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.


### Tramite i cmdlet di PowerShell
Se esiste già un profilo di log, prima di tutto è necessario rimuoverlo.

1. Usare `Get-AzureRmLogProfile` per determinare se esiste già un profilo di log.
2. In tal caso, usare `Remove-AzureRmLogProfile` per rimuoverlo.
3. Usare `Set-AzureRmLogProfile` per creare un profilo:

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

L'ID regola del bus di servizio è una stringa nel formato seguente: {service bus resource ID}/authorizationrules/{key name}. Ad esempio:

### Tramite l'interfaccia della riga di comando di Azure
Se esiste già un profilo di log, prima di tutto è necessario rimuoverlo.

1. Usare `azure insights logprofile list` per determinare se esiste già un profilo di log.
2. In tal caso, usare `azure insights logprofile delete` per rimuoverlo.
3. Usare `azure insights logprofile add` per creare un profilo:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`.
 
## Come utilizzare i dati di log da Hub eventi
[Lo schema per il log attività è disponibile qui](./monitoring-overview-activity-logs.md). Ogni evento si trova in una matrice di BLOB JSON denominati "record".

## Passaggi successivi
- [Archive the Activity Log to a storage account](./monitoring-archive-activity-log.md) (Archiviare il log attività in un account di archiviazione)
- [Vedere la panoramica del log attività di Azure](./monitoring-overview-activity-logs.md)
- [Impostare un avviso in base a un evento del log attività](./insights-auditlog-to-webhook-email.md)

<!---HONumber=AcomDC_0824_2016-->