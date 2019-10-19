---
title: Domande frequenti su Monitoraggio di Azure per i contenitori | Microsoft Docs
description: Monitoraggio di Azure per i contenitori è una soluzione che consente di monitorare l'integrità dei cluster del servizio Azure Container e di Istanze di Container in Azure. Questo articolo fornisce le risposte alle domande frequenti.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/14/2019
ms.openlocfilehash: bda64dd555f1970b70878d827f6be1dab3f1e2d5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555424"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Domande frequenti su Monitoraggio di Azure per i contenitori

Queste domande frequenti Microsoft sono un elenco di domande comuni su Monitoraggio di Azure per i container. Per altre domande sulla soluzione, visitare il [forum di discussione](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>È possibile monitorare il cluster del motore AKS con monitoraggio di Azure per i contenitori?

Monitoraggio di Azure per contenitori supporta il monitoraggio dei carichi di lavoro del contenitore distribuiti nei cluster AKS-Engine (in precedenza noti come ACS-Engine) ospitati in Azure. Per altri dettagli e una panoramica dei passaggi necessari per abilitare il monitoraggio per questo scenario, vedere [uso di monitoraggio di Azure per i contenitori per il motore AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Perché non vengono visualizzati i dati nell'area di lavoro Log Analytics?

Se non si riesce a visualizzare i dati nell'area di lavoro Log Analytics in un determinato momento ogni giorno, è possibile che sia stato raggiunto il limite di 500 MB predefinito o il limite giornaliero specificato per controllare la quantità di dati da raccogliere quotidianamente. Quando viene raggiunto il limite per il giorno, la raccolta dei dati si interrompe e riprende solo il giorno successivo. Per esaminare l'utilizzo dei dati ed eseguire l'aggiornamento a un piano tariffario diverso in base ai modelli di utilizzo previsti, vedere l' [utilizzo dei dati di log e i costi](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quali sono gli Stati del contenitore specificati nella tabella ContainerInventory?

La tabella ContainerInventory contiene informazioni sia sui contenitori arrestati che su quelli in esecuzione. La tabella viene popolata da un flusso di lavoro all'interno dell'agente che esegue una query in Docker per tutti i contenitori (in esecuzione e arrestati) e inoltra tali dati nell'area di lavoro Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Ricerca per categorie risolvere un errore di *registrazione della sottoscrizione mancante* ?

Se viene visualizzato un errore di **registrazione della sottoscrizione mancante per Microsoft. OperationsManagement**, è possibile risolverlo registrando il provider di risorse **Microsoft. OperationsManagement** nella sottoscrizione in cui è definita l'area di lavoro. La documentazione che illustra come eseguire questa operazione è disponibile [qui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>È previsto il supporto per i cluster AKS abilitati per RBAC?

La soluzione di monitoraggio dei contenitori non supporta RBAC, ma è supportata con monitoraggio di Azure per i contenitori. Le informazioni presenti nei pannelli che visualizzano i dati relativi a tali cluster all'interno della pagina dei dettagli della soluzione possono non essere corrette.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Come si abilita la raccolta di log per i contenitori nello spazio dei nomi kube-system tramite Helm?

La raccolta di log dai contenitori nello spazio dei nomi kube-system è disabilitata per impostazione predefinita. È possibile abilitare la raccolta di log impostando una variabile di ambiente in omsagent. Per ulteriori informazioni, vedere la pagina [di GitHub monitoraggio di Azure per contenitori](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) . 

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

Questi dati appariranno come nell'esempio seguente in monitoraggio di Azure per i log quando si esegue una query:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Per informazioni dettagliate sul problema, vedere il [collegamento di GitHub](https://github.com/moby/moby/issues/22920)seguente.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Ricerca per categorie risolvere gli errori Azure AD quando si abilitano I log Live? 

È possibile che venga visualizzato l'errore seguente: **l'URL di risposta specificato nella richiesta non corrisponde agli URL di risposta configurati per l'applicazione:' < ID applicazione \>'** . La soluzione per risolverla è disponibile nell'articolo come visualizzare i log dei contenitori in [tempo reale con monitoraggio di Azure per i contenitori](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Perché non è possibile aggiornare il cluster dopo l'onboarding?

Se dopo l'abilitazione di monitoraggio di Azure per i contenitori per un cluster AKS si elimina l'area di lavoro Log Analytics cui il cluster stava inviando i dati, quando si tenta di aggiornare il cluster l'operazione avrà esito negativo. Per risolvere il problema, sarà necessario disabilitare il monitoraggio e quindi riabilitarlo facendo riferimento a un'area di lavoro valida diversa nella sottoscrizione. Quando si tenta di eseguire di nuovo l'aggiornamento del cluster, il processo dovrebbe essere elaborato e completato correttamente.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quali porte e domini è necessario aprire/inserire nell'elenco elementi consentiti per l'agente?
- *. ods.opinsights.azure.com 443
- *. oms.opinsights.azure.com 443
- *. blob.core.windows.net 443
- dc.services.visualstudio.com 443
- *. microsoftonline.com 443
- *. monitoring.azure.com 443
- login.microsoftonline.com 443

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a monitorare un cluster servizio Azure Kubernetes, esaminare [Come eseguire l'onboarding di Monitoraggio di Azure per contenitori](container-insights-onboard.md) per conoscere i requisiti e i metodi disponibili per abilitare il monitoraggio. 
