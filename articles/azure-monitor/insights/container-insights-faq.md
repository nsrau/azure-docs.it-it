---
title: Domande frequenti su Monitoraggio di Azure per i contenitori | Microsoft Docs
description: Monitoraggio di Azure per i contenitori è una soluzione che consente di monitorare l'integrità dei cluster del servizio Azure Container e di Istanze di Container in Azure. Questo articolo fornisce le risposte alle domande frequenti.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: 250bf6ede3a54b26b6e9c68850f8e7260020002e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960512"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Domande frequenti su Monitoraggio di Azure per i contenitori
Queste domande frequenti Microsoft sono un elenco di domande comuni su Monitoraggio di Azure per i container. Per altre domande sulla soluzione, visitare il [forum di discussione](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="i-am-unable-to-see-any-data-in-the-log-analytics-workspace-at-a-certain-time-everyday-how-do-i-resolve-this"></a>Non riesco a vedere tutti i dati nell'area di lavoro di Log Analitica a una determinata ora ogni giorno. Come posso risolvere il problema? 

È stato raggiunto il limite di 500 MB predefinito o il limite massimo giornaliero per controllare la quantità di dati da raccogliere ogni giorno specificato. Quando viene raggiunto il limite per il giorno, la raccolta dei dati si interrompe e riprende solo nel giorno successivo. Per esaminare l'utilizzo dei dati e aggiornare in un altro piano tariffario basato su modelli di utilizzo previsto, vedere [Log di utilizzo dei dati e i costi](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Quali sono gli stati dei contenitori specificati nella tabella ContainerInventory?
La tabella ContainerInventory contiene informazioni sia sui contenitori arrestati che su quelli in esecuzione. La tabella viene popolata da un flusso di lavoro all'interno dell'agente che esegue una query in Docker per tutti i contenitori (in esecuzione e arrestati) e inoltra tali dati nell'area di lavoro di Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Come si risolvono gli errori correlati alla **mancanza della registrazione della sottoscrizione per Microsoft.OperationsManagement**?
Per risolvere l'errore, registrare il provider di risorse **Microsoft.OperationsManagement** nella sottoscrizione in cui è definita l'area di lavoro. La documentazione che illustra come eseguire questa operazione è disponibile [qui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>Monitoraggio di Azure per i contenitori include il supporto per i cluster del servizio Azure Container abilitati per il controllo degli accessi in base al ruolo?
La soluzione monitoraggio contenitori non supporta RBAC, ma è supportato con monitoraggio di Azure per contenitori. Le informazioni presenti nei pannelli che visualizzano i dati relativi a tali cluster all'interno della pagina dei dettagli della soluzione possono non essere corrette.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Come si abilita la raccolta di log per i contenitori nello spazio dei nomi kube-system tramite Helm?
La raccolta di log dai contenitori nello spazio dei nomi kube-system è disabilitata per impostazione predefinita. È possibile abilitare la raccolta di log impostando una variabile di ambiente in omsagent. Per altre informazioni, vedere la [monitoraggio di Azure per contenitori](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) pagina su GitHub. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Come si aggiorna omsagent alla versione rilasciata più recente?
Per informazioni su come aggiornare l'agente, vedere [Gestione dell'agente](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Come si abilita la registrazione su più righe?
Monitoraggio di Azure per i contenitori attualmente non supporta la registrazione su più righe, ma sono disponibili soluzioni alternative. È possibile configurare tutti i servizi per la scrittura in formato JSON. Docker/Moby eseguirà quindi la scrittura su un'unica riga.

È ad esempio possibile eseguire il wrapping del log come oggetto JSON, come illustrato nell'esempio seguente per un'applicazione node.js di esempio:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Questi dati saranno simile al seguente in Monitoraggio di Azure per i log quando esegue una query per tale:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Per un'analisi approfondita del problema, vedere il [collegamento a GitHub](https://github.com/moby/moby/issues/22920) seguente.

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Come si risolvono gli errori di Azure Active Directory quando si abilitano i log in tempo reale? 
Può essere visualizzato l'errore seguente: **Risposta url specificato nella richiesta non corrisponde a URL di risposta configurati per l'applicazione: ' < ID applicazione\>'**. La soluzione per risolvere l'errore sono reperibili nell'articolo [come visualizzare i contenitori i log tempo reale con monitoraggio di Azure per contenitori](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a monitorare un cluster servizio Azure Kubernetes, esaminare [Come eseguire l'onboarding di Monitoraggio di Azure per contenitori](container-insights-onboard.md) per conoscere i requisiti e i metodi disponibili per abilitare il monitoraggio. 