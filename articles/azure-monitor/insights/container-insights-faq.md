---
title: Domande frequenti su Monitoraggio di Azure per i contenitori | Microsoft Docs
description: Monitoraggio di Azure per i contenitori è una soluzione che consente di monitorare l'integrità dei cluster del servizio Azure Container e di Istanze di Container in Azure. Questo articolo fornisce le risposte alle domande frequenti.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: b0d2be8b573dbbf047f4a27ae9ac9f611b76dc51
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977757"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Domande frequenti su Monitoraggio di Azure per i contenitori

Queste domande frequenti Microsoft sono un elenco di domande comuni su Monitoraggio di Azure per i container. Per altre domande sulla soluzione, visitare il [forum di discussione](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Quando si esegue una query sulla tabella ContainerLog, i valori delle proprietà Image e Name non vengono visualizzati.

Per la versione dell'agente ciprod12042019 e versioni successive, per impostazione predefinita queste due proprietà non vengono popolate per ogni riga di log per ridurre al minimo i costi legati ai dati di log raccolti. Sono disponibili due opzioni per eseguire una query sulla tabella che include queste proprietà con i rispettivi valori:

### <a name="option-1"></a>Opzione 1 

Unire le altre tabelle per includere i valori delle proprietà nei risultati.

Modificare le query in modo da includere le proprietà Image e ImageTag dalla tabella ```ContainerInventory``` tramite join alla proprietà ContainerID. È possibile includere la proprietà Name, come in precedenza nella tabella ```ContainerLog```, dal campo ContaineName della tabella KubepodInventory tramite join alla proprietà ContainerID. Questa è l'opzione consigliata.

Nell'esempio seguente viene illustrata una query dettagliata di esempio in cui viene illustrato come ottenere questi valori di campo con join.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>Opzione 2

Abilitare di nuovo la raccolta per queste proprietà per ogni riga del log del contenitore.

Se la prima opzione non è comoda a causa delle modifiche di query necessarie, è possibile riabilitare la raccolta di questi campi abilitando l'impostazione ```log_collection_settings.enrich_container_logs``` nella mappa di configurazione dell'agente come descritto nelle [impostazioni di configurazione della raccolta dati](./container-insights-agent-config.md).

> [!NOTE]
> La seconda opzione non è consigliata con cluster di grandi dimensioni con più di 50 nodi, perché genera chiamate del server API da ogni nodo > nel cluster per eseguire questa operazione di arricchimento. Questa opzione consente anche di aumentare le dimensioni dei dati per ogni riga di log raccolta.

## <a name="can-i-view-metrics-collected-in-grafana"></a>È possibile visualizzare le metriche raccolte in Grafana?

Il monitoraggio di Azure per i contenitori supporta la visualizzazione delle metriche archiviate nell'area di lavoro Log Analytics nei dashboard di Grafana. È disponibile un modello che è possibile scaricare dal [repository dashboard](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) di Grafana per iniziare e fare riferimento a per informazioni su come eseguire query sui dati aggiuntivi dai cluster monitorati per visualizzare i dashboard Grafana personalizzati. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>È possibile monitorare il cluster del motore AKS con monitoraggio di Azure per i contenitori?

Monitoraggio di Azure per contenitori supporta il monitoraggio dei carichi di lavoro del contenitore distribuiti nei cluster AKS-Engine (in precedenza noti come ACS-Engine) ospitati in Azure. Per altri dettagli e una panoramica dei passaggi necessari per abilitare il monitoraggio per questo scenario, vedere [uso di monitoraggio di Azure per i contenitori per il motore AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Perché non vengono visualizzati i dati nell'area di lavoro Log Analytics?

Se non si riesce a visualizzare i dati nell'area di lavoro Log Analytics in un determinato momento ogni giorno, è possibile che sia stato raggiunto il limite di 500 MB predefinito o il limite giornaliero specificato per controllare la quantità di dati da raccogliere quotidianamente. Quando viene raggiunto il limite per il giorno, la raccolta dei dati si interrompe e riprende solo il giorno successivo. Per esaminare l'utilizzo dei dati ed eseguire l'aggiornamento a un piano tariffario diverso in base ai modelli di utilizzo previsti, vedere l' [utilizzo dei dati di log e i costi](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quali sono gli Stati del contenitore specificati nella tabella ContainerInventory?

La tabella ContainerInventory contiene informazioni sia sui contenitori arrestati che su quelli in esecuzione. La tabella viene popolata da un flusso di lavoro all'interno dell'agente che esegue una query in Docker per tutti i contenitori (in esecuzione e arrestati) e inoltra tali dati nell'area di lavoro Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Ricerca per categorie risolvere un errore di *registrazione della sottoscrizione mancante* ?

Se viene visualizzato un errore di **registrazione della sottoscrizione mancante per Microsoft. OperationsManagement**, è possibile risolverlo registrando il provider di risorse **Microsoft. OperationsManagement** nella sottoscrizione in cui è definita l'area di lavoro. La documentazione che illustra come eseguire questa operazione è disponibile [qui](../../azure-resource-manager/templates/error-register-resource-provider.md).

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

È possibile che venga visualizzato l'errore seguente: **l'URL di risposta specificato nella richiesta non corrisponde agli URL di risposta configurati per l'applicazione:' < ID applicazione\>'** . La soluzione per risolverla è disponibile nell'articolo [come visualizzare i dati del contenitore in tempo reale con monitoraggio di Azure per i contenitori](container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Perché non è possibile aggiornare il cluster dopo l'onboarding?

Se dopo l'abilitazione di monitoraggio di Azure per i contenitori per un cluster AKS si elimina l'area di lavoro Log Analytics cui il cluster stava inviando i dati, quando si tenta di aggiornare il cluster l'operazione avrà esito negativo. Per risolvere il problema, sarà necessario disabilitare il monitoraggio e quindi riabilitarlo facendo riferimento a un'area di lavoro valida diversa nella sottoscrizione. Quando si tenta di eseguire di nuovo l'aggiornamento del cluster, il processo dovrebbe essere elaborato e completato correttamente.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quali porte e domini è necessario aprire/inserire nell'elenco elementi consentiti per l'agente?

Vedere i [requisiti del firewall di rete](container-insights-onboard.md#network-firewall-requirements) per le informazioni di configurazione del proxy e del firewall necessarie per l'agente in contenitori con Azure, Azure US Government e Azure China cloud.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a monitorare un cluster servizio Azure Kubernetes, esaminare [Come eseguire l'onboarding di Monitoraggio di Azure per contenitori](container-insights-onboard.md) per conoscere i requisiti e i metodi disponibili per abilitare il monitoraggio. 
