---
title: Abilitare il monitoraggio e la diagnostica in Microsoft Azure | Microsoft Docs
description: Informazioni su come configurare la diagnostica per le risorse in Azure.
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: af1947a9-c211-4aa1-8924-880a86240be4
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: b82bb1ab419831e803689edb2a2a7fe256dde5a2
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017


---
# <a name="enable-monitoring-and-diagnostics"></a>Abilitare il monitoraggio e la diagnostica
Nel [portale di Azure](https://portal.azure.com), è possibile configurare rich data di monitoraggio e diagnostica frequenti relativi alle risorse. È anche possibile usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) o [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) per configurare la diagnostica a livello di codice.

I dati di diagnostica, di monitoraggio e delle metriche di Azure vengono salvati nell'account di archiviazione preferito. Questo consente di usare qualsiasi strumento per leggere i dati, ad esempio uno strumento di esplorazione di archiviazione, Power BI o uno strumento di terze parti.

## <a name="when-you-create-a-resource"></a>Quando si crea una risorsa
La maggior parte dei servizi consente di abilitare la diagnostica quando li si crea nel [portale di Azure](https://portal.azure.com).

1. Andare a **Nuovo** e scegliere la risorsa a cui si è interessati.
2. Selezionare **Configurazione facoltativa**.
    ![Pannello Diagnostica](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)
3. Selezionare **Diagnostica** e fare clic sull'opzione di **attivazione**. Occorre scegliere l'account di archiviazione in cui salvare la diagnostica. Per l'invio della diagnostica a un account di archiviazione, verranno addebitate le normali tariffe dati per l'archiviazione e le transazioni.
4. Fare clic su **OK** e creare la risorsa.

## <a name="change-settings-for-an-existing-resource"></a>Modificare le impostazioni per una risorsa esistente
Se si è già creata una risorsa e si vuole modificare le impostazioni di diagnostica (per modificare il livello di raccolta dati, ad esempio), è possibile farlo direttamente nel portale di Azure.

1. Andare alla risorsa e fare clic sul comando **Impostazioni** .
2. Selezionare **Diagnostica**.
3. Il pannello **Diagnostica** contiene tutte le possibili diagnostiche e i dati delle raccolte di monitoraggio della risorsa. Per alcune risorse è inoltre possibile scegliere un criterio **Conservazione** per i dati, per eliminarli dall'account di archiviazione.
    ![Diagnostica dell'archiviazione](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. Dopo aver scelto le impostazioni, fare clic sul comando **Salva** . La visualizzazione dei dati di monitoraggio potrebbe richiedere un po' di tempo se li si sta abilitando per la prima volta.

### <a name="categories-of-data-collection-for-virtual-machines"></a>Categorie di raccolta dati per le macchine virtuali
Per le macchine virtuali, tutte le metriche e i log verranno registrati a intervalli di un minuto per far sì che l'utente disponga sempre delle informazioni più aggiornate sulla macchina in uso.

* **Metriche di base** : metriche sull'integrità relative alla macchina virtuale in uso, ad esempio il processore e la memoria
* **Metriche Web e di rete** : metriche relative alle connessioni di rete e ai servizi Web
* **Metriche di .NET** : metriche relative alle applicazioni .NET e ASP.NET in esecuzione sulla macchina virtuale in uso
* **Metriche di SQL** : metriche delle prestazioni, se si esegue il servizio Microsoft SQL
* **Log eventi dell'applicazione di Windows** : eventi Windows inviati al canale delle applicazioni
* **Log eventi del sistema di Windows** : eventi Windows inviati al canale di sistema, inclusi tutti gli eventi [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)
* **Log eventi di sicurezza di Windows** : eventi Windows inviati al canale di sicurezza
* **Log dell'infrastruttura di diagnostica** : registrazioni relative all'infrastruttura per la raccolta della diagnostica
* **Log di IIS** : log relativi al server IIS in uso

Tenere presente che in questo momento non sono supportate alcune distribuzioni di Linux e l'agente guest deve essere installato nella macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* [Monitorare le metriche del servizio](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Scalare automaticamente il numero di istanze](insights-how-to-scale.md) per assicurarsi che il servizio venga scalato in base alla domanda.
* [Monitorare le prestazioni dell'applicazione](../application-insights/app-insights-azure-web-apps.md) se si desidera comprendere esattamente il comportamento del codice nel cloud.
* [Visualizzare eventi e registri attività](insights-debugging-with-events.md) per informazioni su tutti gli eventi che si sono verificati nel servizio.
* [Tenere traccia dello stato del servizio](insights-service-health.md) per scoprire quando Azure ha subito un calo delle prestazioni o interruzioni del servizio.


