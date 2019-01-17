---
title: Registrazione di istanze di contenitore con Azure Log Analytics
description: Informazioni su come inviare l'output dei contenitori (STDOUT e STDERR) ad Azure Log Analytics.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: 4dbcccc1a4b23ca37918495dc536df08a70cade7
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337887"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>Registrazione di istanze di contenitore con Azure Log Analytics

Le aree di lavoro di Log Analytics offrono una posizione centralizzata per l'archiviazione e l'esecuzione di query sui dati di log, non solo dalle risorse di Azure, ma anche per le risorse locali e quelle in altri cloud. Il servizio Istanze di Azure Container supporta per impostazione predefinita l'invio di dati a Log Analytics.

Per inviare i dati delle istanze di contenitore a Log Analytics, è necessario creare un gruppo di contenitori usando l'interfaccia della riga di comando di Azure (o Cloud Shell) e un file YAML. Le sezioni seguenti descrivono la creazione di un gruppo di contenitori abilitato per la registrazione e l'esecuzione di query sui log.

## <a name="prerequisites"></a>Prerequisiti

Per abilitare la registrazione nelle istanze di contenitore, è necessario quanto segue:

* [Area di lavoro di Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (o [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Ottenere le credenziali di Log Analytics

Il servizio Istanze di Azure Container deve avere l'autorizzazione per l'invio dei dati all'area di lavoro di Log Analytics. Per concedere questa autorizzazione e abilitare la registrazione, è necessario fornire l'ID dell'area di lavoro di Log Analytics e una delle relative chiavi (primaria o secondaria) quando si crea il gruppo di contenitori.

Per ottenere l'ID e la chiave primaria dell'area di lavoro di Log Analytics:

1. Passare all'area di lavoro di Log Analytics nel portale di Azure
1. Selezionare **Impostazioni avanzate** in **IMPOSTAZIONI**
1. Selezionare **Origini connesse** > **Windows Server** (o **Server Linux** - l'ID e le chiavi sono gli stessi per entrambe le origini)
1. Prendere nota di:
   * **ID AREA DI LAVORO**
   * **CHIAVE PRIMARIA**

## <a name="create-container-group"></a>Creare un gruppo di contenitori

Dopo aver ottenuto l'ID e la chiave primaria dell'area di lavoro di Log Analytics, è possibile procedere con la creazione di un gruppo di contenitori abilitato per la registrazione.

Gli esempi seguenti illustrano due modi per creare un gruppo di contenitori con un unico contenitore [fluentd][fluentd]: interfaccia della riga di comando di Azure e interfaccia della riga di comando di Azure con un modello YAML. Il contenitore Fluentd produce più righe di output nella configurazione predefinita. Dato che questo output viene inviato all'area di lavoro di Log Analytics, è adatto per dimostrare le funzionalità di visualizzazione ed esecuzione di query per i log.

### <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per eseguire la distribuzione con l'interfaccia della riga di comando di Azure, specificare i parametri `--log-analytics-workspace` e `--log-analytics-workspace-key` nel comando [az container create][az-container-create]. Sostituire i due valori dell'area di lavoro con i valori ottenuti nel passaggio precedente (e aggiornare il nome del gruppo di risorse) prima di eseguire il comando seguente.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Distribuire con YAML

Usare questo metodo se si preferisce distribuire gruppi di contenitori con YAML. Il codice YAML seguente definisce un gruppo di contenitori con un singolo contenitore. Copiare il codice YAML in un nuovo file e quindi sostituire `LOG_ANALYTICS_WORKSPACE_ID` e `LOG_ANALYTICS_WORKSPACE_KEY` con i valori ottenuti nel passaggio precedente. Salvare il file come **deploy-aci.yaml**.

```yaml
apiVersion: 2018-06-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Successivamente, eseguire il comando seguente per distribuire il gruppo di contenitori. Sostituire `myResourceGroup` con un gruppo di risorse nella sottoscrizione oppure creare prima un gruppo di risorse denominato "myResourceGroup":

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Si riceverà una risposta da Azure contenente i dettagli di distribuzione poco dopo aver eseguito il comando.

## <a name="view-logs-in-log-analytics"></a>Visualizzare i log in Log Analytics

Dopo aver distribuito il gruppo di contenitori, possono essere necessari diversi minuti (fino a 10) per la visualizzazione delle prime voci di log nel portale di Azure. Per visualizzare i log del gruppo di contenitori, aprire l'area di lavoro di Log Analytics e quindi:

1. Nella panoramica dell'**area di lavoro OMS** selezionare **Ricerca log**. Le aree di lavoro OMS sono ora denominate aree di lavoro di Log Analytics.  
1. In **Query di esempio per provare** selezionare il collegamento **Tutti i dati raccolti**

Dovrebbero essere visibili vari risultati visualizzati dalla query`search *`. Se inizialmente non viene visualizzato alcun risultato, attendere qualche minuto e quindi selezionare il pulsante **Esegui** per eseguire nuovamente la query. Per impostazione predefinita, le voci di log vengono visualizzate nella visualizzazione "Elenco". Selezionare **Tabella** per visualizzare le voci di log in un formato più compresso. È quindi possibile espandere una riga per visualizzare il contenuto di una singola voce del log.

![Risultati di Ricerca log nel portale di Azure][log-search-01]

## <a name="query-container-logs"></a>Eseguire query sui log dei contenitori

Log Analytics include un [linguaggio di query][query_lang] completo per estrarre informazioni potenzialmente da migliaia di righe di output del log.

L'agente di registrazione di Istanze di Azure Container invia le voci alla tabella `ContainerInstanceLog_CL` nell'area di lavoro di Log Analytics. La struttura di base di una query è costituita da una tabella di origine (`ContainerInstanceLog_CL`) seguita da una serie di operatori separati dal carattere barra verticale (`|`). È possibile concatenare più operatori per limitare i risultati ed eseguire funzioni avanzate.

Per visualizzare risultati di query di esempio, incollare la query seguente nella casella di testo di query (in "Mostra convertitore linguaggio legacy") e selezionare il pulsante **Esegui** per eseguire la query. Questa query consente di visualizzare tutte le voci di log il cui campo "Message" contiene la parola "warn":

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Sono supportate anche query più complesse. Ad esempio, questa query visualizza solo le voci di log per il gruppo di contenitori "mycontainergroup001" generate nell'ultima ora:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="log-analytics"></a>Log Analytics

Per altre informazioni sull'esecuzione di query nei log e la configurazione di avvisi in Azure Log Analytics, vedere:

* [Informazioni sulle ricerche log in Log Analytics](../log-analytics/log-analytics-log-search.md)
* [Avvisi unificati in Monitoraggio di Azure](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorare CPU e memoria del contenitore

Per informazioni sul monitoraggio delle risorse di CPU e memoria per l'istanza di contenitore, vedere:

* [Monitorare le risorse dei contenitori in Istanze di contenitore di Azure](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create