---
title: Installare Istio nel servizio Azure Kubernetes
description: Informazioni su come installare e usare Istio per creare una rete mesh di servizi in un cluster del servizio Azure Kubernetes
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 245ac3b1fd88b8d2430e9ddefef3562efd16e6d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885390"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installare e utilizzare Istio nel servizio Azure Kubernetes

[Istio][istio-github] è una mesh di servizi open source che fornisce un set di chiavi di funzionalità tra i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per ulteriori informazioni su Istio, vedere la documentazione ufficiale [che cos'è Istio?][istio-docs-concepts] .

Questo articolo illustra come installare Istio. Il file binario client `istioctl` Istio viene installato nel computer client e i componenti Istio vengono installati in un cluster Kubernetes in AKS.

> [!NOTE]
> Queste istruzioni fanno riferimento a Istio Version `1.3.2`e usano almeno la versione Helm `2.14.2`.
>
> Le versioni `1.3.x` di Istio sono state testate dal team Istio in base alle versioni di Kubernetes `1.13`, `1.14``1.15`. È possibile trovare altre versioni di Istio in [GitHub-Istio releases][istio-github-releases], informazioni su ogni versione di [Istio News][istio-release-notes] e versioni supportate di Kubernetes in [Istio domande frequenti generali][istio-faq].

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Scaricare e installare il file binario client istioctl di Istio
> * Installare Istio su AKS
> * Convalidare l'installazione di Istio
> * Accedere ai componenti aggiuntivi
> * Disinstallare Istio da AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster AKS (Kubernetes `1.13` e versioni successive, con RBAC abilitato) e che sia stata stabilita una connessione `kubectl` con il cluster. Per informazioni su questi elementi, vedere la [Guida introduttiva di AKS][aks-quickstart].

È necessario [Helm][helm] per seguire queste istruzioni e installare Istio. È consigliabile che nel cluster sia installata e configurata correttamente la versione stabile più recente. Per assistenza sull'installazione di Helm, vedere la Guida all' [installazione di AKS Helm][helm-install]. Anche tutti i pod Istio devono essere pianificati per l'esecuzione nei nodi Linux.

Assicurarsi di aver letto la documentazione [sulle prestazioni e la scalabilità di Istio](https://istio.io/docs/concepts/performance-and-scalability/) per comprendere i requisiti aggiuntivi delle risorse per l'esecuzione di Istio nel cluster AKS. I requisiti di memoria e di base variano in base al carico di lavoro specifico. Scegliere un numero appropriato di nodi e le dimensioni della macchina virtuale da soddisfare per la configurazione.

Questo articolo suddivide il materiale sussidiario per l'installazione di Istio in diversi passaggi. Il risultato finale è lo stesso nella struttura delle [linee guida][istio-install-helm]per l'installazione ufficiale di Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>Aggiungere il repository Istio Helm Chart

Aggiungere il repository Istio Helm Chart per la versione Istio. Assicurarsi di eseguire il `helm repo update` per aggiornare le informazioni locali per il repository Chart.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Installare Istio CRD su AKS

Istio USA [definizioni di risorse personalizzate (CRD)][kubernetes-crd] per gestire la configurazione di Runtime. Prima di tutto è necessario installare il CRD Istio, perché i componenti Istio hanno una dipendenza. Usare Helm e il grafico `istio-init` per installare il CRD Istio nello spazio dei nomi `istio-system` nel cluster AKS:

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

I [processi][kubernetes-jobs] vengono distribuiti come parte del grafico Helm `istio-init` per installare CRD. Il completamento di questi processi dovrebbe richiedere meno di 20 anni, a seconda dell'ambiente cluster. È possibile verificare che i processi siano stati completati correttamente nel modo seguente:

```azurecli
kubectl get jobs -n istio-system
```

Nell'output di esempio seguente vengono mostrati i processi completati correttamente.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Ora che è stato confermato il corretto completamento dei processi, verificare che sia installato il numero corretto di Istio CRD. È possibile verificare che tutti i 23 CRD di Istio siano stati installati eseguendo il comando seguente. Il comando deve restituire il numero `23`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Se si è arrivati a questo punto, significa che l'installazione di Istio CRD è stata completata. Passare ora alla sezione successiva per [installare i componenti di Istio su AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installare i componenti di Istio su AKS

Verrà installato [Grafana][grafana] e [Kiali][kiali] come parte dell'installazione di Istio. Grafana fornisce dashboard di analisi e monitoraggio e Kiali fornisce un dashboard di osservabilità della mesh di servizi. In questa configurazione, ognuno di questi componenti richiede credenziali che devono essere fornite come [segreto][kubernetes-secrets].

Prima di poter installare i componenti Istio, è necessario creare i segreti sia per Grafana che per Kiali. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Installare i componenti di Istio

Ora che sono stati creati i segreti Grafana e Kiali nel cluster AKS, è necessario installare i componenti Istio. Usare Helm e il grafico `istio` per installare i componenti Istio nello spazio dei nomi `istio-system` nel cluster AKS. 

> [!NOTE]
> **Opzioni di installazione**
> 
> Come parte dell'installazione di vengono usate le opzioni seguenti:
> - `global.controlPlaneSecurityEnabled=true`-TLS reciproco abilitato per il piano di controllo
> - `global.mtls.enabled=true`: è necessario che tutte le comunicazioni da servizio a servizio dispongano di MTLS
> - `grafana.enabled=true`-abilitare la distribuzione di Grafana per l'analisi e il monitoraggio dei dashboard
> - abilitare l'autenticazione `grafana.security.enabled=true` per Grafana
> - `tracing.enabled=true`-abilitare la distribuzione di Jaeger per la traccia
> - `kiali.enabled=true`-abilitare la distribuzione Kiali per un dashboard di osservabilità della rete di servizi
>
> **Selettori di nodo**
>
> Attualmente è necessario pianificare l'esecuzione di Istio nei nodi Linux. Se nel cluster sono presenti nodi di Windows Server, è necessario assicurarsi che i pod Istio siano pianificati solo per l'esecuzione nei nodi Linux. Verranno usati i [selettori di nodo][kubernetes-node-selectors] per assicurarsi che i pod siano pianificati per i nodi corretti.

> [!CAUTION]
> Le funzionalità [SDS (Secret Discovery Service)][istio-feature-sds] e [Istio CNI][istio-feature-cni] Istio sono attualmente in [alfa][istio-feature-stages], quindi è necessario considerare prima di abilitare queste funzionalità. Inoltre, la funzionalità di [proiezione del volume del token dell'account di servizio][kubernetes-feature-sa-projected-volume] Kubernetes (requisito per SDS) non è abilitata nelle versioni correnti di AKS.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

Il grafico Helm `istio` distribuisce un numero elevato di oggetti. È possibile visualizzare l'elenco dall'output del comando `helm install` precedente. Il completamento della distribuzione dei componenti di Istio deve richiedere meno di 2 minuti, a seconda dell'ambiente cluster.

A questo punto, è stato distribuito Istio nel cluster AKS. Per assicurarsi che la distribuzione di Istio venga completata correttamente, passare alla sezione successiva per [convalidare l'installazione di Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Convalidare l'installazione di Istio

Verificare innanzitutto che siano stati creati i servizi previsti. Usare il comando [kubectl Get SVC][kubectl-get] per visualizzare i servizi in esecuzione. Eseguire una query sullo spazio dei nomi `istio-system`, in cui sono stati installati i componenti Istio e componente aggiuntivo dal grafico Helm `istio`:

```console
kubectl get svc --namespace istio-system --output wide
```

L'output di esempio indicato di seguito mostra i servizi attualmente in esecuzione:

- Servizi di `istio-*`
- Servizi di traccia del componente aggiuntivo `jaeger-*`, `tracing`e `zipkin`
- `prometheus` il servizio metriche del componente aggiuntivo
- `grafana` componente aggiuntivo di analisi e monitoraggio del servizio dashboard
- `kiali` servizio dashboard mesh del servizio componente aggiuntivo

Se `istio-ingressgateway` mostra un indirizzo IP esterno di `<pending>`, attendere alcuni minuti fino a quando la rete di Azure non avrà assegnato un indirizzo IP.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
```

Successivamente, verificare che siano stati creati i pod richiesti. Usare il comando [kubectl Get Pod][kubectl-get] , quindi eseguire nuovamente una query sullo spazio dei nomi `istio-system`:

```console
kubectl get pods --namespace istio-system
```

L'output di esempio indicato di seguito illustra i pod in esecuzione:

- Pod `istio-*`
- il pod della metrica del componente aggiuntivo `prometheus-*`
- il pod del dashboard di analisi e monitoraggio del `grafana-*` componente aggiuntivo
- il pod del dashboard della mesh del servizio componente aggiuntivo `kiali`

```console
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Devono essere presenti tre `istio-init-crd-*` Pod con uno stato `Completed`. Questi pod erano responsabili dell'esecuzione dei processi che hanno creato il CRD in un passaggio precedente. Tutti gli altri pod dovrebbero mostrare lo stato `Running`. Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se i pod segnalano un problema, usare il [kubectl descrivere il Pod][kubectl-describe] comando per verificarne l'output e lo stato.

## <a name="accessing-the-add-ons"></a>Accesso ai componenti aggiuntivi

Una serie di componenti aggiuntivi è stata installata da Istio nella configurazione precedente che fornisce funzionalità aggiuntive. Le applicazioni Web per i componenti aggiuntivi **non** vengono esposte pubblicamente tramite un indirizzo IP esterno. 

Per accedere alle interfacce utente del componente aggiuntivo, utilizzare il comando `istioctl dashboard`. Questo comando USA [kubectl porta-in][kubectl-port-forward] poi e una porta casuale per creare una connessione sicura tra il computer client e il Pod pertinente nel cluster AKS. Verrà quindi aperta automaticamente l'applicazione Web del componente aggiuntivo nel browser predefinito.

È stato aggiunto un ulteriore livello di sicurezza per Grafana e Kiali specificando le relative credenziali in precedenza in questo articolo.

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

Un dashboard di osservabilità della rete di servizi è fornito da [Kiali][kiali]. Ricordarsi di usare le credenziali create con il segreto Kiali in precedenza quando richiesto. Aprire il dashboard di Kiali in modo sicuro come segue:

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

### <a name="remove-istio-components-and-namespace"></a>Rimuovere i componenti e lo spazio dei nomi Istio

Per rimuovere Istio dal cluster AKS, usare i comandi seguenti. Il `helm delete` comandi rimuoverà i grafici `istio` e `istio-init` e il `kubectl delete namespace` comando rimuoverà lo spazio dei nomi `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Rimuovere Istio CRD e segreti

I comandi precedenti eliminano tutti i componenti e lo spazio dei nomi di Istio, ma restano ancora Istio CRD e Secrets. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come è possibile usare Istio per fornire il routing intelligente per implementare una versione Canary:

> [!div class="nextstepaction"]
> [Scenario di routing intelligente AKS Istio][istio-scenario-routing]

Per esplorare altre opzioni di installazione e configurazione per Istio, vedere i seguenti articoli ufficiali di Istio:

- [Guida all'installazione di Istio-Helm][istio-install-helm]
- [Opzioni di installazione di Istio-Helm][istio-install-helm-options]

È anche possibile seguire altri scenari usando:

- [Esempio di applicazione BookInfo Istio][istio-bookinfo-example]

Per informazioni su come monitorare l'applicazione AKS usando Application Insights e Istio, vedere la documentazione di monitoraggio di Azure seguente:

- [Monitoraggio dell'applicazione con zero strumentazione per applicazioni ospitate in Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
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
[helm-install]: ./kubernetes-helm.md
