---
title: Installare Istio nel servizio Azure Kubernetes
description: Informazioni su come installare e usare Istio per creare una rete mesh di servizi in un cluster del servizio Azure Kubernetes
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 285fa34db3886cf405a3682438a27a17c75d81ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666713"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installare e utilizzare Istio nel servizio Azure Kubernetes

[Istio][istio-github] è una rete mesh di servizi open source che offre un set di funzionalità chiave per i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per maggiori informazioni su Istio, vedere la documentazione ufficiale [Cos'è Istio?][istio-docs-concepts].

Questo articolo illustra come installare Istio. Il `istioctl` file binario client Istio viene installato nel computer client e i componenti Istio vengono installati in un cluster Kubernetes in AKS.

> [!NOTE]
> Le istruzioni seguenti fanno riferimento alla versione di Istio `1.7.3` .
>
> Le `1.7.x` versioni di Istio sono state testate dal team Istio in base alla versione di Kubernetes `1.16+` . È possibile trovare altre versioni di Istio in [GitHub-Istio releases][istio-github-releases], informazioni su ogni versione di [Istio News][istio-release-notes] e versioni supportate di Kubernetes in [Istio domande frequenti generali][istio-faq].

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Scaricare e installare il file binario client istioctl di Istio
> * Installare Istio su AKS
> * Convalidare l'installazione di Istio
> * Accedere ai componenti aggiuntivi
> * Disinstallare Istio da AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster AKS (Kubernetes `1.16` e versioni successive, con RBAC abilitato) e che sia stata stabilita una `kubectl` connessione con il cluster. Se occorre assistenza con uno di questi elementi, vedere la [Guida introduttiva al servizio Azure Kubernetes][aks-quickstart].

Assicurarsi di aver letto la documentazione [sulle prestazioni e la scalabilità di Istio](https://istio.io/docs/concepts/performance-and-scalability/) per comprendere i requisiti aggiuntivi delle risorse per l'esecuzione di Istio nel cluster AKS. I requisiti di memoria e di base variano in base al carico di lavoro specifico. Scegliere un numero appropriato di nodi e le dimensioni della macchina virtuale da soddisfare per la configurazione.

Questo articolo suddivide il materiale sussidiario per l'installazione di Istio in diversi passaggi. Il risultato finale presenta una struttura identica al [materiale sussidiario][istio-install-istioctl] ufficiale per l'installazione di Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>Installare l'operatore Istio su AKS

Istio fornisce un [operatore][istio-install-operator] per gestire l'installazione e gli aggiornamenti ai componenti Istio all'interno del cluster AKS. L'operatore Istio verrà installato utilizzando il `istioctl` file binario client.

```bash
istioctl operator init
```

Verrà visualizzato un output simile al seguente per confermare che l'operatore Istio è stato installato.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

L'operatore Istio viene installato nello `istio-operator` spazio dei nomi. Eseguire una query sullo spazio dei nomi.

```bash
kubectl get all -n istio-operator
```

Verranno visualizzati i componenti seguenti.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Sono disponibili altre informazioni sul modello di operatore e su come può essere utile per automatizzare attività complesse tramite [kubernetes.io][kubernetes-operator].


### <a name="install-istio-components"></a>Installare i componenti di Istio

Ora che l'operatore Istio è stato installato nel cluster AKS, è possibile installare i componenti di Istio. 

Il `default` [profilo di configurazione Istio][istio-configuration-profiles] viene usato per compilare la [specifica dell'operatore Istio][istio-control-plane].

È possibile eseguire il `istioctl` comando seguente per visualizzare la configurazione del `default` profilo di configurazione Istio.

```bash
istioctl profile dump default
```

> [!NOTE]
> Attualmente è necessario pianificare l'esecuzione di Istio nei nodi Linux. Se nel cluster sono presenti nodi di Windows Server, è necessario assicurarsi che i pod Istio siano pianificati solo per l'esecuzione nei nodi Linux. Verranno usati i [selettori di nodo][kubernetes-node-selectors] per assicurarsi che i pod siano pianificati per i nodi corretti.

> [!CAUTION]
> Le funzionalità di [ISTIO CNI][istio-feature-cni] Istio sono attualmente in [alfa][istio-feature-stages], quindi è necessario tenerne conto prima di abilitarle. 

Creare un file denominato `istio.aks.yaml` con il contenuto seguente. Questo file conterrà la [specifica dell'operatore Istio][istio-control-plane] per la configurazione di Istio.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Creare lo `istio-system` spazio dei nomi e distribuire la specifica dell'operatore Istio in tale spazio dei nomi. L'operatore Istio guarderà la specifica dell'operatore Istio e lo userà per installare e configurare Istio nel cluster AKS.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

A questo punto, è stato distribuito Istio nel cluster AKS. Per assicurarsi che la distribuzione di Istio venga completata correttamente, passare alla sezione successiva per [convalidare l'installazione di Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Convalidare l'installazione di Istio

Eseguire una query sullo `istio-system` spazio dei nomi, in cui sono stati installati i componenti Istio e componenti aggiuntivi dall'operatore Istio:

```bash
kubectl get all -n istio-system
```

Verranno visualizzati i componenti seguenti:

- `istio*` -componenti Istio
- `jaeger-*`, `tracing` e `zipkin` -addon per la traccia
- `prometheus` -addon metrica
- `grafana` -Addon del dashboard di Analytics e monitoraggio
- `kiali` -Addon del dashboard del servizio mesh

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

È anche possibile ottenere ulteriori informazioni sull'installazione osservando i log dell'operatore Istio.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Se `istio-ingressgateway` mostra un indirizzo IP esterno di `<pending>`, attendere alcuni minuti fino a quando la rete di Azure non avrà assegnato un indirizzo IP.

Tutti i pod dovrebbero mostrare lo stato `Running` . Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se per uno dei pod viene segnalato un problema, usare il comando [kubectl describe pod][kubectl-describe] per esaminare l'output e lo stato.

## <a name="accessing-the-add-ons"></a>Accesso ai componenti aggiuntivi

Un numero di componenti aggiuntivi è stato installato dall'operatore Istio che fornisce funzionalità aggiuntive. Le applicazioni Web per i componenti aggiuntivi **non** vengono esposte pubblicamente tramite un indirizzo IP esterno. 

Per accedere alle interfacce utente del componente aggiuntivo, utilizzare il `istioctl dashboard` comando. Questo comando usa il [porting di kubectl][kubectl-port-forward] e una porta casuale per creare una connessione sicura tra il computer client e il Pod pertinente nel cluster AKS. Verrà quindi aperta automaticamente l'applicazione Web del componente aggiuntivo nel browser predefinito.

### <a name="grafana"></a>Grafana

I dashboard di Analytics e monitoraggio per Istio sono forniti da [Grafana][grafana]. Ricordarsi di usare le credenziali create con il segreto Grafana in precedenza quando richiesto. Aprire il dashboard di Grafana in modo sicuro come segue:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Le metriche per Istio sono fornite da [Prometheus][prometheus]. Aprire il dashboard di Prometeo in modo sicuro come segue:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

La traccia in Istio viene fornita da [Jaeger][jaeger]. Aprire il dashboard Jaeger in modo sicuro come segue:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

La dashboard di osservabilità della rete mesh di servizi è fornita da [Kiali][kiali]. Ricordarsi di usare le credenziali create con il segreto Kiali in precedenza quando richiesto. Aprire il dashboard di Kiali in modo sicuro come segue:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

È disponibile un'interfaccia semplice per i proxy di [invio][envoy] . Fornisce le informazioni di configurazione e le metriche per un proxy inviato in esecuzione in un pod specificato. Aprire l'interfaccia di invio in modo sicuro come segue:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Disinstallare Istio da AKS

> [!WARNING]
> L'eliminazione di Istio da un sistema in esecuzione può causare problemi correlati al traffico tra i servizi. Assicurarsi di aver fatto in modo che il sistema continui a funzionare correttamente senza Istio prima di procedere.

### <a name="remove-istio"></a>Rimuovere Istio

Per rimuovere Istio dal cluster AKS, eliminare la `IstioOperator` risorsa denominata `istio-control-plane` aggiunta in precedenza. L'operatore Istio rileverà che la specifica dell'operatore Istio è stata rimossa e quindi eliminerà tutti i componenti Istio associati.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

È possibile eseguire il comando seguente per verificare quando tutti i componenti di Istio sono stati eliminati.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Rimuovi operatore Istio

Quando Istio è stato disinstallato correttamente, è anche possibile rimuovere l'operatore Istio.

```bash
istioctl operator remove
```

E infine rimuovere gli `istio-` spazi dei nomi.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'installazione e le opzioni di configurazione per Istio, vedere le indicazioni ufficiali Istio seguenti:

- [Istio-guide all'installazione][istio-installation-guides]

È anche possibile seguire altri scenari usando:

- [Esempio di applicazione BookInfo Istio][istio-bookinfo-example]

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
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
