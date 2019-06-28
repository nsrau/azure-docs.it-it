---
title: Distribuire un Prometheus autonomo in un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Di seguito viene illustrato come creare un'istanza di Prometheus in un cluster Azure Red Hat OpenShift per monitorare le metriche dell'applicazione.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, metriche, red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342537"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Distribuire un Prometheus autonomo in un cluster Azure Red Hat OpenShift

Questa guida descrive come configurare un computer autonomo Prometheus con individuazione dei servizi in un cluster Azure Red Hat OpenShift.  NON è necessario l'accesso "Amministratore del cliente" al cluster.

La configurazione di destinazione è come segue:

- un progetto (prometheus-), che contiene Prometheus e Alertmanager
- due progetti (project1 di app e app-project2), che contengono le applicazioni da monitorare

Viene illustrato come preparare alcuni file di configurazione di Prometheus in locale. Creare una nuova cartella in cui archiviarli.
Questi file di configurazione verranno archiviati nel cluster come segreti nel caso in cui i token segreto a essi aggiunti in un secondo momento.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>Passaggio 1: Accedere al cluster tramite il `oc` strumento
Usando un web browser, passare alla console web del cluster (https://openshift. *id casuale*. *area*. azmosa.io).
Accedere con le credenziali di Azure.
Fare clic sul proprio nome utente in alto a destra e selezionare "Copia Login Command". Incollarlo nel terminale che verrà usato.

È possibile verificare se si è connessi al cluster corretto con il `oc whoami -c` comando.

## <a name="step-2-prepare-the-projects"></a>Passaggio 2: Preparare i progetti

Creare progetti.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> È possibile usare la `-n` oppure `--namespace` parametro o selezionare il progetto con un oggetto attivo il `oc project` comando

## <a name="step-3-prepare-prometheus-config"></a>Passaggio 3: Prepara configurazione di Prometheus
Creare un file denominato prometheus.yml con il contenuto seguente.
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
Creare un segreto denominato "prom" con la configurazione.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Il file elencato in precedenza è un file di configurazione di Prometheus base.
Imposta gli intervalli e consente di configurare l'individuazione automatica in tre progetti (prometheus a progetto, project1 di app, app-project2).
In questo esempio, l'auto individuati gli endpoint verranno scorporati tramite HTTP senza autenticazione.
Per altre informazioni su scraping gli endpoint, vedere https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config.


## <a name="step-4-prepare-alertmanager-config"></a>Passaggio 4: Preparare Alertmanager config
Creare un file denominato alertmanager.yml con il contenuto seguente.
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
Creare un segreto denominato "prom-alerts" con la configurazione.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Il file elencato in precedenza è il file di configurazione di gestione avvisi.

> [!NOTE]
> È possibile verificare i due passaggi precedenti con `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>Passaggio 5: Avviare Prometheus e Alertmanager
Scaricare il [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) modello dalle [openshift/repository origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e applicarlo nel progetto di prometheus
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Il file di prometheus standalone.yaml è un modello di OpenShift, che verrà creata un'istanza di Prometheus con un proxy di oauth davanti a e un'istanza Alertmanager, anche protetti con il proxy di oauth.  In questo modello oauth per il proxy è configurato per consentire tutti gli utenti possono "get" lo spazio dei nomi "prometheus-project" (vedere il `-openshift-sar` flag).

> [!NOTE]
> È possibile verificare se l'oggetto StatefulSet "prom" è uguale *DESIRED* e *corrente* numero di repliche con i `oc get statefulset -n prometheus-project` comando.
> È inoltre possibile controllare tutte le risorse nel progetto con `oc get all -n prometheus-project`.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>Passaggio 6: Aggiungere le autorizzazioni per consentire l'individuazione del servizio
Creare prometheus sdrole.yml con il contenuto seguente.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
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
Applicare il modello a tutti i progetti in cui si desidera consentire l'individuazione del servizio.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Se si vuole anche Prometheus per essere in grado di raccogliere le metriche da se stessa, ricordarsi di applicare le autorizzazioni nel progetto di prometheus troppo.

> [!NOTE]
> È possibile verificare se il ruolo e RoleBinding sono stati creati correttamente con il `oc get role` e `oc get rolebinding` comandi rispettivamente

## <a name="optional-deploy-example-application"></a>Facoltativo: Distribuire l'applicazione di esempio
Tutto funziona, ma non sono presenti origini delle metriche. Passare all'URL di Prometheus (https://prom-prometheus-project.apps. *id casuale*. *area*.azmosa.io/), che è disponibile con il comando seguente.
```
oc get route prom -n prometheus-project
```
Ricordarsi di prefisso del nome host con https://.

Il **stato > Service Discovery** pagina mostrerà destinazioni active 0 o 0.

Per distribuire un'applicazione di esempio, che espone le metriche di base python nell'endpoint di /metrics eseguire i comandi seguenti.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Le nuove applicazioni vengono visualizzati come destinazioni valide nella pagina di individuazione servizio entro 30 secondi dopo la distribuzione. È possibile ottenere altri dettagli **stato > destinazioni** pagina.

> [!NOTE]
> Per ogni destinazione ottenute correttamente Prometheus aggiunge un elemento datapoint la metrica "massimo". Fare clic su **Prometheus** in alto a sinistra angolo ed immettere "up" come l'espressione, quindi scegliere **Execute**.

## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere strumentazione di Prometheus personalizzata alle applicazioni.

La libreria Client di Prometheus, semplificando la preparazione delle metriche di Prometheus è pronta per diversi linguaggi di programmazione.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Vai https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
