---
title: Distribuire l'istanza prometeo nel cluster Azure Red Hat OpenShiftDeploy Prometheus instance in Azure Red Hat OpenShift cluster
description: Creare un'istanza Prometheus in un cluster Azure Red Hat OpenShift per monitorare le metriche dell'applicazione.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometeo, aro, openshift, metriche, cappello rosso
ms.openlocfilehash: 7f22df587f51af735e0ea663e53f6eef14d60692
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886889"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Distribuire un'istanza Prometheus autonoma in un cluster Azure Red Hat OpenShiftDeploy a standalone Prometheus instance in an Azure Red Hat OpenShift cluster

Questo articolo descrive come configurare un'istanza Prometheus autonoma che usa l'individuazione dei servizi in un cluster Azure Red Hat OpenShift.This article describes how to configure a standalone Prometheus instance that uses service discovery in an Azure Red Hat OpenShift cluster.

> [!NOTE]
> L'accesso di amministratore dei clienti al cluster OpenShift di Azure Red Hat non è necessario.

Configurazione di destinazione:

- Un progetto (prometheus-project), che contiene Prometheus e Alertmanager.
- Due progetti (app-project1 e app-project2), che contengono le applicazioni da monitorare.

Si prepareranno alcuni file di configurazione Prometheus in locale. Creare una nuova cartella per archiviarli. I file di configurazione vengono archiviati nel cluster come segreti, nel caso in cui i token segreti vengano aggiunti successivamente al cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Accedere al cluster utilizzando lo strumento OC

1. Aprire un Web browser e quindi passare allahttps://openshiftconsole Web del cluster ( .* id-casuale*. *regione*.azmosa.io).
2. Accedere con le credenziali di Azure.
3. Selezionare il nome utente nell'angolo superiore destro, quindi selezionare **Copia comando**di accesso .
4. Incolla il tuo nome utente nel terminale che userai.

> [!NOTE]
> Per verificare se è stato eseguito l'accesso al cluster corretto, eseguire il `oc whoami -c` comando.

## <a name="prepare-the-projects"></a>Preparare i progetti

Per creare i progetti, eseguire i seguenti comandi:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> È possibile utilizzare `-n` `--namespace` il parametro o oppure `oc project` selezionare un progetto attivo eseguendo il comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Preparare il file di configurazione Prometheus
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
Creare un segreto denominato Prom immettendo la seguente configurazione:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Il file prometheus.yml è un file di configurazione Prometheus di base. Imposta gli intervalli e configura l'individuazione automatica in tre progetti (prometheus-project, app-project1, app-project2). Nel file di configurazione precedente, gli endpoint individuati automaticamente vengono eliminati tramite HTTP senza autenticazione.

Per ulteriori informazioni sull'eliminazione degli endpoint, vedere [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Preparare il file di configurazione di AlertmanagerPrepare the Alertmanager config file
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
Creare un segreto denominato Prom-Alerts immettendo la seguente configurazione:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml è il file di configurazione di Gestione avvisi.

> [!NOTE]
> Per verificare i due `oc get secret -n prometheus-project` passaggi precedenti, eseguire il comando.

## <a name="start-prometheus-and-alertmanager"></a>Avviare Prometheus e Alertmanager
Vai al [repository openshift/origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e scarica il modello [prometheus-standalone.yaml.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) Applicare il modello a prometheus-project immettendo la seguente configurazione:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Il file prometheus-standalone.yaml è un modello OpenShift. Verrà creata un'istanza Prometheus con oauth-proxy di fronte ad essa e un'istanza Alertmanager, anch'essa protetta con oauth-proxy. In questo modello, oauth-proxy è configurato per consentire a qualsiasi utente che può `-openshift-sar` "ottenere" lo spazio dei nomi prometheus-project (vedere il flag).

> [!NOTE]
> Per verificare se il prom StatefulSet ha repliche `oc get statefulset -n prometheus-project` di numeri DESIRED e CURRENT uguali, eseguire il comando. Per controllare tutte le risorse `oc get all -n prometheus-project` nel progetto, eseguire il comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Aggiungere autorizzazioni per consentire l'individuazione dei serviziAdd permissions to allow service discovery

Creare un file prometheus-sdrole.yml immettendo il seguente contenuto:
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
Per applicare il modello a tutti i progetti da cui si desidera consentire l'individuazione dei servizi, eseguire i comandi seguenti:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Per verificare che Role e RoleBinding `oc get role` `oc get rolebinding` siano stati creati correttamente, eseguire i comandi e .

## <a name="optional-deploy-example-application"></a>Facoltativo: Distribuire un'applicazione di esempioOptional: Deploy example application

Tutto funziona, ma non ci sono origini metriche. Passare all'URL del prometeo (https://prom-prometheus-project.apps.* id-casuale*. *regione*.azmosa.io/). È possibile trovarlo utilizzando il seguente comando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Ricordarsi di aggiungere il prefisso https:// all'inizio del nome host.

Nella pagina **Stato > individuazione del servizio** verranno visualizzate le destinazioni attive 0/0.The Status > Service Discovery page will show 0/0 active targets.

Per distribuire un'applicazione di esempio, che espone metriche Python di base nell'endpoint /metrics, eseguire i comandi seguenti:To deploy an example application, which exposes basic Python metrics under the /metrics endpoint, run the following commands:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Le nuove applicazioni devono essere visualizzate come destinazioni valide nella pagina Individuazione servizio entro 30 secondi dalla distribuzione.

Per ulteriori dettagli, selezionare**Destinazioni** **stato** > .

> [!NOTE]
> Per ogni obiettivo raschiato con successo, Prometheus aggiunge un punto dati nella metrica verso l'alto. Selezionare **Prometheus** nell'angolo superiore sinistro, immettere **up** come espressione e quindi selezionare **Esegui**.

## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere la strumentazione Prometheus personalizzata alle applicazioni. La libreria Prometheus Client, che semplifica la preparazione delle metriche di Prometheus, è pronta per diversi linguaggi di programmazione.

Per altre informazioni, vedere le seguenti librerie GitHub:For more information, see the following GitHub libraries:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [invio](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
