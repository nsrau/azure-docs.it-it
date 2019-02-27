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
ms.date: 02/18/2019
ms.author: magoedte
ms.openlocfilehash: 27a191bb62ae59aa154167a22c99d3e699f3eb5a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418428"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Domande frequenti su Monitoraggio di Azure per i contenitori
Queste domande frequenti Microsoft sono un elenco di domande comuni su Monitoraggio di Azure per i container. Per altre domande sulla soluzione, visitare il [forum di discussione](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="why-am-i-not-seeing-data-if-log-analytics-workspace-is-configured-with-the-free-pricing-tier"></a>Perché non vengono visualizzati dati se l'area di lavoro di Log Analytics è configurata con il piano tariffario Gratuito? 

Può essere stato raggiunto il limite predefinito di 500 MB o può essere stato specificato un limite giornaliero per controllare la quantità di dati da raccogliere ogni giorno. Per controllare e gestire l'utilizzo dei dati, vedere [Registrare l'utilizzo dei dati e il costo](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Quali sono gli stati dei contenitori specificati nella tabella ContainerInventory?
La tabella ContainerInventory contiene informazioni sia sui contenitori arrestati che su quelli in esecuzione. La tabella viene popolata da un flusso di lavoro all'interno dell'agente che esegue una query in Docker per tutti i contenitori (in esecuzione e arrestati) e inoltra tali dati nell'area di lavoro di Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Come si risolvono gli errori correlati alla **mancanza della registrazione della sottoscrizione per Microsoft.OperationsManagement**?
Per risolvere l'errore, registrare il provider di risorse **Microsoft.OperationsManagement** nella sottoscrizione in cui è definita l'area di lavoro. La documentazione che illustra come eseguire questa operazione è disponibile [qui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>Monitoraggio di Azure per i contenitori include il supporto per i cluster del servizio Azure Container abilitati per il controllo degli accessi in base al ruolo?
I cluster del servizio Azure Container abilitati per il controllo degli accessi in base al ruolo non sono attualmente supportati dalla soluzione. Le informazioni presenti nei pannelli che visualizzano i dati relativi a tali cluster all'interno della pagina dei dettagli della soluzione possono non essere corrette.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Come si abilita la raccolta di log per i contenitori nello spazio dei nomi kube-system tramite Helm?
La raccolta di log dai contenitori nello spazio dei nomi kube-system è disabilitata per impostazione predefinita. È possibile abilitare la raccolta di log impostando una variabile di ambiente in omsagent. Per altre informazioni, vedere la pagina di [panoramica di Monitoraggio di Azure per i contenitori](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) in GitHub. 

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

Quando si esegue una query in Monitoraggio di Azure per i log, i dati avranno l'aspetto seguente:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Per un'analisi approfondita del problema, vedere il [collegamento a GitHub](https://github.com/moby/moby/issues/22920) seguente.

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Come si risolvono gli errori di Azure Active Directory quando si abilitano i log in tempo reale? 
Può essere visualizzato l'errore seguente: **The reply url specified in the request does not match the reply urls configured for the application: '60b4dec7-5a69-4165-a211-12c40b5c0435'** (L'URL di risposta specificato nella richiesta non corrisponde agli URL di risposta configurati per l'applicazione: '60b4dec7-5a69-4165-a211-12c40b5c0435'). La risoluzione di questo errore è disponibile nell'articolo [Come visualizzare i log dei contenitori in tempo reale con Monitoraggio di Azure per i contenitori](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a monitorare un cluster servizio Azure Kubernetes, esaminare [Come eseguire l'onboarding di Monitoraggio di Azure per contenitori](container-insights-onboard.md) per conoscere i requisiti e i metodi disponibili per abilitare il monitoraggio. 