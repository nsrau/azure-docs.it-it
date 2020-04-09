---
title: Installare Istio nel servizio Azure Kubernetes
description: Informazioni su come installare e usare Istio per creare una rete mesh di servizi in un cluster del servizio Azure Kubernetes
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d1d02cb42a86023e5c341daab678c39f22f75dda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877695"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installare e utilizzare Istio nel servizio Azure Kubernetes

[Istio][istio-github] è una rete mesh di servizi open source che offre un set di funzionalità chiave per i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per maggiori informazioni su Istio, vedere la documentazione ufficiale [Cos'è Istio?][istio-docs-concepts].

Questo articolo illustra come installare Istio. Il file `istioctl` binario del client Istio viene installato nel computer client e i componenti Istio vengono installati in un cluster Kubernetes in AKS.

> [!NOTE]
> Le istruzioni seguenti fanno `1.4.0`riferimento alla versione Istio .
>
> Le versioni `1.4.x` Istio sono state testate dal team Istio `1.13` `1.14`rispetto `1.15`alle versioni di Kubernetes, . Ulteriori versioni di Istio sono disponibili in [GitHub - Istio Releases][istio-github-releases], informazioni su ciascuna delle versioni disponibili in [Istio News][istio-release-notes] e sulle versioni di Kubernetes supportate in [Istio General FAQ][istio-faq].

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Scaricare e installare il file binario del client Istio Istio Istioctl
> * Installare Istio su AKS
> * Convalidare l'installazione di Istio
> * Accedere ai componenti aggiuntivi
> * Disinstallare Istio da AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato `1.13` un cluster AKS (Kubernetes e versioni successive, con RBAC abilitato) e che sia stata stabilita una `kubectl` connessione con il cluster. Se occorre assistenza con uno di questi elementi, vedere la [Guida introduttiva al servizio Azure Kubernetes][aks-quickstart].

Assicurarsi di aver letto la documentazione [Istio Performance and Scalability](https://istio.io/docs/concepts/performance-and-scalability/) per comprendere i requisiti di risorse aggiuntive per l'esecuzione di Istio nel cluster AKS. I requisiti di base e di memoria variano in base al carico di lavoro specifico. Scegliere un numero appropriato di nodi e le dimensioni della macchina virtuale per la configurazione.

Questo articolo suddivide il materiale sussidiario per l'installazione di Istio in diversi passaggi. Il risultato finale presenta una struttura identica al [materiale sussidiario][istio-install-istioctl] ufficiale per l'installazione di Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Installare i componenti Istio su AKS

Installeremo [Grafana][grafana] e [Kiali][kiali] come parte della nostra installazione Istio. Grafana fornisce dashboard di analisi e monitoraggio e Kiali fornisce un dashboard di osservabilità mesh del servizio. Nella nostra configurazione, ognuno di questi componenti richiede credenziali che devono essere fornite come [segreto][kubernetes-secrets].

Prima di poter installare i componenti Istio, dobbiamo creare i segreti sia per Grafana che per Kiali. Questi segreti devono essere `istio-system` installati nello spazio dei nomi che verrà utilizzato da Istio, pertanto è necessario creare anche lo spazio dei nomi. È necessario utilizzare `--save-config` l'opzione quando `kubectl create` si crea lo spazio `kubectl apply` dei nomi tramite in modo che il programma di installazione Istio possa essere eseguito su questo oggetto in futuro.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Installare i componenti Istio

Ora che abbiamo creato con successo i segreti Grafana e Kiali nel nostro cluster AKS, è il momento di installare i componenti Istio. 

L'approccio di installazione [Helm][helm] per Istio sarà deprecato in futuro. Il nuovo approccio di installazione `istioctl` per Istio sfrutta il file binario client, i profili di [configurazione Istio][istio-configuration-profiles]e le nuove specifiche del piano di [controllo Istio e api][istio-control-plane]. Questo nuovo approccio è quello che useremo per installare Istio.

> [!NOTE]
> Istio deve attualmente essere pianificato per l'esecuzione su nodi Linux.Istio currently must be scheduled to run on Linux nodes. Se nel cluster sono presenti nodi di Windows Server, è necessario assicurarsi che i pod Istio siano pianificati solo per l'esecuzione sui nodi Linux. Useremo [i selettori][kubernetes-node-selectors] di nodo per assicurarci che i pod siano pianificati per i nodi corretti.

> [!CAUTION]
> Le funzionalità [SDS (secret discovery service)][istio-feature-sds] e [Istio CNI][istio-feature-cni] Istio si trovano attualmente in [Alpha][istio-feature-stages], quindi è necessario pensare prima di abilitarle. 
>
> Si noti che la funzionalità [di proiezione del][kubernetes-feature-sa-projected-volume] volume di token dell'account del servizio Kubernetes (un requisito per SDS) è ora **abilitata** per tutte le Kubernetes 1.13 e versioni successive in AKS.

Creare un `istio.aks.yaml` file chiamato con il contenuto seguente. Questo file conterrà i dettagli delle specifiche del piano di [controllo Istio][istio-control-plane] per la configurazione di Istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Installare istio `istioctl apply` utilizzando il `istio.aks.yaml` comando e il file di specifiche del piano di controllo Istio sopra quanto segue come segue:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Il programma di installazione distribuirà un numero di [CRD][kubernetes-crd] e quindi gestirà le dipendenze per installare tutti gli oggetti rilevanti definiti per questa configurazione di Istio. Si dovrebbe vedere qualcosa di simile al frammento di codice di output seguente.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

A questo punto, istio è stato distribuito nel cluster AKS. Per garantire una corretta distribuzione di Istio, passiamo alla sezione successiva a [Convalida dell'installazione di Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Convalidare l'installazione di Istio

Verificare innanzitutto che siano stati creati i servizi previsti. Usare il comando [kubectl get svc][kubectl-get] per visualizzare i servizi in esecuzione. Eseguire `istio-system` una query sullo spazio dei nomi in cui `istio` sono stati installati i componenti Istio e add-on dal grafico Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

L'output di esempio indicato di seguito mostra i servizi attualmente in esecuzione:

- Servizi di `istio-*`
- `jaeger-*`, `tracing`, `zipkin` e servizi di traccia dei componenti aggiuntivi
- `prometheus`servizio di metriche dei componenti aggiuntivi
- `grafana`servizio di analisi e monitoraggio del dashboard dei componenti aggiuntivi
- `kiali`servizio dashboard mesh di servizio aggiuntivoAdd-on service mesh dashboard service service

Se `istio-ingressgateway` mostra un indirizzo IP esterno di `<pending>`, attendere alcuni minuti fino a quando la rete di Azure non avrà assegnato un indirizzo IP.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Successivamente, verificare che siano stati creati i pod richiesti. Usate il comando [kubectl get pods][kubectl-get] ed eseguite nuovamente una query sullo `istio-system` spazio dei nomi:

```console
kubectl get pods --namespace istio-system
```

L'output di esempio indicato di seguito illustra i pod in esecuzione:

- i `istio-*` baccelli
- il `prometheus-*` contenitore delle metriche dei componenti aggiuntivi
- il `grafana-*` pod di analisi e dashboard di monitoraggio dei componenti aggiuntivi
- il `kiali` contenitore del dashboard mesh del servizio aggiuntivo

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Tutti i pod devono mostrare `Running`lo stato di . Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se per uno dei pod viene segnalato un problema, usare il comando [kubectl describe pod][kubectl-describe] per esaminare l'output e lo stato.

## <a name="accessing-the-add-ons"></a>Accesso ai componenti aggiuntivi

Un certo numero di componenti aggiuntivi sono stati installati da Istio nella nostra configurazione sopra che forniscono funzionalità aggiuntive. Le applicazioni web per i componenti aggiuntivi **non** sono esposte pubblicamente tramite un indirizzo IP esterno. 

Per accedere alle interfacce utente del `istioctl dashboard` componente aggiuntivo, utilizzare il comando . Questo comando sfrutta [kubectl port-forward][kubectl-port-forward] e una porta casuale per creare una connessione sicura tra il computer client e il pod pertinente nel cluster AKS. Si aprirà quindi automaticamente l'applicazione web add-on nel browser predefinito.

È stato aggiunto un ulteriore livello di sicurezza per Grafana e Kiali specificando le credenziali per loro in precedenza in questo articolo.

### <a name="grafana"></a>Grafana

I dashboard di analisi e monitoraggio per Istio sono forniti da [Grafana.][grafana] Ricordarsi di utilizzare le credenziali create tramite il segreto Grafana in precedenza quando richiesto. Aprire il dashboard Grafana in modo sicuro come segue:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Le metriche per Istio sono fornite da [Prometheus][prometheus]. Aprire il dashboard Prometheus in modo sicuro come segue:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

La traccia all'interno di Istio è fornita da [Jaeger][jaeger]. Aprire il dashboard di Jaeger in modo sicuro come segue:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

La dashboard di osservabilità della rete mesh di servizi è fornita da [Kiali][kiali]. Ricordati di utilizzare le credenziali che hai creato tramite il segreto Kiali in precedenza quando richiesto. Aprire la dashboard di Kiali in modo sicuro come segue:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

È disponibile una semplice interfaccia per i proxy [Envoy.][envoy] Fornisce informazioni di configurazione e metriche per un proxy inviato in esecuzione in un pod specificato. Aprire l'interfaccia di Envoy in modo sicuro come segue:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Disinstallare Istio da AKS

> [!WARNING]
> L'eliminazione di Istio da un sistema in esecuzione può causare problemi relativi al traffico tra i servizi. Assicurarsi di aver effettuato disposizioni per il corretto funzionamento del sistema senza Istio prima di procedere.

### <a name="remove-istio-components-and-namespace"></a>Rimuovere i componenti Istio e lo spazio dei nomi

Per rimuovere Istio dal cluster AKS, utilizzare il `istioctl manifest generate` comando con il `istio.aks.yaml` file di specifiche del piano di controllo Istio. Verrà generato il manifesto distribuito, `kubectl delete` che verrà pipe a per `istio-system` rimuovere tutti i componenti installati e lo spazio dei nomi.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Rimuovere i CRD e i segreti di Istio

I comandi precedenti eliminano tutti i componenti Istio e lo spazio dei nomi, ma ci rimangono ancora segreti Istio generati. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come è possibile utilizzare Istio per fornire un routing intelligente per implementare una versione canary:The following documentation describes how you can use Istio to provide intelligent routing to roll out a canary release:

> [!div class="nextstepaction"]
> [Scenario di routing intelligente di Istio - servizio Azure Kubernetes][istio-scenario-routing]

Per esplorare altre opzioni di installazione e configurazione per Istio, vedere le seguenti linee guida ufficiali di Istio:

- [Istio - guide all'installazione][istio-installation-guides]

È inoltre possibile seguire scenari aggiuntivi utilizzando:You can also follow additional scenarios using:

- [Istio Bookinfo Esempio di applicazione][istio-bookinfo-example]

Per informazioni su come monitorare l'applicazione AKS usando Application Insights e Istio, vedere la documentazione di Monitoraggio di Azure seguente:To learn how to monitor your AKS application using Application Insights and Istio, see the following Azure Monitor documentation:

- [Monitoraggio delle applicazioni di strumentazione zero per le applicazioni ospitate Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
