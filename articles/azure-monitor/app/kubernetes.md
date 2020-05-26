---
title: Usare Application Insights per monitorare il servizio Azure Kubernetes o altre applicazioni ospitate in Kubernetes - Monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure usa la tecnologia della rete mesh di servizi Istio nel cluster Kubernetes per fornire il monitoraggio delle applicazioni per qualsiasi applicazione ospitata in Kubernetes. In questo modo è possibile raccogliere i dati di telemetria di Application Insights relativi alle richieste in ingresso e in uscita verso e dai pod in esecuzione nel cluster.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: c3b2b4e8868b73e22706c684d94371fe37885b9a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119255"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Monitoraggio dell'applicazione senza strumentazione per applicazioni ospitate in Kubernetes con Istio: DEPRECATO

> [!IMPORTANT]
> Questa funzionalità è attualmente deprecata e non verrà più supportata dopo il 1 agosto 2020.
> La versione attualmente consigliata funziona solo per [Java tramite agente autonomo](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).

Monitoraggio di Azure usa ora la tecnologia della rete mesh di servizi nel cluster Kubernetes per fornire il monitoraggio delle applicazioni predefinito per qualsiasi applicazione ospitata in Kubernetes. Con funzionalità predefinite di Application Insight come la [mappa delle applicazioni](../../azure-monitor/app/app-map.md) per modellare le dipendenze, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) per il monitoraggio in tempo reale, le visualizzazioni avanzate con il [dashboard predefinito](../../azure-monitor/app/overview-dashboard.md), [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md) e [cartelle di lavoro](../../azure-monitor/app/usage-workbooks.md). Questa funzionalità consente agli utenti di individuare i colli di bottiglia delle prestazioni e gli hotspot di errore in tutti i carichi di lavoro Kubernetes all'interno dello spazio dei nomi Kubernetes selezionato. Grazie alla possibilità di sfruttare gli investimenti nella rete mesh di servizi esistenti con tecnologie come Istio, Monitoraggio di Azure consente di monitorare le app con strumentazione automatica senza apportare modifiche al codice dell'applicazione.

> [!NOTE]
> Si tratta di uno dei numerosi metodi per eseguire il monitoraggio delle applicazioni in Kubernetes. È anche possibile instrumentare qualsiasi app ospitata in Kubernetes usando [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) senza la necessità di una rete mesh di servizi. Per monitorare Kubernetes senza instrumentare l'applicazione con un SDK è possibile usare il metodo seguente.

## <a name="prerequisites"></a>Prerequisiti

- Un [cluster Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Accesso alla console per il cluster per l'esecuzione di *kubectl*.
- Una [risorsa di Application Insights](create-new-resource.md)
- Una rete mesh di servizi. Se nel cluster non è stato distribuito Istio, è possibile ottenere informazioni su come [installare e usare Istio nel servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Capabilities

Usando il monitoraggio dell'applicazione senza strumentazione per le applicazioni ospitate in Kubernetes, sarà possibile usare:

- [Mappa delle applicazioni](../../azure-monitor/app/app-map.md)
- [Metriche di Live Stream](../../azure-monitor/app/live-stream.md)
- [Dashboard](../../azure-monitor/app/overview-dashboard.md)
- [Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md)
- [Analisi distribuita](../../azure-monitor/app/distributed-tracing.md)
- [Rilevamento delle transazioni end-to-end](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Procedura di installazione

Per abilitare la soluzione è necessario seguire la procedura seguente:
- Distribuire l'applicazione (se non è già stata distribuita).
- Verificare che l'applicazione faccia parte della rete mesh di servizi.
- Osservare i dati di telemetria raccolti.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurare l'app per l'uso con una rete mesh di servizi

Istio supporta due metodi per [instrumentare un pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Nella maggior parte dei casi, è più semplice contrassegnare lo spazio dei nomi Kubernetes contenente l'applicazione con l'etichetta *istio-injection*:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Poiché la rete mesh di servizi trae i dati dalla rete, non è possibile intercettare il traffico crittografato. Per il traffico interno al cluster usare un protocollo non crittografato (ad esempio HTTP). Per il traffico esterno che deve essere crittografato, valutare la [configurazione della terminazione TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) sul controller di ingresso.

Le applicazioni che vengono eseguite all'esterno della rete mesh di servizi non vengono influenzate dall'operazione.

### <a name="deploy-your-application"></a>Distribuire l'applicazione

- Distribuire l'applicazione nello spazio dei nomi *my-app-namespace*. Se l'applicazione è già stata distribuita ed è stato applicato il metodo di inserimento collaterale automatico descritto in precedenza, è necessario ricreare i pod per assicurarsi che Istio inserisca il file collaterale. Avviare un aggiornamento in sequenza o eliminare i pod singolarmente, quindi attendere che vengano ricreati.
- Verificare che l'applicazione sia conforme ai [requisiti di Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Distribuire il monitoraggio dell'applicazione senza strumentazione per le app ospitate in Kubernetes

1. Scaricare ed estrarre una [*versione dell'adattatore di Application Insights*](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Passare a */src/kubernetes/* all'interno della cartella della versione.
3. Modificare *application-insights-istio-mixer-adapter-deployment.yaml*
    - Modificare il valore della variabile di ambiente *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* per contenere la chiave di strumentazione della risorsa di Application Insights nel portale di Azure per contenere la telemetria.
    - Se necessario, modificare il valore della variabile di ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* per contenere l'elenco di spazi dei nomi separato da virgole per il quale si vuole attivare il monitoraggio. Per il monitoraggio di tutti gli spazi dei nomi, lasciare la variabile vuota.
4. Applicare *ogni* file YAML presente in *src/kubernetes/* eseguendo quanto segue (è necessario rimanere all'interno di */src/kubernetes/* ):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificare la distribuzione

- Verificare che l'adattatore di Application Insights sia stato distribuito:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> In alcuni casi, è richiesta l'ottimizzazione. Per includere o escludere la raccolta di dati di telemetria per un singolo pod, usare l'etichetta *appinsights/monitoring.enabled* sul pod in questione. Questa operazione avrà priorità su tutte le configurazioni basate sullo spazio dei nomi. Impostare *appinsights/monitoring.enabled* su *true* per includere il pod oppure su *false* per escluderlo.

### <a name="view-application-insights-telemetry"></a>Visualizzare i dati di telemetria di Application Insights

- Generare una richiesta di esempio per l'applicazione per confermare che il monitoraggio funziona correttamente.
- Entro 3-5 minuti si dovrebbero iniziare a visualizzare i dati di telemetria nel portale di Azure. Nel portale, assicurarsi di consultare la sezione *Mappa delle applicazioni* della risorsa Application Insights.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito è riportato il flusso di risoluzione dei problemi da usare quando i dati di telemetria non vengono visualizzati nel portale di Azure come previsto.

1. Verificare che l'applicazione sia in condizioni di carico e che invii/riceva richieste in HTTP normale. Dato che la telemetria viene tratta dalla rete, il traffico crittografato non è supportato. In assenza di richieste in entrata o in uscita, non saranno visualizzati neanche i dati di telemetria.
2. Verificare di aver fornito la chiave di strumentazione corretta nella variabile di ambiente *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* in *application-insights-istio-mixer-adapter-deployment.yaml*. La chiave di strumentazione si trova nella scheda *Panoramica* della risorsa di Application Insights nel portale di Azure.
3. Verificare di aver fornito lo spazio dei nomi di Kubernetes corretto nella variabile di ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* in *application-insights-istio-mixer-adapter-deployment.yaml*. Per il monitoraggio di tutti gli spazi dei nomi, lasciare la variabile vuota.
4. Assicurarsi che i pod dell'applicazione siano stati inseriti in modo collaterale da Istio. Verificare che il file collaterale di Istio sia presente in ogni pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verificare che sia presente un contenitore denominato *istio-proxy* in esecuzione sul pod.

5. Visualizzare le tracce dell'adattatore di Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Il numero di voci di telemetria ricevute viene aggiornato una volta al minuto. Se non aumenta al trascorrere dei minuti, Istio non invia nessun dato di telemetria all'adattatore.
   Individuare eventuali errori nel log.
6. Se è stato stabilito che l'adattatore di *Application Insights per Kubernetes* non sta ricevendo dati di telemetria, controllare i log del Mixer di Istio per capire perché i dati non vengono inviati all'adattatore:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Individuare eventuali errori, specialmente pertinenti alle comunicazioni con l'adattatore *applicationinsightsadapter*.

## <a name="faq"></a>Domande frequenti

Per le informazioni più aggiornate sullo stato di avanzamento del progetto, visitare il [GitHub del progetto Adattatore di Application Insights per Istio Mixer](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Disinstallare

Per disinstallare il prodotto, per *ogni* file YAML individuato in *src/kubernetes/* eseguire:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul funzionamento congiunto di Monitoraggio di Azure e dei contenitori, visita la pagina [Panoramica di Monitoraggio di Azure per i contenitori](../../azure-monitor/insights/container-insights-overview.md)
