---
title: Trasmettere il log attività di Azure a Hub eventi | Microsoft Docs
description: Informazioni su come trasmettere il log attività di Azure a Hub eventi.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 4b2d9866839f943f65beb271d44bc691441b0fb3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Trasmettere il log attività di Azure a Hub eventi
È possibile trasmettere il [log attività di Azure](monitoring-overview-activity-logs.md) quasi in tempo reale a qualsiasi applicazione:

* Tramite l'opzione predefinita **Esporta** disponibile nel portale
* Abilitando l'ID regola del bus di servizio di Azure in un profilo di log con i cmdlet di Azure PowerShell o l'interfaccia della riga di comando di Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Che cosa si può fare con il log attività e Hub eventi
Di seguito sono riportati due esempi di come è possibile usare la funzionalità di trasmissione per il log attività:

* **Trasmettere a sistemi di telemetria e registrazione di terze parti** : in futuro, la funzionalità di trasmissione di Hub eventi di Azure diventerà il meccanismo di invio del log attività a soluzioni di analisi di log e SIEM di terze parti.
* **Creare una piattaforma di telemetria e registrazione personalizzata** : se si ha già una piattaforma di telemetria personalizzata o si intende crearne una, le caratteristiche di pubblicazione-sottoscrizione altamente scalabili di Hub eventi consentono di inserire il log attività con la massima flessibilità. Per altre informazioni, guardare il [video di Dan Rosanova sull'uso di Hub eventi in una piattaforma di telemetria su scala globale](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Abilitare la trasmissione del log attività
È possibile abilitare la trasmissione del log attività a livello di codice o tramite il portale. In entrambi i casi è necessario scegliere uno spazio dei nomi di Hub eventi e criteri di accesso condiviso per tale spazio dei nomi. Quando si verifica il primo nuovo evento del log attività, in questo spazio dei nomi viene creato un hub eventi con il nome insights-logs-operationallogs. 

Se non si ha uno spazio dei nomi di Hub eventi, è necessario crearne uno. Se in precedenza sono stati trasmessi eventi del log attività a questo spazio dei nomi di Hub eventi, verrà usato nuovamente l'hub eventi creato in tale occasione. 

Il criterio di accesso condiviso definisce le autorizzazioni per il meccanismo di trasmissione. Attualmente la trasmissione a Hub eventi richiede autorizzazioni di **gestione**, **invio** e **attesa**. È possibile creare o modificare i criteri di accesso condiviso per lo spazio dei nomi di Hub eventi nel portale di Azure, nella scheda **Configura** relativa a tale spazio dei nomi. 

Per aggiornare il profilo del log attività e includere la trasmissione, l'utente che apporta la modifica deve avere l'autorizzazione ListKey nella regola di autorizzazione di Hub eventi. Lo spazio dei nomi di Hub eventi non deve necessariamente trovarsi nella stessa sottoscrizione in cui vengono creati i log, purché l'utente che configura l'impostazione abbia l'accesso RBAC appropriato a entrambe le sottoscrizioni.

### <a name="via-the-azure-portal"></a>Tramite il portale di Azure
1. Passare alla sezione **Log attività** usando la casella di ricerca **Tutti i servizi** sul lato sinistro del portale.
   
   ![Selezione di Log attività nell'elenco dei servizi disponibili nel portale](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Selezionare il pulsante **Esporta** nella parte superiore del log.
   
   ![Pulsante Esporta nel portale](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Si noti che le impostazioni di filtro applicate durante la visualizzazione del log attività in precedenza non hanno alcun effetto sulle impostazioni di esportazione. Servono solo per filtrare i dati visualizzati durante l'esplorazione del log attività nel portale.
3. Nella sezione che viene visualizzata selezionare **Tutte le aree**. Non selezionare aree specifiche.
   
   ![Sezione di esportazione](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Se si seleziona qualsiasi opzione diversa da **Tutte le aree**, non verranno trovati gli eventi principali che si prevede di ricevere. Il log attività è di tipo globale e non correlato ad aree specifiche. La maggior parte degli eventi, pertanto, non è associata a un'area. 
   > 

4. Selezionare **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.
5. Se si hanno più sottoscrizioni, ripetere questa operazione e inviare tutti i dati allo stesso hub eventi.

### <a name="via-powershell-cmdlets"></a>Tramite i cmdlet di PowerShell
Se esiste già un profilo di log, prima di tutto è necessario rimuoverlo.

1. Usare `Get-AzureRmLogProfile` per determinare se esiste già un profilo di log.
2. In tal caso, usare `Remove-AzureRmLogProfile` per rimuoverlo.
3. Usare `Set-AzureRmLogProfile` per creare un profilo:

   ```powershell

   Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

   ```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`. 

### <a name="via-azure-cli"></a>Tramite l'interfaccia della riga di comando di Azure
Se esiste già un profilo di log, prima di tutto è necessario rimuoverlo.

1. Usare `azure insights logprofile list` per determinare se esiste già un profilo di log.
2. In tal caso, usare `azure insights logprofile delete` per rimuoverlo.
3. Usare `azure insights logprofile add` per creare un profilo:

   ```azurecli-interactive
   azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
   ```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="consume-the-log-data-from-event-hubs"></a>Utilizzare i dati di log da Hub eventi
Lo schema del log attività è disponibile in [Monitorare l'attività di sottoscrizione con il log attività di Azure](monitoring-overview-activity-logs.md). Ogni evento si trova in una matrice di BLOB JSON denominati *record*.

## <a name="next-steps"></a>Passaggi successivi
* [Archive the Activity Log to a storage account](monitoring-archive-activity-log.md) (Archiviare il log attività in un account di archiviazione)
* Leggere la [panoramica sul log attività di Azure](monitoring-overview-activity-logs.md)
* [Set up an alert based on an Activity Log event](insights-auditlog-to-webhook-email.md) (Configurare un avviso in base a un evento del log attività)

