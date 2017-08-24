---
title: "Trasmettere il log attività di Azure a Hub eventi | Microsoft Docs"
description: "Informazioni su come trasmettere il log attività di Azure a Hub eventi."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/06/2017
ms.author: johnkem
ms.translationtype: Human Translation
ms.sourcegitcommit: 51a7a274c0cdbec169154bd1100abb4534283cff
ms.openlocfilehash: ac478dc5c6691ef9f6c4829bd47a81912774d032
ms.contentlocale: it-it
ms.lasthandoff: 12/10/2016

---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Trasmettere il log attività di Azure a Hub eventi
Il [**log attività di Azure**](monitoring-overview-activity-logs.md) può essere trasmesso quasi in tempo reale a qualsiasi applicazione con l'opzione "Esporta" incorporata nel portale oppure abilitando l'ID regola del bus di servizio in un profilo di log tramite i cmdlet di Azure PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Che cosa si può fare con il log attività e Hub eventi
Ecco alcuni esempi di come è possibile usare la funzionalità di trasmissione per il log attività:

* **Trasmettere a sistemi di telemetria e registrazione di terze parti** : in futuro, la funzionalità di trasmissione di Hub eventi diventerà il meccanismo di invio del log attività a soluzioni di analisi di log e SIEM di terze parti.
* **Compilare una piattaforma di registrazione e telemetria personalizzata** : se è disponibile una piattaforma di telemetria personalizzata o si intende crearne una, le caratteristiche di pubblicazione-sottoscrizione altamente scalabili di Hub eventi offrono grande flessibilità per l'inserimento del log attività. [Vedere la guida all'uso di Hub eventi in una piattaforma di telemetria su scala globale di Dan Rosanova.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Abilitare la trasmissione del log attività
È possibile abilitare la trasmissione del log attività a livello di codice o tramite il portale. In entrambi i casi, selezionare uno spazio dei nomi del bus di servizio e un criterio di accesso condiviso per tale spazio dei nomi; quando si verifica il primo nuovo evento del log attività, viene creato un Hub eventi nello spazio dei nomi. Se non si dispone di uno spazio dei nomi del bus di servizio, è necessario crearne uno. Se in precedenza sono stati trasmessi gli eventi del log attività allo spazio dei nomi del bus di servizio, verrà usato di nuovo l'Hub eventi creato in quell'occasione. Il criterio di accesso condiviso definisce le autorizzazioni per il meccanismo di trasmissione. Al momento, per trasmettere a un hub eventi sono necessarie autorizzazioni di **gestione**, **invio** e **ascolto**. Per creare o modificare i criteri di accesso condiviso dello spazio dei nomi del bus di servizio nel portale classico è possibile usare la scheda "Configura" dello spazio dei nomi del bus di servizio. Per aggiornare il profilo di log del log attività e includere la trasmissione, l'utente che apporta la modifica deve disporre dell'autorizzazione ListKey nella regola di autorizzazione del bus di servizio.

Lo spazio dei nomi del bus di servizio o per l'hub eventi non deve trovarsi nella stessa sottoscrizione della sottoscrizione che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni.

### <a name="via-azure-portal"></a>Tramite il portale di Azure
1. Passare al pannello **Log attività** usando il menu sul lato sinistro del portale.
   
    ![Passare al log attività nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Fare clic sul pulsante **Esporta** nella parte superiore del pannello.
   
    ![Pulsante Esporta nel portale](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Nel pannello visualizzato è possibile selezionare le aree per cui trasmettere gli eventi e lo spazio dei nomi del bus di servizio in cui creare un hub eventi per la trasmissione degli eventi.
   
    ![Pannello Esporta log di controllo](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.

### <a name="via-powershell-cmdlets"></a>Tramite i cmdlet di PowerShell
Se esiste già un profilo di log, prima di tutto è necessario rimuoverlo.

1. Usare `Get-AzureRmLogProfile` per determinare se esiste già un profilo di log.
2. In tal caso, usare `Remove-AzureRmLogProfile` per rimuoverlo.
3. Usare `Set-AzureRmLogProfile` per creare un profilo:

```
Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

L'ID regola del bus di servizio è una stringa nel formato seguente: {service bus resource ID}/authorizationrules/{key name}. Ad esempio: 

### <a name="via-azure-cli"></a>Tramite l'interfaccia della riga di comando di Azure
Se esiste già un profilo di log, prima di tutto è necessario rimuoverlo.

1. Usare `azure insights logprofile list` per determinare se esiste già un profilo di log.
2. In tal caso, usare `azure insights logprofile delete` per rimuoverlo.
3. Usare `azure insights logprofile add` per creare un profilo:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Come utilizzare i dati di log da Hub eventi
[Lo schema per il log attività è disponibile qui](monitoring-overview-activity-logs.md). Ogni evento si trova in una matrice di BLOB JSON denominati "record".

## <a name="next-steps"></a>Passaggi successivi
* [Archive the Activity Log to a storage account](monitoring-archive-activity-log.md) (Archiviare il log attività in un account di archiviazione)
* Leggere la [panoramica sul log attività di Azure](monitoring-overview-activity-logs.md)
* [Set up an alert based on an Activity Log event](insights-auditlog-to-webhook-email.md) (Configurare un avviso in base a un evento del log attività)


