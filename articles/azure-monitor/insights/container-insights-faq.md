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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: afa332b40884a79b5114b3b8093cd27108c39984
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779999"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Domande frequenti su Monitoraggio di Azure per i contenitori

Queste domande frequenti Microsoft sono un elenco di domande comuni su Monitoraggio di Azure per i container. Per altre domande sulla soluzione, visitare il [forum di discussione](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Il motivo per cui non sono visualizzati i dati nell'area di lavoro Log Analitica?

Se non si riesce a visualizzare i dati nell'area di lavoro di Log Analitica in un determinato tempo tutti i giorni, è stato raggiunto il limite di 500 MB predefinito o il limite massimo giornaliero per controllare la quantità di dati da raccogliere ogni giorno specificato. Quando viene raggiunto il limite per il giorno, la raccolta dei dati si interrompe e riprende solo nel giorno successivo. Per esaminare l'utilizzo dei dati e aggiornare in un altro piano tariffario basato su modelli di utilizzo previsto, vedere [Log di utilizzo dei dati e i costi](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quali sono gli stati di contenitore specificati nella tabella ContainerInventory?

La tabella ContainerInventory contiene informazioni sia sui contenitori arrestati che su quelli in esecuzione. La tabella viene popolata da un flusso di lavoro all'interno dell'agente che esegue una query in Docker per tutti i contenitori (in esecuzione e arrestati) e inoltra tali dati nell'area di lavoro Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Come si risolvono **registrazione della sottoscrizione manca** errore?

Se viene visualizzato l'errore **registrazione della sottoscrizione mancante per Microsoft.OperationsManagement**, è possibile risolvere il problema tramite la registrazione del provider di risorse **Microsoft.OperationsManagement** di sottoscrizione in cui è definito l'area di lavoro. La documentazione che illustra come eseguire questa operazione è disponibile [qui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>È disponibile il supporto per RBAC abilitato i cluster AKS?

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

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Come si risolvono gli errori di Azure AD quando Abilita i log in tempo reale? 

Può essere visualizzato l'errore seguente: **Risposta url specificato nella richiesta non corrisponde a URL di risposta configurati per l'applicazione: ' < ID applicazione\>'**. La soluzione per risolvere l'errore sono reperibili nell'articolo [come visualizzare i contenitori i log tempo reale con monitoraggio di Azure per contenitori](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Il motivo per cui non è possibile eseguire l'aggiornamento del cluster dopo l'onboarding?

Se dopo aver abilitato il monitoraggio di Azure per contenitori per un cluster AKS, si elimina l'area di lavoro di Log Analitica il cluster è stato l'invio dei dati, quando si tenta di aggiornare il cluster avrà esito negativo. Per risolvere questo problema, si dovrà disabilitare il monitoraggio e quindi riabilitarla una che fanno riferimento a un'altra area di lavoro valido nella sottoscrizione. Quando si prova a eseguire di nuovo l'aggiornamento del cluster, deve elaborare e completata correttamente.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quali porte e domini devo open/elenco elementi consentiti per l'agente?
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a monitorare un cluster servizio Azure Kubernetes, esaminare [Come eseguire l'onboarding di Monitoraggio di Azure per contenitori](container-insights-onboard.md) per conoscere i requisiti e i metodi disponibili per abilitare il monitoraggio. 
