---
title: Usare Application Insights per monitorare il servizio Azure Kubernetes (AKS) o altre applicazioni ospitate in Kubernetes-monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure usa la tecnologia mesh di servizi, Istio, nel cluster Kubernetes per fornire il monitoraggio delle applicazioni per qualsiasi applicazione ospitata in Kubernetes. In questo modo è possibile raccogliere Application Insights la telemetria relativa alle richieste in ingresso e in uscita verso e da Pod in esecuzione nel cluster.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3056b6c56be32cf5c054c4526a88157650a3e30b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820769"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Monitoraggio dell'applicazione con zero strumentazione per applicazioni ospitate in Kubernetes

> [!IMPORTANT]
> Questa funzionalità è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Monitoraggio di Azure sfrutta ora il servizio Mesh Tech nel cluster Kubernetes per fornire il monitoraggio delle applicazioni per qualsiasi app ospitata in Kubernetes. Con le funzionalità predefinite di Application Insight, come la [mappa delle applicazioni](../../azure-monitor/app/app-map.md) , per modellare le dipendenze, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) per il monitoraggio in tempo reale, visualizzazioni potenti con il [dashboard predefinito](../../azure-monitor/app/overview-dashboard.md), [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md)e [ Cartelle di lavoro](../../azure-monitor/app/usage-workbooks.md)di. Questa funzionalità consentirà agli utenti di individuare i colli di bottiglia delle prestazioni e gli hotspot di errore in tutti i carichi di lavoro Kubernetes all'interno di uno spazio dei nomi Kubernetes selezionato. Grazie alla possibilità di sfruttare gli investimenti esistenti di mesh Service con tecnologie come Istio, monitoraggio di Azure consente il monitoraggio delle app con strumentazione automatica senza alcuna modifica al codice dell'applicazione.

> [!NOTE]
> Questo è uno dei numerosi modi per eseguire il monitoraggio delle applicazioni in Kubernetes. È anche possibile instrumentare qualsiasi app ospitata in Kubernetes usando il [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md) senza che sia necessaria una mesh di servizi. Per monitorare Kubernetes senza instrumentare l'applicazione con un SDK, è possibile usare il metodo seguente.

## <a name="prerequisites"></a>Prerequisiti

- Un [cluster Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Accesso alla console per il cluster per eseguire *kubectl*.
- Una [risorsa Application Insights](create-new-resource.md)
- Disporre di una rete mesh di servizi. Se nel cluster non è distribuito Istio, è possibile apprendere come [installare e usare Istio nel servizio Kubernetes di Azure](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Capabilities

Usando il monitoraggio dell'applicazione con zero strumentazione per le app ospitate in Kubernetes, sarà possibile usare:

- [Mappa delle applicazioni](../../azure-monitor/app/app-map.md)
- [Metriche di Live Stream](../../azure-monitor/app/live-stream.md)
- [Dashboard](../../azure-monitor/app/overview-dashboard.md)
- [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md)
- [Analisi distribuita](../../azure-monitor/app/distributed-tracing.md)
- [Monitoraggio delle transazioni end-to-end](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Procedura di installazione

Per abilitare la soluzione, verranno eseguiti i passaggi seguenti:
- Distribuire l'applicazione (se non è già stata distribuita).
- Assicurarsi che l'applicazione faccia parte della rete mesh del servizio.
- Osservare i dati di telemetria raccolti.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurare l'app per l'uso con una mesh di servizi

Istio supporta due modi per [instrumentare un pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Nella maggior parte dei casi, è più semplice contrassegnare lo spazio dei nomi Kubernetes contenente l'applicazione con l'etichetta *Istio-Injection* :

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Poiché il servizio mesh solleva i dati fuori rete, non è possibile intercettare il traffico crittografato. Per il traffico che non lascia il cluster, usare un protocollo non crittografato (ad esempio, HTTP). Per il traffico esterno che deve essere crittografato, è consigliabile [configurare la terminazione SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) al controller di ingresso.

Le applicazioni in esecuzione all'esterno della mesh del servizio non sono interessate.

### <a name="deploy-your-application"></a>Distribuire l'applicazione

- Distribuire l'applicazione nello spazio dei nomi *My-App-namespace* . Se l'applicazione è già stata distribuita ed è stato seguito il metodo di injection sidecar automatico descritto in precedenza, è necessario ricreare i pod per assicurarsi che Istio inserisca il sidecar; avviare un aggiornamento in sequenza o eliminare singoli pod e attendere che vengano ricreati.
- Assicurarsi che l'applicazione sia conforme ai [requisiti di Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Distribuire il monitoraggio dell'applicazione di strumentazione zero per le app ospitate in Kubernetes

1. Scaricare ed estrarre una [versione di *Adapter Application Insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Passare a */src/kubernetes/* all'interno della cartella Release.
3. Modificare *Application-Insights-Istio-mixer-Adapter-Deployment. YAML*
    - modificare il valore della variabile di ambiente *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* in modo che contenga la chiave di strumentazione della risorsa Application Insights in portale di Azure per contenere i dati di telemetria.
    - Se necessario, modificare il valore della variabile di ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* in modo che contenga un elenco delimitato da virgole di spazi dei nomi per cui si vuole abilitare il monitoraggio. Lasciare vuoto il campo per monitorare tutti gli spazi dei nomi.
4. Applicare *ogni* file YAML trovato in *src/kubernetes/* eseguendo il comando seguente (è ancora necessario trovarsi all'interno di */src/kubernetes/* ):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificare la distribuzione

- Verificare che Application Insights Adapter sia stato distribuito:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> In alcuni casi, è richiesta l'ottimizzazione dell'ottimizzazione. Per includere o escludere i dati di telemetria per un singolo pod dalla raccolta, usare l'etichetta *appinsights/Monitoring. Enabled* nel pod. Questa operazione avrà la priorità su tutte le configurazioni basate su spazio dei nomi. Impostare *appinsights/Monitoring. Enabled* su *true* per includere il pod e su *false* per escluderlo.

### <a name="view-application-insights-telemetry"></a>Visualizza Application Insights telemetria

- Generare una richiesta di esempio sull'applicazione per verificare il corretto funzionamento del monitoraggio.
- Entro 3-5 minuti è necessario iniziare a visualizzare i dati di telemetria nella portale di Azure. Assicurarsi di consultare la sezione *mappa delle applicazioni* della risorsa Application Insights nel portale.

## <a name="troubleshooting"></a>risoluzione dei problemi

Di seguito è riportato il flusso di risoluzione dei problemi da usare quando i dati di telemetria non vengono visualizzati nel portale di Azure come previsto.

1. Assicurarsi che l'applicazione sia sotto carico e che invii/riceva richieste in HTTP normale. Dato che la telemetria viene sollevata dalla rete, il traffico crittografato non è supportato. Se non sono presenti richieste in ingresso o in uscita, non saranno presenti dati di telemetria.
2. Verificare che la chiave di strumentazione corretta sia disponibile nella variabile di ambiente *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* in *Application-Insights-ISTIO-mixer-Adapter-Deployment. YAML*. La chiave di strumentazione si trova nella scheda *Panoramica* della risorsa Application Insights nel portale di Azure.
3. Verificare che nella variabile di ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* sia disponibile lo spazio dei nomi Kubernetes corretto in *Application-Insights-ISTIO-mixer-Adapter-Deployment. YAML*. Lasciare vuoto il campo per monitorare tutti gli spazi dei nomi.
4. Assicurarsi che i pod dell'applicazione siano stati inseriti in sidecar da Istio. Verificare che il sidecar di Istio esista in ogni pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verificare che sia presente un contenitore denominato *Istio-proxy* in esecuzione sul Pod.

5. Visualizzare le tracce dell'adapter Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Il numero di elementi di telemetria ricevuti viene aggiornato una volta al minuto. Se non aumenta al minuto, non viene inviato alcun telemetria alla scheda da Istio.
   Individuare eventuali errori nel log.
6. Se è stato stabilito che *Application Insights for Kubernetes* adapter non viene alimentato la telemetria, controllare i log del mixer di Istio per capire perché non invia i dati all'adapter:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Cercare eventuali errori, in particolare per quanto riguarda le comunicazioni con l'adattatore *applicationinsightsadapter* .

## <a name="faq"></a>FAQ

Per le informazioni più aggiornate per lo stato di avanzamento del progetto, visitare il [Application Insights Adapter per il progetto Istio mixer per GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Disinstallare

Per disinstallare il prodotto, per *ogni* file YAML trovato in *src/kubernetes/* Run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul funzionamento di monitoraggio di Azure e dei contenitori, vedere [Panoramica di monitoraggio di Azure per i contenitori](../../azure-monitor/insights/container-insights-overview.md)