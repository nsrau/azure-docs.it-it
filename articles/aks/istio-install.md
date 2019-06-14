---
title: Installare Istio nel servizio Azure Kubernetes
description: Informazioni su come installare e usare Istio per creare una rete mesh di servizi in un cluster del servizio Azure Kubernetes
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 33d86ab8c88b45c7787620773f0df6e7fe888cf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65850406"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installare e utilizzare Istio nel servizio Azure Kubernetes

[Istio][istio-github] è una rete mesh di servizi open source che offre un set di funzionalità chiave per i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per maggiori informazioni su Istio, vedere la documentazione ufficiale [Cos'è Istio?][istio-docs-concepts].

Questo articolo illustra come installare Istio. Il Istio `istioctl` binario client viene installato nel computer client e i componenti di Istio vengono installati in un cluster Kubernetes nel servizio contenitore di AZURE.

> [!NOTE]
> Queste istruzioni fanno riferimento a versione Istio `1.1.3`.
>
> Il Istio `1.1.x` rilasci sono stati testati dal team con le versioni di Kubernetes Istio `1.11`, `1.12`, `1.13`. È possibile trovare versioni Istio aggiuntive alla [GitHub - rilascia Istio] [ istio-github-releases] e informazioni su ognuna delle versioni in [Istio - note sulla versione] [ istio-release-notes].

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Scaricare Istio
> * Installare il client di istioctl Istio binario
> * Installare le CRD Istio nel servizio contenitore di AZURE
> * Installare i componenti di Istio nel servizio contenitore di AZURE
> * Convalidare l'installazione di Istio
> * Accesso ai componenti aggiuntivi
> * Disinstallare Istio dal servizio contenitore di AZURE

## <a name="before-you-begin"></a>Prima di iniziare

La procedura descritta in questo articolo si presuppone di aver creato un cluster del servizio contenitore di AZURE (Kubernetes `1.11` e versioni successive, con RBAC abilitato) e aver stabilito un `kubectl` connessione con il cluster. Se occorre assistenza con uno di questi elementi, vedere la [Guida introduttiva al servizio Azure Kubernetes][aks-quickstart].

È necessario [Helm] [ helm] seguire queste istruzioni e installare Istio. Si consiglia di avere la versione `2.12.2` o in un secondo momento correttamente installato e configurato nel cluster. Se occorre assistenza con l'installazione di Helm, vedere la [informazioni aggiuntive sull'installazione di Helm AKS][helm-install]. Tutti i POD Istio devono essere pianificati anche per l'esecuzione nei nodi Linux.

Questo articolo suddivide il materiale sussidiario per l'installazione di Istio in diversi passaggi. Il risultato finale presenta una struttura identica al [materiale sussidiario][istio-install-helm] ufficiale per l'installazione di Istio.

## <a name="download-istio"></a>Scaricare Istio

Prima di tutto, scaricare ed estrarre la versione più recente di Istio. I passaggi sono leggermente diversi per una shell di bash in MacOS, Linux o sottosistema Windows per Linux e per una shell di PowerShell. Scegliere una delle seguenti operazioni di installazione corrispondente all'ambiente preferito:

* [Bash su MacOS, Linux o un sottosistema Windows per Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

In MacOS usare `curl` per scaricare la versione più recente di Istio, quindi estrarre con `tar` come indicato di seguito:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

In Linux o in un sottosistema di Windows per Linux, usare `curl` per scaricare la versione più recente di Istio, quindi estrarre con `tar` come indicato di seguito:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Ora passare alla sezione [installare il client di istioctl Istio binario](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

In PowerShell, usare `Invoke-WebRequest` per scaricare la versione più recente di Istio e quindi estrarre con `Expand-Archive` come indicato di seguito:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Ora passare alla sezione [installare il client di istioctl Istio binario](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Installare il client di istioctl Istio binario

> [!IMPORTANT]
> Assicurarsi di eseguire i passaggi descritti in questa sezione, dalla cartella di primo livello della versione Istio che è stato scaricato ed estratto.

Il `istioctl` client binario vengono eseguite nel computer client e consente di interagire con la rete mesh di servizi Istio. I passaggi di installazione sono leggermente diversi tra i sistemi operativi client. Scegliere una delle seguenti operazioni di installazione corrispondente all'ambiente preferito:

* [MacOS](#macos)
* [Linux o il sottosistema Windows per Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Per installare il file binario del client `istioctl` di Istio in una shell basata su bash in MacOS, usare i comandi indicati di seguito. Questi comandi copiano il file binario del client `istioctl` nel percorso del programma dell'utente standard in `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Ora passare alla sezione successiva relativa alle [installarvi il CRD Istio AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux o sottosistema di Windows per Linux

Per installare il file binario del client `istioctl` di Istio in una shell basata su bash in Linux o in un [sottosistema di Windows per Linux][install-wsl], usare i comandi indicati di seguito. Questi comandi copiano il file binario del client `istioctl` nel percorso del programma dell'utente standard in `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Ora passare alla sezione successiva relativa alle [installarvi il CRD Istio AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Per installare il Istio `istioctl` client binario in un **Powershell**-shell basate su Windows, usare i comandi seguenti. Questi comandi copiano il `istioctl` binari in una cartella Istio client e che desideriate fornirlo in modo permanente tramite il `PATH`. Non è necessario con privilegi elevati privilegi (amministratore) eseguire questi comandi.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

Ora passare alla sezione successiva relativa alle [installarvi il CRD Istio AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Installare le CRD Istio nel servizio contenitore di AZURE

> [!IMPORTANT]
> Assicurarsi di eseguire i passaggi descritti in questa sezione, dalla cartella di primo livello della versione Istio che è stato scaricato ed estratto.

Usa Istio [definizioni delle risorse personalizzate (i crd)] [ kubernetes-crd] per gestire la configurazione di runtime. È necessario installare le CRD Istio in primo luogo, poiché i componenti di Istio presentano una dipendenza su di essi. Usare Helm e il `istio-init` per installare le CRD Istio nel grafico il `istio-system` dello spazio dei nomi nel cluster AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[I processi] [ kubernetes-jobs] distribuite come parte di `istio-init` grafico Helm per installare le CRD. Questi processi dovrebbero richiedere tra 1 e 2 minuti, a seconda dell'ambiente cluster. È possibile verificare che i processi completati correttamente come indicato di seguito:

```azurecli
kubectl get jobs -n istio-system
```

L'output di esempio seguente mostra i processi completati correttamente.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Ora che è stato confermato il completamento dei processi, è possibile verificare che abbiamo il numero corretto di Istio i CRD installato. È possibile verificare che siano stati installati tutti i CRD Istio 53 eseguendo il comando appropriato per l'ambiente. Il comando deve restituire il numero `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Se hai a questo punto, significa che è stato installato correttamente il Istio CRD. Ora passare alla sezione successiva relativa alle [installare i componenti di Istio nel servizio contenitore di AZURE](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installare i componenti di Istio nel servizio contenitore di AZURE

> [!IMPORTANT]
> Assicurarsi di eseguire i passaggi descritti in questa sezione, dalla cartella di primo livello della versione Istio che è stato scaricato ed estratto.

Abbiamo installerai [Grafana] [ grafana] e [Kiali] [ kiali] come parte del nostro installazione Istio. Grafana fornisce analitica e dashboard di monitoraggio e Kiali fornisce un dashboard l'osservabilità mesh del servizio. Nel nostro programma di installazione, ognuno di questi componenti richiede le credenziali che devono essere specificate come un [segreto][kubernetes-secrets].

Prima di poter installare i componenti di Istio, è necessario creare i segreti per Grafana sia Kiali. Creare questi segreti eseguendo i comandi appropriati per l'ambiente.

### <a name="add-grafana-secret"></a>Add Grafana Secret

Sostituire il `REPLACE_WITH_YOUR_SECURE_PASSWORD` il token con la password ed eseguire i comandi seguenti:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Aggiungere Kiali segreto

Sostituire il `REPLACE_WITH_YOUR_SECURE_PASSWORD` il token con la password ed eseguire i comandi seguenti:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Installare i componenti di Istio

Ora che è stata creata correttamente i segreti di Grafana e Kiali in cluster servizio contenitore di AZURE, è possibile installare i componenti di Istio. Usare Helm e il `istio` per installare i componenti di Istio nel grafico il `istio-system` dello spazio dei nomi nel cluster AKS. Usare i comandi appropriati per l'ambiente.

> [!NOTE]
> Si sta usando le opzioni seguenti durante l'installazione di:
> - `global.controlPlaneSecurityEnabled=true` -TLS reciproca abilitata per il piano di controllo
> - `mixer.adapters.useAdapterCRDs=false` -rimuovere espressioni di controllo nella scheda di mixer i CRD è deprecato e ciò migliorerà le prestazioni
> - `grafana.enabled=true` -abilitare la distribuzione di Grafana per analitica e dashboard di monitoraggio
> - `grafana.security.enabled=true` -abilitare l'autenticazione per Grafana
> - `tracing.enabled=true` -abilitare la distribuzione Jaeger per la traccia
> - `kiali.enabled=true` -abilitare la distribuzione Kiali per un dashboard l'osservabilità mesh del servizio

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

Il `istio` grafico Helm consente di distribuire un numero elevato di oggetti. È possibile visualizzare l'elenco dall'output del `helm install` comando precedente. La distribuzione dei componenti Istio può richiedere da 4 a 5 minuti, a seconda dell'ambiente cluster.

> [!NOTE]
> Tutti i POD Istio devono essere pianificati per l'esecuzione nei nodi Linux. Se si dispone di pool di nodi di Windows Server oltre a pool di nodi di Linux nel cluster, verificare che tutti i POD Istio sono stati pianificati per l'esecuzione nei nodi Linux.

A questo punto, è stata distribuita Istio nel cluster AKS. Per garantire che abbiamo una corretta distribuzione di Istio, è possibile passare alla sezione successiva per [convalidare l'installazione di Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Convalidare l'installazione di Istio

Verificare innanzitutto che siano stati creati i servizi previsti. Usare il comando [kubectl get svc][kubectl-get] per visualizzare i servizi in esecuzione. Query di `istio-system` dello spazio dei nomi, in cui sono stati installati i componenti Istio e componente aggiuntivo dal `istio` grafico Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

L'output di esempio indicato di seguito mostra i servizi attualmente in esecuzione:

- `istio-*` servizi
- `jaeger-*`, `tracing`, e `zipkin` servizi aggiuntivi di traccia
- `prometheus` servizio di metriche di componente aggiuntivo
- `grafana` monitoraggio servizio di dashboard e analitica di componente aggiuntivo
- `kiali` servizio di componente aggiuntivo del servizio rete mesh di dashboard

Se `istio-ingressgateway` mostra un indirizzo IP esterno di `<pending>`, attendere alcuni minuti fino a quando la rete di Azure non avrà assegnato un indirizzo IP.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Successivamente, verificare che siano stati creati i pod richiesti. Usare la [kubectl get pods] [ kubectl-get] comando ed eseguire nuovamente una query di `istio-system` dello spazio dei nomi:

```console
kubectl get pods --namespace istio-system
```

L'output di esempio indicato di seguito illustra i pod in esecuzione:

- il `istio-*` POD
- il `prometheus-*` pod di metriche di componente aggiuntivo
- il `grafana-*` analitica di componente aggiuntivo e sorveglianza pod del dashboard
- il `kiali` pod del dashboard del servizio rete mesh di componente aggiuntivo

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Dovrebbero essere presenti due `istio-init-crd-*` POD con un `Completed` dello stato. I POD erano responsabile dell'esecuzione dei processi che ha creato i CRD in un passaggio precedente. Tutti gli altri POD deve mostrare lo stato `Running`. Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se per uno dei pod viene segnalato un problema, usare il comando [kubectl describe pod][kubectl-describe] per esaminare l'output e lo stato.

## <a name="accessing-the-add-ons"></a>Accesso ai componenti aggiuntivi

Nella configurazione precedente di Istio sono stati installati diversi componenti aggiuntivi che forniscono funzionalità supplementari. Le interfacce utente per i componenti aggiuntivi non sono esposte pubblicamente tramite un indirizzo IP esterno. Per accedere alle interfacce utente dei componenti aggiuntivi, usare il comando [kubectl port-forward][kubectl-port-forward]. Questo comando crea una connessione sicura tra il computer client e il pod rilevante nel cluster AKS.

Abbiamo aggiunto un ulteriore livello di sicurezza per Grafana e Kiali specificando le credenziali per loro più indietro in questo articolo.

### <a name="grafana"></a>Grafana

Di analitica e dashboard di monitoraggio per Istio vengono fornite da [Grafana][grafana]. Inoltrare la porta locale `3000` nel computer client alla porta `3000` nel pod in esecuzione nel cluster AKS Grafana:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

L'output di esempio indicato di seguito mostra il trasferimento della porta che viene configurato per Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

È ora possibile accedere a Grafana visitando il seguente URL dal computer client - [http://localhost:3000](http://localhost:3000). Ricordarsi di usare le credenziali create tramite il Grafana segreto in precedenza quando viene richiesto.

### <a name="prometheus"></a>Prometheus

Le metriche per Istio fornite da [Prometheus][prometheus]. Inoltrare la porta locale `9090` nel computer client alla porta `9090` nel pod in esecuzione nel cluster AKS Prometheus:

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

Traccia all'interno di Istio è fornita da [Jaeger][jaeger]. Inoltrare la porta locale `16686` nel computer client alla porta `16686` nel pod in esecuzione nel cluster AKS Jaeger:

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

La dashboard di osservabilità della rete mesh di servizi è fornita da [Kiali][kiali]. Inoltrare la porta locale `20001` nel computer client alla porta `20001` nel pod in esecuzione nel cluster AKS Kiali:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

L'output di esempio indicato di seguito mostra il trasferimento della porta che viene configurato per Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

È ora possibile accedere alla dashboard di osservabilità della rete mesh di servizi di Kiali visitando il seguente URL dal computer client - [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Ricordarsi di usare le credenziali create tramite il Kiali segreto in precedenza quando viene richiesto.

## <a name="uninstall-istio-from-aks"></a>Disinstallare Istio dal servizio contenitore di AZURE

> [!WARNING]
> L'eliminazione di Istio da un sistema in esecuzione può comportare traffico problemi tra i servizi correlati. Assicurarsi che siano state effettua il provisioning per il sistema di comunque funzionare correttamente senza Istio prima di procedere.

### <a name="remove-istio-components-and-namespace"></a>Rimuovere lo spazio dei nomi e i componenti di Istio

Per rimuovere Istio dal cluster servizio contenitore di AZURE, usare i comandi seguenti. Il `helm delete` comandi rimuovono la `istio` e `istio-init` grafici e il `kubectl delete ns` comando rimuoverà il `istio-system` dello spazio dei nomi.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Rimuovere i CRD Istio

I comandi precedenti eliminano tutti i componenti di Istio e dello spazio dei nomi, ma è comunque ancora il Istio CRD. Per eliminare i CRD, è possibile usare uno gli approcci seguenti.

Approccio #1 - questo comando presuppone che si esegue questo passaggio dalla cartella di primo livello della versione scaricata ed estratta di Istio usato per installare Istio con.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Approccio #2 - usare uno di questi comandi se non è più necessario l'accesso alla versione di Istio usato per installare Istio con scaricata ed estratta. Questo comando richiederà un po' più tempo - operazione sarà necessario attendere alcuni minuti.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come è possibile usare Istio per fornire il routing intelligente per distribuire una versione canary:

> [!div class="nextstepaction"]
> [Scenario di routing intelligente di Istio - servizio Azure Kubernetes][istio-scenario-routing]

Per esplorare altre opzioni di installazione e configurazione per Istio, vedere i seguenti articoli ufficiali di Istio:

- [Istio - Guida all'installazione di Helm][istio-install-helm]
- [Istio - Opzioni per l'installazione di Helm][istio-install-helm-options]

Inoltre è possibile esplorare altri scenari usando l'[esempio di applicazione Bookinfo di Istio][istio-bookinfo-example].

Per informazioni su come monitorare l'applicazione del servizio contenitore di AZURE con Application Insights e Istio, vedere la documentazione di monitoraggio di Azure seguente:
- [Zero monitoraggio strumentazione dell'applicazione per Kubernetes ospitato applicazioni][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md