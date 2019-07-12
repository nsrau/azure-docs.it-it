---
title: Distribuire un'istanza di Prometheus autonoma in un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Creare un'istanza di Prometheus in un cluster Azure Red Hat OpenShift per monitorare le metriche dell'applicazione.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, metriche, red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827011"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Distribuire un'istanza di Prometheus autonoma in un cluster Azure Red Hat OpenShift

Questo articolo descrive come configurare un'istanza di Prometheus autonomo che usa l'individuazione del servizio in un cluster Azure Red Hat OpenShift.

> [!NOTE]
> Accesso di amministratore dei clienti per Azure Red Hat OpenShift cluster non è obbligatorio.

Programma di installazione di destinazione:

- Un progetto (prometheus-), che contiene Prometheus e Alertmanager.
- Due progetti (project1 di app e app-project2), che contengono le applicazioni da monitorare.

Viene illustrato come preparare dei file di configurazione di Prometheus in locale. Creare una nuova cartella in cui archiviarli. I file di configurazione vengono archiviati nel cluster come segreti, nel caso in cui i token segreto saranno state aggiunte al cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Accedere al cluster usando lo strumento di O.C.

1. Aprire un web browser e passare quindi alla console web del cluster (https://openshift. *id casuale*. *area*. azmosa.io).
2. Accedere con le credenziali di Azure.
3. Selezionare il nome utente nell'angolo superiore destro e quindi selezionare **comando Login copia**.
4. Incollare il nome utente nel terminale che verrà usato.

> [!NOTE]
> Per vedere se si è connessi al cluster corretto, eseguire il `oc whoami -c` comando.

## <a name="prepare-the-projects"></a>Preparare i progetti

Per creare i progetti, eseguire i comandi seguenti:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> È possibile usare la `-n` o `--namespace` parametro o selezionare un progetto attivo eseguendo il `oc project` comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Preparare il file di configurazione di Prometheus
Creare un file prometheus.yml immettendo il seguente contenuto:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Creare un segreto denominato Prom immettendo la configurazione seguente:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Il file prometheus.yml è un file di configurazione di Prometheus base. Imposta gli intervalli e consente di configurare l'individuazione automatica in tre progetti (prometheus a progetto, project1 di app, app-project2). Nel file di configurazione precedente, gli endpoint di individuazione automatica sono scorporati su HTTP senza autenticazione.

Per altre informazioni sull'endpoint di scraping, vedere [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Preparare il file di configurazione Alertmanager
Creare un file alertmanager.yml immettendo il contenuto seguente:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Creare un segreto denominato Prom-Alerts immettendo la configurazione seguente:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml è il file di configurazione di gestione avvisi.

> [!NOTE]
> Per verificare se i due passaggi precedenti, eseguire il `oc get secret -n prometheus-project` comando.

## <a name="start-prometheus-and-alertmanager"></a>Avviare Prometheus e Alertmanager
Passare a [openshift/repository origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e scaricare la [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) modello. Applicare il modello di progetto di prometheus immettendo la configurazione seguente:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Il file di prometheus standalone.yaml è un modello di OpenShift. Creerà un'istanza di Prometheus con oauth-proxy davanti a e un'istanza Alertmanager, anche protetti con il proxy di oauth. In questo modello, oauth-proxy è configurato per consentire tutti gli utenti possono "get" lo spazio dei nomi progetto di prometheus (vedere il `-openshift-sar` flag).

> [!NOTE]
> Per verificare se l'oggetto StatefulSet prom sia uguale DESIDERATA e numero di repliche corrente, eseguire il `oc get statefulset -n prometheus-project` comando. Per controllare tutte le risorse nel progetto, eseguire il `oc get all -n prometheus-project` comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Aggiungere le autorizzazioni per consentire l'individuazione del servizio

Creare un file di prometheus sdrole.yml immettendo il seguente contenuto:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Per modello applicato a tutti i progetti che si desidera consentire l'individuazione del servizio, eseguire i comandi seguenti:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Per consentire a Prometheus per raccogliere metriche da se stesso, applicare le autorizzazioni nel progetto di prometheus.

> [!NOTE]
> Per verificare che ruoli e RoleBinding siano stati creati correttamente, eseguire la `oc get role` e `oc get rolebinding` comandi.

## <a name="optional-deploy-example-application"></a>Facoltativo: Distribuire l'applicazione di esempio

Tutto funziona, ma non sono presenti origini delle metriche. Passare all'URL di Prometheus (https://prom-prometheus-project.apps. *id casuale*. *area*.azmosa.io/). È possibile trovarlo tramite il seguente comando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Ricordarsi di aggiungere il prefisso https:// all'inizio del nome host.

Il **stato > Service Discovery** pagina mostrerà destinazioni active 0 o 0.

Per distribuire un'applicazione di esempio, che espone le metriche di base Python sotto l'endpoint /metrics, eseguire i comandi seguenti:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Le nuove applicazioni devono essere visualizzati come destinazioni valide nella pagina di individuazione servizio entro 30 secondi dopo la distribuzione.

Per altri dettagli, selezionare **lo stato** > **destinazioni**.

> [!NOTE]
> Per ogni destinazione ottenute correttamente, Prometheus aggiunge un punto dati nella metrica rapidamente. Selezionare **Prometheus** nell'angolo superiore sinistro, immettere **backup** come espressione e quindi selezionare **Execute**.

## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere strumentazione di Prometheus personalizzata alle applicazioni. La libreria Client di Prometheus, che semplifica la preparazione di Prometheus metriche, è pronta per diversi linguaggi di programmazione.

Per altre informazioni, vedere le librerie di GitHub seguenti:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
