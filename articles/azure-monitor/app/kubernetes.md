---
title: Usare Application Insights per il monitoraggio di Azure Kubernetes Service (AKS) o altri Kubernetes ospitato applications - monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure Usa la tecnologia mesh del servizio, Istio, nel cluster Kubernetes per fornire il monitoraggio delle applicazioni per qualsiasi applicazione in Kubernetes ospitato. In questo modo è possibile raccogliere i dati di telemetria di Application Insights relativi a richieste in ingresso e in uscita da e verso i POD in esecuzione nel cluster.
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: c94d589875195207ec6f71c35ad077cac281fda5
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65555828"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Zero monitoraggio strumentazione dell'applicazione per Kubernetes ospitato applicazioni

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Monitoraggio di Azure sfrutta ora tech mesh del servizio nel cluster Kubernetes per fornire il monitoraggio dell'applicazione casella per qualsiasi app Kubernetes ospitato. Con impostazione predefinita le funzionalità di Application Insights come [mappa delle applicazioni](../../azure-monitor/app/app-map.md) per modellare le dipendenze [Live Stream metriche](../../azure-monitor/app/live-stream.md) per il monitoraggio in tempo reale, visualizzazioni avanzate con il [predefinito dashboard](../../azure-monitor/app/overview-dashboard.md), [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md), e [cartelle di lavoro](../../azure-monitor/app/usage-workbooks.md). Questa funzionalità consentirà agli utenti individuare eventuali colli di bottiglia e aree sensibili di errore in tutti i carichi di lavoro di Kubernetes all'interno di uno spazio dei nomi Kubernetes selezionato. Sfruttando i tuoi investimenti esistenti mesh di servizi con tecnologie quali Istio, monitoraggio di Azure consente il monitoraggio di app instrumentata saranno automaticamente senza alcuna modifica al codice dell'applicazione.

> [!NOTE]
> Questo è uno dei molti modi per eseguire il monitoraggio delle applicazioni in Kubernetes. È anche possibile instrumentare qualsiasi app ospitate in Kubernetes usando il [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md) senza la necessità di una rete mesh di servizi. Monitorare Kubernetes senza la strumentazione dell'applicazione con un SDK è possibile usare il seguente metodo.

## <a name="prerequisites"></a>Prerequisiti

- Oggetto [cluster Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Console di accesso al cluster per eseguire *kubectl*.
- Un [risorsa di Application Insights](create-new-resource.md)
- Disporre di una rete mesh di servizi. Se il cluster non dispone di Istio distribuito, sarà possibile apprendere come [installare e usare Istio in Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Funzionalità

Con zero applicazione di strumentazione di monitoraggio per Kubernetes App ospitate, sarà possibile usare:

- [Mappa delle applicazioni](../../azure-monitor/app/app-map.md)
- [Metriche di Live Stream](../../azure-monitor/app/live-stream.md)
- [Dashboard](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Traccia distribuita](../../azure-monitor/app/distributed-tracing.md)
- [Monitoraggio delle transazioni end-to-end](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Procedura di installazione

Per abilitare la soluzione, è possibile eseguire la procedura seguente:
- Distribuire l'applicazione (se non è già stato distribuito).
- Verificare che l'applicazione fa parte della mesh del servizio.
- Osservare i dati di telemetria raccolti.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurare l'app per lavorare con una rete mesh di servizi

Istio supporta due metodi [strumentazione di un pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Nella maggior parte dei casi, è più semplice contrassegnare lo spazio dei nomi Kubernetes che contiene l'applicazione con il *istio injection* etichetta:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Poiché i dati del servizio rete mesh di ascensori off transito, è possibile intercettare il traffico crittografato. Per il traffico che non lascino il cluster, usare un protocollo non crittografato (ad esempio, HTTP). Per il traffico esterno che deve essere crittografato, prendere in considerazione [configurazione di terminazione SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) al controller di ingresso.

Le applicazioni in esecuzione all'esterno di rete mesh di servizi non sono interessate.

### <a name="deploy-your-application"></a>Distribuire l'applicazione

- Distribuire l'applicazione *-app-spazio dei nomi my* dello spazio dei nomi. Se l'applicazione è già stata distribuita e aver eseguito il metodo di inserimento automatico sidecar descritto in precedenza, è necessario ricreare i POD per garantire che istio inserisce il sidecar; avviare un aggiornamento in sequenza o eliminare singole POD e attenderne la ricreazione.
- Verificare che l'applicazione è conforme [Istio requisiti](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Distribuzione zero applicazione di strumentazione di monitoraggio per Kubernetes App ospitate

1. Scaricare ed estrarre un' [ *scheda di Application Insights* rilasciare](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Passare a */src/kubernetes/* all'interno della cartella di rilascio.
3. Modifica *application-insights-istio-mixer-adapter-deployment.yaml*
    - modificare il valore della *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variabile di ambiente per contenere la chiave di strumentazione della risorsa di Application Insights nel portale di Azure per contenere i dati di telemetria.
    - Se necessario, modificare il valore della *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variabile di ambiente conterrà un elenco delimitato da virgole degli spazi dei nomi per il quale si desidera abilitare il monitoraggio. Lasciare vuoto per monitorare tutti gli spazi dei nomi.
4. Si applicano *ogni* trovare il file YAML sotto *src/kubernetes/* eseguendo le operazioni seguenti (è necessario comunque essere all'interno */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verifica distribuzione

- Assicurarsi che l'adapter di Application Insights è stato distribuito:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> In alcuni casi, l'ottimizzazione di ottimizzazione è obbligatorio. Per includere o escludere i dati di telemetria per un singolo pod da raccolti, usare *appinsights/monitoring.enabled* etichetta in quel pod. Questo avrà priorità su tutte le configurazioni basate su spazio dei nomi. Impostare *appinsights/monitoring.enabled* al *true* da includere nel pod e di ottenere *false* per escluderlo.

### <a name="view-application-insights-telemetry"></a>Visualizzare i dati di telemetria di Application Insights

- Generare una richiesta di esempio contro la tua applicazione per verificare che il monitoraggio funzioni correttamente.
- Entro 3-5 minuti, è consigliabile iniziare a visualizzare i dati di telemetria vengono visualizzati nel portale di Azure. Assicurarsi di consultare il *mappa delle applicazioni* sezione della risorsa di Application Insights nel portale.

## <a name="troubleshooting"></a>risoluzione dei problemi

Di seguito il flusso di risoluzione dei problemi da utilizzare quando i dati di telemetria non viene visualizzato nel portale di Azure come previsto.

1. Verificare che l'applicazione è sotto carico ed è l'invio/ricezione di richieste in HTTP semplice. Poiché i dati di telemetria è elevato fuori transito, è possibile che il traffico crittografato non è supportato. Se non sono presenti richieste in ingresso o in uscita, vi sarà alcun dato di telemetria sia.
2. Verificare che la chiave di strumentazione corretta viene fornita nel *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variabile di ambiente nel *application-insights-istio-mixer-adapter-deployment.yaml*. La chiave di strumentazione si trova il *Panoramica* scheda della risorsa di Application Insights nel portale di Azure.
3. Verificare che lo spazio dei nomi Kubernetes corretto è disponibile nel *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variabile di ambiente nel *application-insights-istio-mixer-adapter-deployment.yaml*. Lasciare vuoto per monitorare tutti gli spazi dei nomi.
4. Verificare che i POD dell'applicazione sono state inserite sidecar dall'Istio. Verificare l'esistenza di contenitore collaterale dell'Istio su ogni pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verificare che sia presente un contenitore denominato *istio proxy* in esecuzione nel pod.

5. Visualizzare le tracce dell'adapter Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Il conteggio degli elementi di dati di telemetria ricevuti viene aggiornato ogni minuto. Se non aumentano minuto per minuto - Nessun dato di telemetria inviata all'adapter dal Istio.
   Cercare eventuali errori nel log.
6. Se è stato stabilito che *Application Insights per Kubernetes* adapter non è essere inseriti i dati di telemetria, controllare i log di Mixer di Istio capire il motivo per cui non sta inviando dati all'adapter:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Cercare eventuali errori, soprattutto per le comunicazioni con *applicationinsightsadapter* adapter.

## <a name="faq"></a>Domande frequenti

Per informazioni più recenti per lo stato di avanzamento del progetto, visitare il [adapter di Application Insights per GitHub del progetto di Mixer Istio](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Disinstallare

Per disinstallare il prodotto, per *ogni* trovare il file YAML sotto *src/kubernetes/* eseguire:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio di Azure e i contenitori di funzionano di visita [monitoraggio di Azure per una panoramica dei contenitori](../../azure-monitor/insights/container-insights-overview.md)