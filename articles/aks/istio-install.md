---
title: Installare Istio nel servizio Azure Kubernetes
description: Informazioni su come installare e usare Istio per creare una rete mesh di servizi in un cluster del servizio Azure Kubernetes
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9d973cb2ac210e912d93941a2f81889557379f43
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67625985"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installare e utilizzare Istio nel servizio Azure Kubernetes

[Istio][istio-github] è una mesh di servizi open source che fornisce un set di chiavi di funzionalità tra i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per ulteriori informazioni su Istio, vedere la documentazione ufficiale [che cos'è Istio?][istio-docs-concepts] .

Questo articolo illustra come installare Istio. Il file `istioctl` binario client Istio viene installato nel computer client e i componenti Istio vengono installati in un cluster Kubernetes in AKS.

> [!NOTE]
> Queste istruzioni fanno riferimento alla `1.1.3`versione di Istio.
>
> Le versioni `1.1.x` di Istio sono state testate dal team Istio in base `1.11`alle `1.12`versioni `1.13`di Kubernetes,,. È possibile trovare altre versioni di Istio in [GitHub-Istio releases][istio-github-releases] e informazioni su ognuna delle versioni in [Note sulla versione di Istio][istio-release-notes].

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Scaricare Istio
> * Installare il file binario client istioctl di Istio
> * Installare Istio CRD su AKS
> * Installare i componenti di Istio su AKS
> * Convalidare l'installazione di Istio
> * Accesso ai componenti aggiuntivi
> * Disinstallare Istio da AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster AKS `1.11` (Kubernetes e versioni successive, con RBAC abilitato) e `kubectl` che sia stata stabilita una connessione con il cluster. Per informazioni su questi elementi, vedere la [Guida introduttiva di AKS][aks-quickstart].

È necessario [Helm][helm] per seguire queste istruzioni e installare Istio. È consigliabile che nel cluster sia installata `2.12.2` e configurata correttamente la versione o una versione successiva. Per assistenza sull'installazione di Helm, vedere la Guida all' [installazione di AKS Helm][helm-install]. Anche tutti i pod Istio devono essere pianificati per l'esecuzione nei nodi Linux.

Questo articolo suddivide il materiale sussidiario per l'installazione di Istio in diversi passaggi. Il risultato finale è lo stesso nella struttura delle [linee guida][istio-install-helm]per l'installazione ufficiale di Istio.

## <a name="download-istio"></a>Scaricare Istio

Prima di tutto, scaricare ed estrarre la versione più recente di Istio. I passaggi sono leggermente diversi per una shell bash in MacOS, Linux o sottosistema Windows per Linux e per una shell di PowerShell. Scegliere una delle seguenti procedure di installazione corrispondenti all'ambiente preferito:

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

Passare ora alla sezione per [installare il file binario client istioctl di Istio](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

In PowerShell usare `Invoke-WebRequest` per scaricare la versione più recente di Istio e quindi estrarla con `Expand-Archive` come indicato di seguito:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Passare ora alla sezione per [installare il file binario client istioctl di Istio](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Installare il file binario client istioctl di Istio

> [!IMPORTANT]
> Assicurarsi di eseguire i passaggi descritti in questa sezione dalla cartella di livello superiore della versione Istio scaricata ed estratta.

Il `istioctl` file binario client viene eseguito nel computer client e consente di interagire con la mesh del servizio Istio. I passaggi di installazione sono leggermente diversi tra i sistemi operativi client. Scegliere una delle seguenti procedure di installazione corrispondenti all'ambiente preferito:

* [MacOS](#macos)
* [Sottosistema Linux o Windows per Linux](#linux-or-windows-subsystem-for-linux)
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

Passare ora alla sezione successiva per [installare Istio CRD in AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux o sottosistema di Windows per Linux

Usare i comandi seguenti per installare il file `istioctl` binario del client Istio in una shell basata su bash in un sottosistema Linux o [Windows per Linux][install-wsl]. Questi comandi copiano il file binario del client `istioctl` nel percorso del programma dell'utente standard in `PATH`.

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

Passare ora alla sezione successiva per [installare Istio CRD in AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Per installare il file `istioctl` binario client di Istio in una shell basata su **PowerShell**in Windows, usare i comandi seguenti. Questi comandi copiano `istioctl` il file binario client in una cartella Istio e quindi lo rendono disponibile immediatamente (nella shell corrente) e in modo permanente (tra i riavvii `PATH`della Shell) tramite il. Non sono necessari privilegi elevati (amministratore) per eseguire questi comandi e non è necessario riavviare la Shell.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Passare ora alla sezione successiva per [installare Istio CRD in AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Installare Istio CRD su AKS

> [!IMPORTANT]
> Assicurarsi di eseguire i passaggi descritti in questa sezione dalla cartella di livello superiore della versione Istio scaricata ed estratta.

Istio USA [definizioni di risorse personalizzate (CRD)][kubernetes-crd] per gestire la configurazione di Runtime. Prima di tutto è necessario installare il CRD Istio, perché i componenti Istio hanno una dipendenza. Usare Helm e il `istio-init` grafico per installare Istio CRD `istio-system` nello spazio dei nomi nel cluster AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

I [processi][kubernetes-jobs] vengono distribuiti come parte del `istio-init` grafico Helm per installare CRD. Per completare questi processi sono necessari da 1 a 2 minuti, a seconda dell'ambiente cluster. È possibile verificare che i processi siano stati completati correttamente nel modo seguente:

```azurecli
kubectl get jobs -n istio-system
```

Nell'output di esempio seguente vengono mostrati i processi completati correttamente.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Ora che è stato confermato il corretto completamento dei processi, verificare che sia installato il numero corretto di Istio CRD. È possibile verificare che tutti i CRD Istio 53 siano stati installati eseguendo il comando appropriato per l'ambiente in uso. Il comando deve restituire il numero `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Se si è arrivati a questo punto, significa che l'installazione di Istio CRD è stata completata. Passare ora alla sezione successiva per [installare i componenti di Istio su AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installare i componenti di Istio su AKS

> [!IMPORTANT]
> Assicurarsi di eseguire i passaggi descritti in questa sezione dalla cartella di livello superiore della versione Istio scaricata ed estratta.

Verrà installato [Grafana][grafana] e [Kiali][kiali] come parte dell'installazione di Istio. Grafana fornisce dashboard di analisi e monitoraggio e Kiali fornisce un dashboard di osservabilità della mesh di servizi. In questa configurazione, ognuno di questi componenti richiede credenziali che devono essere fornite come [segreto][kubernetes-secrets].

Prima di poter installare i componenti Istio, è necessario creare i segreti sia per Grafana che per Kiali. Creare questi segreti eseguendo i comandi appropriati per l'ambiente in uso.

### <a name="add-grafana-secret"></a>Aggiungi Grafana Secret

Sostituire il `REPLACE_WITH_YOUR_SECURE_PASSWORD` token con la password ed eseguire i comandi seguenti:

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

### <a name="add-kiali-secret"></a>Aggiungi Kiali Secret

Sostituire il `REPLACE_WITH_YOUR_SECURE_PASSWORD` token con la password ed eseguire i comandi seguenti:

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

Ora che sono stati creati i segreti Grafana e Kiali nel cluster AKS, è necessario installare i componenti Istio. Usare Helm e il `istio` grafico per installare i componenti `istio-system` Istio nello spazio dei nomi nel cluster AKS. Usare i comandi appropriati per l'ambiente.

> [!NOTE]
> Come parte dell'installazione di vengono usate le opzioni seguenti:
> - `global.controlPlaneSecurityEnabled=true`-TLS reciproco abilitato per il piano di controllo
> - `mixer.adapters.useAdapterCRDs=false`-rimuovere gli orologi sull'adattatore del mixer CRD perché sono deprecati e ciò consente di migliorare le prestazioni
> - `grafana.enabled=true`-Abilita la distribuzione di Grafana per i dashboard di analisi e monitoraggio
> - `grafana.security.enabled=true`-abilitare l'autenticazione per Grafana
> - `tracing.enabled=true`-abilitare la distribuzione di Jaeger per la traccia
> - `kiali.enabled=true`-abilitare la distribuzione di Kiali per un dashboard di osservabilità della rete di servizi

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

Il `istio` grafico Helm distribuisce un numero elevato di oggetti. È possibile visualizzare l'elenco dall'output del `helm install` comando precedente. La distribuzione dei componenti Istio può richiedere da 4 a 5 minuti per il completamento, a seconda dell'ambiente cluster.

> [!NOTE]
> Tutti i pod Istio devono essere pianificati per l'esecuzione nei nodi Linux. Se sono presenti pool di nodi di Windows Server oltre ai pool di nodi Linux nel cluster, verificare che tutti i pod Istio siano stati pianificati per l'esecuzione nei nodi Linux.

A questo punto, è stato distribuito Istio nel cluster AKS. Per assicurarsi che la distribuzione di Istio venga completata correttamente, passare alla sezione successiva per convalidare [l'installazione di Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Convalidare l'installazione di Istio

Verificare innanzitutto che siano stati creati i servizi previsti. Usare il comando [kubectl Get SVC][kubectl-get] per visualizzare i servizi in esecuzione. Eseguire una `istio-system` query sullo spazio dei nomi, `istio` in cui sono stati installati i componenti Istio e componente aggiuntivo dal grafico Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

L'output di esempio indicato di seguito mostra i servizi attualmente in esecuzione:

- `istio-*`Servizi
- `jaeger-*`Servizi `tracing`di traccia `zipkin` aggiuntivi, e
- `prometheus`servizio metrica componenti aggiuntivi
- `grafana`servizio dashboard di analisi e monitoraggio componenti aggiuntivi
- `kiali`servizio dashboard mesh del servizio componente aggiuntivo

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

Successivamente, verificare che siano stati creati i pod richiesti. Usare il comando [kubectl Get Pod][kubectl-get] e nuovamente eseguire una query `istio-system` sullo spazio dei nomi:

```console
kubectl get pods --namespace istio-system
```

L'output di esempio indicato di seguito illustra i pod in esecuzione:

- i `istio-*` Pod
- Pod `prometheus-*` metrica del componente aggiuntivo
- il `grafana-*` Pod del dashboard di analisi e monitoraggio del componente aggiuntivo
- Pod `kiali` del dashboard mesh del servizio componente aggiuntivo

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

Devono essere presenti due `istio-init-crd-*` Pod con uno `Completed` stato. Questi pod erano responsabili dell'esecuzione dei processi che hanno creato il CRD in un passaggio precedente. Tutti gli altri pod dovrebbero mostrare lo stato `Running`. Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se i pod segnalano un problema, usare il [kubectl descrivere il Pod][kubectl-describe] comando per verificarne l'output e lo stato.

## <a name="accessing-the-add-ons"></a>Accesso ai componenti aggiuntivi

Nella configurazione precedente di Istio sono stati installati diversi componenti aggiuntivi che forniscono funzionalità supplementari. Le interfacce utente per i componenti aggiuntivi non sono esposte pubblicamente tramite un indirizzo IP esterno. Per accedere alle interfacce utente dei componenti aggiuntivi, usare il comando [kubectl Port-avanti][kubectl-port-forward] . Questo comando crea una connessione sicura tra il computer client e il Pod pertinente nel cluster AKS.

È stato aggiunto un ulteriore livello di sicurezza per Grafana e Kiali specificando le relative credenziali in precedenza in questo articolo.

### <a name="grafana"></a>Grafana

I dashboard di Analytics e monitoraggio per Istio sono forniti da [Grafana][grafana]. Inviare la porta `3000` locale del computer client alla porta `3000` sul Pod che esegue Grafana nel cluster AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

L'output di esempio indicato di seguito mostra il trasferimento della porta che viene configurato per Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

È ora possibile accedere a Grafana visitando il seguente URL dal computer client - [http://localhost:3000](http://localhost:3000). Ricordarsi di usare le credenziali create con il segreto Grafana in precedenza quando richiesto.

### <a name="prometheus"></a>Prometheus

Le metriche per Istio sono fornite da [Prometheus][prometheus]. Inviare la porta `9090` locale del computer client alla porta `9090` sul Pod che esegue Prometeo nel cluster AKS:

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

La traccia in Istio viene fornita da [Jaeger][jaeger]. Inviare la porta `16686` locale del computer client alla porta `16686` sul Pod che esegue Jaeger nel cluster AKS:

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

Un dashboard di osservabilità della rete di servizi è fornito da [Kiali][kiali]. Inviare la porta `20001` locale del computer client alla porta `20001` sul Pod che esegue Kiali nel cluster AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

L'output di esempio indicato di seguito mostra il trasferimento della porta che viene configurato per Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

È ora possibile accedere alla dashboard di osservabilità della rete mesh di servizi di Kiali visitando il seguente URL dal computer client - [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Ricordarsi di usare le credenziali create con il segreto Kiali in precedenza quando richiesto.

## <a name="uninstall-istio-from-aks"></a>Disinstallare Istio da AKS

> [!WARNING]
> L'eliminazione di Istio da un sistema in esecuzione può causare problemi correlati al traffico tra i servizi. Assicurarsi di aver fatto in modo che il sistema continui a funzionare correttamente senza Istio prima di procedere.

### <a name="remove-istio-components-and-namespace"></a>Rimuovere i componenti e lo spazio dei nomi Istio

Per rimuovere Istio dal cluster AKS, usare i comandi seguenti. I `helm delete` comandi elimineranno i `istio` grafici `istio-init` e e il `kubectl delete ns` comando rimuoverà lo `istio-system` spazio dei nomi.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Rimuovere Istio CRD

I comandi precedenti eliminano tutti i componenti e lo spazio dei nomi di Istio, ma rimane ancora Istio CRD. Per eliminare il CRD, è possibile usare uno degli approcci seguenti.

Approccio #1-questo comando presuppone che si stia eseguendo questo passaggio dalla cartella di livello superiore della versione scaricata ed estratta di Istio usata per installare Istio con.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Approccio #2: usare uno di questi comandi se non si ha più accesso alla versione scaricata ed estratta di Istio usata per installare Istio con. Per il completamento di questo comando è necessario attendere alcuni minuti.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come è possibile usare Istio per fornire il routing intelligente per implementare una versione Canary:

> [!div class="nextstepaction"]
> [Scenario di routing intelligente AKS Istio][istio-scenario-routing]

Per esplorare altre opzioni di installazione e configurazione per Istio, vedere i seguenti articoli ufficiali di Istio:

- [Guida all'installazione di Istio-Helm][istio-install-helm]
- [Opzioni di installazione di Istio-Helm][istio-install-helm-options]

È anche possibile seguire altri scenari usando l' [esempio di applicazione Istio BookInfo][istio-bookinfo-example].

Per informazioni su come monitorare l'applicazione AKS usando Application Insights e Istio, vedere la documentazione di monitoraggio di Azure seguente:
- [Monitoraggio dell'applicazione con zero strumentazione per applicazioni ospitate in Kubernetes][app-insights]

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
