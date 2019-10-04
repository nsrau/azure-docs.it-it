---
title: Distribuire un'istanza di Prometeo autonoma in un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Creare un'istanza di Prometeo in un cluster Azure Red Hat OpenShift per monitorare le metriche dell'applicazione.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometeo, Aro, OpenShift, metriche, Red Hat
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875129"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Distribuire un'istanza di Prometeo autonoma in un cluster Azure Red Hat OpenShift

Questo articolo descrive come configurare un'istanza di Prometeo autonoma che usa l'individuazione del servizio in un cluster OpenShift di Azure Red Hat.

> [!NOTE]
> L'accesso dell'amministratore del cliente al cluster Azure Red Hat OpenShift non è obbligatorio.

Configurazione di destinazione:

- Un progetto (Prometheus-Project), che contiene Prometheus e Alertmanager.
- Due progetti (app-Project1 e app-Progetto2) che contengono le applicazioni da monitorare.

I file di configurazione Prometheus verranno preparati localmente. Creare una nuova cartella per archiviarle. I file di configurazione vengono archiviati nel cluster come segreti, nel caso in cui i token segreti vengano aggiunti successivamente al cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Accedere al cluster usando lo strumento OC

1. Aprire un Web browser e quindi passare alla console Web del cluster (https://openshift. *ID casuale*. *Region*. azmosa.io).
2. Accedere con le credenziali di Azure.
3. Selezionare il nome utente nell'angolo in alto a destra e quindi selezionare **copia login comando**.
4. Incollare il nome utente nel terminale che verrà usato.

> [!NOTE]
> Per verificare se è stato effettuato l'accesso al cluster corretto, eseguire il `oc whoami -c` comando.

## <a name="prepare-the-projects"></a>Preparare i progetti

Per creare i progetti, eseguire i comandi seguenti:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> È possibile usare il `-n` parametro o `--namespace` oppure selezionare un progetto attivo eseguendo il `oc project` comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Preparare il file di configurazione Prometheus
Per creare un file Prometheus. yml, immettere il contenuto seguente:
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
Per creare un segreto denominato Prom, immettere la configurazione seguente:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Il file Prometheus. yml è un file di configurazione Prometheus di base. Imposta gli intervalli e configura l'individuazione automatica in tre progetti (Prometheus-Project, app-Project1, app-Progetto2). Nel file di configurazione precedente, gli endpoint individuati automaticamente vengono ricavati tramite HTTP senza autenticazione.

Per ulteriori informazioni sul frammento degli endpoint, vedere la pagina relativa alla [configurazione di Prometeo scape](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Preparare il file di configurazione Alertmanager
Per creare un file alertmanager. yml, immettere il contenuto seguente:
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
Per creare un segreto denominato Prom-Alerts, immettere la configurazione seguente:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yml è il file di configurazione di Alert Manager.

> [!NOTE]
> Per verificare i due passaggi precedenti, eseguire il `oc get secret -n prometheus-project` comando.

## <a name="start-prometheus-and-alertmanager"></a>Avviare Prometheus e Alertmanager
Passare al [repository OpenShift/Origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e scaricare il [modello Prometheus-standalone.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) YAML. Applicare il modello a Prometheus-Project immettendo la configurazione seguente:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Il file Prometheus-standalone. YAML è un modello OpenShift. Verrà creata un'istanza di Prometeo con OAuth-proxy davanti a essa e un'istanza di Alertmanager, anche protetta con OAuth-proxy. In questo modello, OAuth-proxy è configurato per consentire a qualsiasi utente che può "ottenere" lo spazio dei nomi Prometheus-Project `-openshift-sar` (vedere il flag).

> [!NOTE]
> Per verificare se il StatefulSet di Prom ha le repliche dei numeri desiderate e correnti `oc get statefulset -n prometheus-project` uguali, eseguire il comando. Per controllare tutte le risorse del progetto, eseguire il `oc get all -n prometheus-project` comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Aggiungere autorizzazioni per consentire l'individuazione del servizio

Per creare un file Prometheus-sdrole. yml, immettere il contenuto seguente:
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
Per applicare il modello a tutti i progetti da cui si desidera consentire l'individuazione del servizio, eseguire i comandi seguenti:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Per verificare che i ruoli e i ruoli siano stati creati correttamente, `oc get role` eseguire `oc get rolebinding` i comandi e.

## <a name="optional-deploy-example-application"></a>Facoltativo: Distribuire un'applicazione di esempio

Tutto funziona, ma non sono presenti fonti di metriche. Passare all'URL di Prometheus (https://prom-prometheus-project.apps.*id casuale*.*area*.azmosa.io/). È possibile trovarlo usando il comando seguente:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Ricordarsi di aggiungere il prefisso https://all'inizio del nome host.

La pagina di **individuazione del servizio stato >** Visualizza 0/0 destinazioni attive.

Per distribuire un'applicazione di esempio, che espone le metriche di base di Python nell'endpoint/Metrics, eseguire i comandi seguenti:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Le nuove applicazioni dovrebbero essere visualizzate come destinazioni valide nella pagina Individuazione servizio entro 30 secondi dopo la distribuzione.

Per altri dettagli, selezionare**destinazioni**di **stato** > .

> [!NOTE]
> Per ogni destinazione ricavata correttamente, Prometeo aggiunge un punto dati nella metrica in alto. Selezionare **Prometheus** nell'angolo superiore sinistro, immettere come espressione , quindi selezionare **Execute (Esegui**).

## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere strumentazione Prometheus personalizzata alle applicazioni. La libreria client Prometheus, che semplifica la preparazione della metrica Prometeo, è pronta per diversi linguaggi di programmazione.

Per ulteriori informazioni, vedere le librerie di GitHub seguenti:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
