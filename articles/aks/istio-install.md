---
title: Installare Istio nel servizio Azure Kubernetes
description: Informazioni su come installare e usare Istio per creare una rete mesh di servizi in un cluster del servizio Azure Kubernetes
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: d85b830b63e2d52f3eeb5df8645edccfccf43c76
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138151"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installare e utilizzare Istio nel servizio Azure Kubernetes

[Istio][istio-github] è una rete mesh di servizi open source che offre un set di funzionalità chiave per i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per maggiori informazioni su Istio, vedere la documentazione ufficiale [Cos'è Istio?][istio-docs-concepts].

Questo articolo illustra come installare Istio. Il file binario del client di Istio `istioctl` viene installato nel computer client e quindi i componenti di Istio in un cluster Kubernetes nel servizio Azure Kubernetes. Queste istruzioni fanno riferimento alla versione di Istio *1.0.4*. È possibile trovare versioni di Istio aggiuntive in [GitHub - Versioni di Istio][istio-github-releases].

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Scaricare Istio
> * Installare il file binario del client di Istio
> * Installare i componenti Kubernetes per Istio
> * Convalidare l'installazione

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster del servizio Azure Kubernetes (Kubernetes 1.10 e versioni successive, con controllo degli accessi in base al ruolo attivato) e che sia stata stabilita una connessione `kubectl` al cluster. Se occorre assistenza con uno di questi elementi, vedere la [Guida introduttiva al servizio Azure Kubernetes][aks-quickstart].

Per installare Istio è necessario aver installato e configurato correttamente nel cluster [Helm][helm] versione *2.10.0* o successiva. Se occorre assistenza con l'installazione di Helm, vedere il [materiale sussidiario per l'installazione di Helm per il servizio Azure Kubernetes][helm-install]. Se la versione di Helm installata non è almeno la *2.10.0*, eseguire l'aggiornamento o vedere [Istio - Guida all'installazione con Helm][istio-install-helm] per altre opzioni di installazione.

Questo articolo suddivide il materiale sussidiario per l'installazione di Istio in diversi passaggi. La descrizione di ognuno di questi passaggi fornirà informazioni su come installare Istio e sul suo funzionamento con Kubernetes. Il risultato finale presenta una struttura identica al [materiale sussidiario][istio-install-k8s-quickstart] ufficiale per l'installazione di Istio.

## <a name="download-istio"></a>Scaricare Istio

Prima di tutto, scaricare ed estrarre la versione più recente di Istio. I passaggi sono leggermente diversi per una shell di bash in MacOS, Linux o sottosistema Windows per Linux e per una shell di PowerShell. Scegliere uno dei seguenti passaggi di installazione per l'ambiente preferito:

* [Bash su MacOS, Linux o un sottosistema Windows per Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

In MacOS usare `curl` per scaricare la versione più recente di Istio, quindi estrarre con `tar` come indicato di seguito:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

In Linux o in un sottosistema di Windows per Linux, usare `curl` per scaricare la versione più recente di Istio, quindi estrarre con `tar` come indicato di seguito:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

In PowerShell usare [Invoke-WebRequest][Invoke-WebRequest] per scaricare la versione più recente di Istio, quindi estrarre con [Expand-Archive][Expand-Archive] come indicato di seguito:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Installare il file binario del client di Istio

Il file binario del client `istioctl` viene eseguito nel computer client e consente di gestire i criteri e le regole di gestione di Istio. Anche in questo caso, i passaggi dell'installazione differiscono leggermente tra sistemi operativi del client. Scegliere uno dei seguenti passaggi di installazione per l'ambiente preferito.

> [!IMPORTANT]
> Assicurarsi di eseguire i passaggi descritti in questa sezione, dalla cartella di primo livello della versione Istio che è stato scaricato ed estratto.

### <a name="macos"></a>MacOS

Per installare il file binario del client `istioctl` di Istio in una shell basata su bash in MacOS, usare i comandi indicati di seguito. Questi comandi copiano il file binario del client `istioctl` nel percorso del programma dell'utente standard in `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Se si desidera il completamento della riga di comando per il file binario del client `istioctl` di Istio, procedere alla configurazione come indicato di seguito:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Passare ora alla sezione [Installazione dei componenti Kubernetes di Istio](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux o sottosistema di Windows per Linux

Per installare il file binario del client `istioctl` di Istio in una shell basata su bash in Linux o in un [sottosistema di Windows per Linux][install-wsl], usare i comandi indicati di seguito. Questi comandi copiano il file binario del client `istioctl` nel percorso del programma dell'utente standard in `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Se si desidera il completamento della riga di comando per il file binario del client `istioctl` di Istio, procedere alla configurazione come indicato di seguito:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Passare ora alla sezione [Installazione dei componenti Kubernetes di Istio](#install-the-istio-kubernetes-components).

### <a name="windows"></a>Windows

Per installare il file binario del client `istioctl` di Istio in una shell basata su Powershell in Windows, usare i comandi indicati di seguito. Questi comandi copiano il file binario del client `istioctl` in un percorso del programma di un nuovo utente e lo rendono disponibile tramite `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Installare i componenti Kubernetes per Istio

> [!IMPORTANT]
> Assicurarsi di eseguire i passaggi descritti in questa sezione, dalla cartella di primo livello della versione Istio che è stato scaricato ed estratto.

> [!NOTE]
> Versione `1.0.6` e più recenti del grafico Istio Helm include modifiche di rilievo. Se si sceglie di installare questa versione, è ora necessario creare manualmente un segreto per Kiali. È necessario anche creare manualmente un segreto per Grafana se sono stati impostati `grafana.security.enabled=true`. Vedere il grafico Helm Istio [README.md](https://github.com/istio/istio/tree/master/install/kubernetes/helm/istio#installing-the-chart) per altri dettagli su come creare questi segreti.

Per installare i componenti di Istio nel cluster del servizio Azure Kubernetes è necessario usare Helm. Installare le risorse di Istio nello spazio dei nomi `istio-system` e abilitare le opzioni aggiuntive per la sicurezza e il monitoraggio come indicato di seguito:

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Il chart di Helm consente di distribuire un numero elevato di oggetti. A seconda dell'ambiente del cluster, il completamento della distribuzione può richiedere da 2 a 3 minuti.

## <a name="validate-the-installation"></a>Convalidare l'installazione

Per assicurarsi di aver effettuato una corretta distribuzione di Istio, verrà ora illustrato come convalidare l'installazione.

Verificare innanzitutto che siano stati creati i servizi previsti. Usare il comando [kubectl get svc][kubectl-get] per visualizzare i servizi in esecuzione. Eseguire query sullo spazio dei nomi *istio-system* in cui il chart di Helm ha installato i componenti aggiuntivi e di Istio:

```console
kubectl get svc --namespace istio-system --output wide
```

L'output di esempio indicato di seguito mostra i servizi attualmente in esecuzione:

- *istio-** servizi
- * jaeger-**, *analisi* e *zipkin*, servizi aggiuntivi di analisi
- *prometheus*, servizio aggiuntivo per le metriche
- *grafana*, servizio dashboard aggiuntivo di analisi e monitoraggio
- *kiali*, servizio dashboard aggiuntivo della rete mesh di servizi

Se `istio-ingressgateway` mostra un indirizzo IP esterno di `<pending>`, attendere alcuni minuti fino a quando la rete di Azure non avrà assegnato un indirizzo IP.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

Successivamente, verificare che siano stati creati i pod richiesti. Usare il comando [kubectl get pods][kubectl-get] ed eseguire nuovamente una query sullo spazio dei nomi *istio-system*:

```console
kubectl get pods --namespace istio-system
```

L'output di esempio indicato di seguito illustra i pod in esecuzione:

- pod di *istio-**
- pod di *prometheus-**, componente aggiuntivo per le metriche
- pod di *grafana-**, componente aggiuntivo della dashboard di analisi e monitoraggio
- pod di *kiali*, componente aggiuntivo della dashboard della rete mesh di servizi

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Tutti i pod presentano lo stato `Running`. Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se per uno dei pod viene segnalato un problema, usare il comando [kubectl describe pod][kubectl-describe] per esaminare l'output e lo stato.

## <a name="accessing-the-add-ons"></a>Accesso ai componenti aggiuntivi

Nella configurazione precedente di Istio sono stati installati diversi componenti aggiuntivi che forniscono funzionalità supplementari. Le interfacce utente per i componenti aggiuntivi non sono esposte pubblicamente tramite un indirizzo IP esterno. Per accedere alle interfacce utente dei componenti aggiuntivi, usare il comando [kubectl port-forward][kubectl-port-forward]. Questo comando crea una connessione sicura tra una porta locale nel computer client e il pod rilevante nel cluster del servizio Azure Kubernetes.

### <a name="grafana"></a>Grafana

Le dashboard di analisi e monitoraggio per Istio sono fornite da [Grafana][grafana]. Trasferire la porta locale *3000* del computer client alla porta *3000* del pod che sta eseguendo Grafana nel cluster del servizio Azure Kubernetes:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

L'output di esempio indicato di seguito mostra il trasferimento della porta che viene configurato per Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

È ora possibile accedere a Grafana visitando il seguente URL dal computer client - [http://localhost:3000](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

Il browser delle espressioni per le metriche è fornito da [Prometheus][prometheus]. Trasferire la porta locale *9090* del computer client alla porta *9090* del pod che sta eseguendo Prometheus nel cluster del servizio Azure Kubernetes:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

L'output di esempio indicato di seguito mostra il trasferimento della porta che viene configurato per Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

È ora possibile accedere al browser delle espressioni di Prometheus visitando il seguente URL dal computer client - [ http://localhost:9090 ](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

L'interfaccia utente di analisi è fornita da [Jaeger][jaeger]. Trasferire la porta locale *16686* del computer client alla porta *16686* del pod che sta eseguendo Jaeger nel cluster del servizio Azure Kubernetes:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

L'output di esempio indicato di seguito mostra il trasferimento della porta che viene configurato per Grafana:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

È ora possibile accedere all'interfaccia utente di analisi di Jaeger visitando il seguente URL dal computer client - [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

La dashboard di osservabilità della rete mesh di servizi è fornita da [Kiali][kiali]. Trasferire la porta locale *20001* del computer client alla porta *20001* del pod che sta eseguendo Kiali nel cluster del servizio Azure Kubernetes:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

L'output di esempio indicato di seguito mostra il trasferimento della porta che viene configurato per Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

È ora possibile accedere alla dashboard di osservabilità della rete mesh di servizi di Kiali visitando il seguente URL dal computer client - [http://localhost:20001](http://localhost:20001).

Il nome utente e la password predefiniti per la dashboard di Kiali sono *nome utente: admin/password: admin*. Queste credenziali possono essere configurate tramite i valori di Helm *kiali.dashboard.username* e *kiali.dashboard.passphrase*.

## <a name="next-steps"></a>Passaggi successivi

Per scoprire come utilizzare Istio per fornire funzionalità di routing intelligente tra più versioni dell'applicazione e per distribuire una versione canary, vedere la seguente documentazione:

> [!div class="nextstepaction"]
> [Scenario di routing intelligente di Istio - servizio Azure Kubernetes][istio-scenario-routing]

Per esplorare altre opzioni di installazione e configurazione per Istio, vedere i seguenti articoli ufficiali di Istio:

- [Istio - Guida introduttiva all'installazione di Kubernetes][istio-install-k8s-quickstart]
- [Istio - Guida all'installazione di Helm][istio-install-helm]
- [Istio - Opzioni per l'installazione di Helm][istio-install-helm-options]

Inoltre è possibile esplorare altri scenari usando l'[esempio di applicazione Bookinfo di Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
