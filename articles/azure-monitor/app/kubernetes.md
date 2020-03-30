---
title: Usare Application Insights per monitorare il servizio Azure Kubernetes (AKS) o altre applicazioni ospitate da Kubernetes, ovvero Monitoraggio di Azure. Documenti Microsoft
description: Monitoraggio di Azure usa la tecnologia mesh del servizio, Istio, nel cluster Kubernetes per fornire il monitoraggio delle applicazioni per qualsiasi applicazione ospitata Kubernetes.Azure Monitor uses service mesh technology, Istio, on your Kubernetes cluster to provide application monitoring for any Kubernetes hosted application. In questo modo è possibile raccogliere dati di telemetria di Application Insights relativi alle richieste in ingresso e in uscita da e verso i pod in esecuzione nel cluster.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132353"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Monitoraggio delle applicazioni di strumentazione zero per le applicazioni ospitate Kubernetes

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Monitoraggio di Azure ora sfrutta la tecnologia mesh del servizio nel cluster Kubernetes per fornire il monitoraggio delle applicazioni out-of-the-box per qualsiasi app ospitata Kubernetes. Con le funzionalità predefinite di Application Insight come [Application Map](../../azure-monitor/app/app-map.md) per modellare le dipendenze, Live [Metrics Stream](../../azure-monitor/app/live-stream.md) per il monitoraggio in tempo reale, visualizzazioni potenti con il dashboard [predefinito,](../../azure-monitor/app/overview-dashboard.md) [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md)e [Cartelle di lavoro](../../azure-monitor/app/usage-workbooks.md). Questa funzionalità consente agli utenti di individuare i colli di bottiglia delle prestazioni e gli hotspot di errore in tutti i carichi di lavoro di Kubernetes all'interno di uno spazio dei nomi Kubernetes selezionato. Sfruttando gli investimenti mesh del servizio esistenti con tecnologie come Istio, Monitoraggio di Azure consente il monitoraggio automatico delle app senza alcuna modifica al codice dell'applicazione.

> [!NOTE]
> Questo è uno dei tanti modi per eseguire il monitoraggio delle applicazioni su Kubernetes.Puoi anche instrumentare qualsiasi app ospitata in Kubernetes usando [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) senza la necessità di una rete mesh del servizio. Per monitorare Kubernetes senza instrumentare l'applicazione con un SDK è possibile utilizzare il metodo seguente.

## <a name="prerequisites"></a>Prerequisiti

- Un [cluster Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Accesso console al cluster per eseguire *kubectl*.
- Una [risorsa di Application Insight](create-new-resource.md)
- Disporre di una rete di servizi. Se nel cluster non è distribuito Istio, è possibile imparare a [installare e usare Istio nel servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Capabilities

Utilizzando zero monitoraggio delle applicazioni di strumentazione per le applicazioni ospitate Kubernetes, sarà possibile utilizzare:

- [Mappa dell'applicazione](../../azure-monitor/app/app-map.md)
- [Metriche in streaming in diretta](../../azure-monitor/app/live-stream.md)
- [Dashboard](../../azure-monitor/app/overview-dashboard.md)
- [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md)
- [Traccia distribuita](../../azure-monitor/app/distributed-tracing.md)
- [Monitoraggio delle transazioni end-to-end](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Procedura di installazione

Per abilitare la soluzione, verranno eseguiti i passaggi seguenti:To enable the solution, we'll be performing the following steps:
- Distribuire l'applicazione (se non è già distribuita).
- Assicurarsi che l'applicazione faccia parte della rete di servizio.
- Osservare i dati di telemetria raccolti.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurare l'app per l'utilizzo di una rete di serviziConfigure your app to work with a service mesh

Istio supporta due modi per [instrumentare un baccello.](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)
Nella maggior parte dei casi, è più semplice contrassegnare lo spazio dei nomi Kubernetes contenente l'applicazione con l'etichetta *istio-injection:In* most cases, it's easiest to mark the Kubernetes namespace containing your application with the istio-injection label:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Poiché la rete mesh di servizio solleva i dati dalla rete, non è possibile intercettare il traffico crittografato. Per il traffico che non lascia il cluster, utilizzare un protocollo non crittografato (ad esempio, HTTP). Per il traffico esterno che deve essere crittografato, è [consigliabile configurare](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) la terminazione TLS nel controller in ingresso.

Le applicazioni in esecuzione all'esterno della rete mesh del servizio non sono interessate.

### <a name="deploy-your-application"></a>Distribuire l'applicazione

- Distribuire l'applicazione nello spazio dei nomi *my-app-namespace.* Se l'applicazione è già distribuita ed è stato seguito il metodo di iniezione sidecar automatico descritto in precedenza, è necessario ricreare i baccelli per assicurarsi che Istio inietti il suo sidecar; avviare un aggiornamento in sequenza o eliminare singoli pod e attendere che vengano ricreati.
- Assicurarsi che l'applicazione sia conforme ai [requisiti Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Distribuire zero il monitoraggio delle applicazioni di strumentazione per le app ospitate KubernetesDeploy zero instrumentation application monitoring for Kubernetes hosted apps

1. Scaricare ed estrarre una versione [ *dell'adattatore di Application Insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Passare a */src/kubernetes/* all'interno della cartella di rilascio.
3. Modificare *application-insights-istio-mixer-adapter-deployment.yaml*
    - Modificare il valore di *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variabile di ambiente per contenere la chiave di strumentazione della risorsa Application Insights nel portale di Azure per contenere i dati di telemetria.
    - Se necessario, modificare il valore di *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variabile di ambiente in modo che contenga un elenco separato da virgole di spazi dei nomi per i quali si desidera abilitare il monitoraggio. Lasciare vuoto per monitorare tutti gli spazi dei nomi.
4. Applicare *ogni* file YAML trovato in *src/kubernetes/* eseguendo quanto segue (è comunque necessario essere all'interno */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificare la distribuzione

- Verificare che l'adapter Application Insights sia stato distribuito:Ensure Application Insights adapter has been deployed:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> In alcuni casi, è necessaria l'ottimizzazione. Per includere o escludere i dati di telemetria per un singolo pod dalla raccolta, usa l'etichetta *appinsights/monitoring.enabled* in quel pod. Questo avrà la priorità su tutta la configurazione basata su spazio dei nomi. Impostare *appinsights/monitoring.enabled* su *true* per includere il pod e *su false* per escluderlo.

### <a name="view-application-insights-telemetry"></a>Visualizzare i dati di telemetria di Application InsightsView Application Insights telemetry

- Generare una richiesta di esempio sull'applicazione per verificare che il monitoraggio funzioni correttamente.
- Entro 3-5 minuti, è consigliabile iniziare a visualizzare i dati di telemetria nel portale di Azure.Within 3-5 minutes, you should start see telemetry appear in the Azure portal. Assicurarsi di estrarre la sezione *Mappa applicazioni* della risorsa Application Insights nel portale.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito è riportato il flusso di risoluzione dei problemi da usare quando i dati di telemetria non vengono visualizzati nel portale di Azure come previsto.

1. Assicurarsi che l'applicazione sia sotto carico e stia inviando/ricevendo richieste in http semplice. Poiché la telemetria viene sollevata dalla rete, il traffico crittografato non è supportato. Se non sono presenti richieste in ingresso o in uscita, non saranno presenti dati di telemetria.
2. Verificare che nella ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY variabile di *ambiente* in *application-insights-istio-mixer-adapter-deployment.yaml*sia fornita la chiave di strumentazione corretta. La chiave di strumentazione è disponibile nella scheda *Panoramica* della risorsa Application Insights nel portale di Azure.The instrumentation key found on the Overview tab of the Application Insights resource in the Azure portal.
3. Verificare che nello spazio dei nomi Kubernetes sia disponibile lo spazio dei nomi Kubernetes corretto *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* in *application-insights-istio-mixer-adapter-deployment.yaml*. Lasciare vuoto per monitorare tutti gli spazi dei nomi.
4. Assicurati che i pod dell'applicazione siano stati iniettati da sidecar da Istio. Verificate che il sidecar di Istio esista su ogni baccello.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verificare che nel pod sia in esecuzione un contenitore denominato *istio-proxy.*

5. Visualizzare le tracce dell'adapter Application Insights.View the Application Insights adapter's traces.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Il conteggio degli elementi di telemetria ricevuti viene aggiornato una volta al minuto. Se non cresce minuto per minuto - nessun telemetryo viene inviato all'adapter da Istio.If it doesn't grow minute over minute - no telemetry is being sent to the adapter by Istio.
   Cercare eventuali errori nel registro.
6. Se è stato stabilito che *l'adattatore Application Insight per Kubernetes* non viene alimentato telemetria, controllare i registri Mixer di Istio per capire perché non sta inviando dati all'adapter:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Cercare eventuali errori, in particolare relativi alle comunicazioni con *l'adapter applicationinsightsadapter.Look* for any errors, especially relative to communications with applicationinsightsadapter adapter.

## <a name="faq"></a>Domande frequenti

Per informazioni aggiornate sullo stato di avanzamento di questo progetto, visitare [l'adattatore Application Insights per GitHub del progetto Istio Mixer.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)

## <a name="uninstall"></a>Disinstallare

Per disinstallare il prodotto, per *ogni* file YAML trovato in *src/kubernetes/* eseguire:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come Azure Monitor e i contenitori collaborano, vedere Panoramica di [Monitoraggio di Azure per i contenitoriTo](../../azure-monitor/insights/container-insights-overview.md) learn more about how Azure Monitor and containers work together visit Azure Monitor for containers overview
