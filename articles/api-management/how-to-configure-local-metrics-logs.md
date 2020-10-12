---
title: Configurare le metriche e i log locali per il gateway self-hosted di gestione API di Azure | Microsoft Docs
description: Informazioni su come configurare le metriche e i log locali per il gateway self-hosted di gestione API di Azure
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: ac147863fe54be3343eda653fc863ebd08dac54d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254504"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Configurare le metriche e i log locali per il gateway self-hosted di gestione API di Azure

Questo articolo fornisce informazioni dettagliate per la configurazione di metriche e log locali per il [gateway self-hosted](./self-hosted-gateway-overview.md). Per la configurazione delle metriche e dei log del cloud, vedere [questo articolo](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Metriche
Il gateway self-hosted supporta le [statistiche](https://github.com/statsd/statsd), che sono diventate un protocollo unificante per la raccolta e l'aggregazione di metriche. Questa sezione illustra i passaggi per la distribuzione delle statistiche in Kubernetes, la configurazione del gateway per l'emissione di metriche tramite statistiche e l'uso di [Prometeo](https://prometheus.io/) per monitorare le metriche. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Distribuire statistiche e Prometeo nel cluster

Di seguito è riportata una configurazione YAML di esempio per la distribuzione di statistiche e Prometeo al cluster Kubernetes in cui viene distribuito un gateway self-hosted. Viene inoltre creato un [servizio](https://kubernetes.io/docs/concepts/services-networking/service/) per ogni. Il gateway self-hosted pubblicherà le metriche nel servizio Statistics. Si accederà al dashboard Prometheus tramite il servizio.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: prom/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Salvare le configurazioni in un file denominato `metrics.yaml` e usare il comando seguente per distribuire tutti gli elementi nel cluster:

```console
kubectl apply -f metrics.yaml
```

Al termine della distribuzione, eseguire il comando seguente per verificare che i pod siano in esecuzione. Si noti che il nome del pod sarà diverso. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Eseguire il comando seguente per verificare che i servizi siano in esecuzione. Prendere nota di e del `CLUSTER-IP` `PORT` servizio Statistics, che sarà necessario in un secondo momento. È possibile visitare il dashboard Prometheus usando `EXTERNAL-IP` e `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Configurare il gateway self-hosted per emettere le metriche

Ora che sono stati distribuiti sia statsd che Prometeo, è possibile aggiornare le configurazioni del gateway self-hosted per iniziare a emettere metriche tramite statistiche. La funzionalità può essere abilitata o disabilitata usando la `telemetry.metrics.local` chiave nella ConfigMap della distribuzione del gateway self-hosted con opzioni aggiuntive. Di seguito è riportata una suddivisione delle opzioni disponibili:

| Campo  | Predefinito | Descrizione |
| ------------- | ------------- | ------------- |
| telemetria. metrica. locale  | `none` | Abilita la registrazione tramite statistiche. Il valore può `none` essere `statsd` . |
| telemetria. metrica. local. Statistics. endpoint  | n/d | Specifica l'endpoint Statistics. |
| telemetria. metrica. local. Statistics. campionamento  | n/d | Specifica la frequenza di campionamento della metrica. Il valore può essere compreso tra 0 e 1. e.g. `0.5`|
| telemetria. metrica. local. Statistics. Tag-Format  | n/d | Formato per l' [assegnazione di tag](https://github.com/prometheus/statsd_exporter#tagging-extensions)all'utilità di esportazione statistica. Il valore può essere `none` ,, `librato` `dogStatsD` , `influxDB` . |

Ecco una configurazione di esempio:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Aggiornare il file YAML della distribuzione del gateway self-hosted con le configurazioni precedenti e applicare le modifiche usando il comando seguente: 

```console
kubectl apply -f <file-name>.yaml
 ```

Per individuare le modifiche di configurazione più recenti, riavviare la distribuzione del gateway usando il comando seguente:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Visualizzare le metriche

Ora che tutti gli elementi sono stati distribuiti e configurati, il gateway self-hosted deve segnalare le metriche tramite statistiche. Prometeo rileverà le metriche dalle statistiche. Passare al dashboard di Prometeo usando `EXTERNAL-IP` e `PORT` del servizio Prometheus. 

Eseguire alcune chiamate API tramite il gateway self-hosted, se tutti gli elementi sono configurati correttamente, dovrebbe essere possibile visualizzare le metriche seguenti:

| Metrica  | Descrizione |
| ------------- | ------------- |
| Requests  | Numero di richieste API nel periodo |
| DurationInMS | Numero di millisecondi dal momento in cui il gateway ha ricevuto la richiesta al momento dell'invio della risposta completa |
| BackendDurationInMS | Numero di millisecondi impiegati complessivamente per l'I/O del back-end (connessione, invio e ricezione byte)  |
| ClientDurationInMS | Numero di millisecondi impiegati complessivamente per l'I/O del client (connessione, invio e ricezione byte)  |

## <a name="logs"></a>Log

Il gateway self-hosted restituisce i log a `stdout` e `stderr` per impostazione predefinita. È possibile visualizzare facilmente i log usando il comando seguente:

```console
kubectl logs <pod-name>
```

Se il gateway self-hosted viene distribuito nel servizio Azure Kubernetes, è possibile abilitare [monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-overview.md) per la raccolta `stdout` e `stderr` i carichi di lavoro e per visualizzare i log in log Analytics. 

Il gateway self-hosted supporta inoltre diversi protocolli, tra cui `localsyslog` , `rfc5424` e `journal` . Nella tabella seguente sono riepilogate tutte le opzioni supportate. 

| Campo  | Predefinito | Descrizione |
| ------------- | ------------- | ------------- |
| telemetria. logs. STD  | `text` | Abilita la registrazione nei flussi standard. Il valore può essere `none` , `text` , `json` |
| telemetria. logs. local  | `none` | Abilita la registrazione locale. Il valore può essere `none` ,, `auto` `localsyslog` , `rfc5424` , `journal`  |
| telemetria. logs. local. localsyslog. endpoint  | n/d | Specifica l'endpoint localsyslog.  |
| telemetria. logs. local. localsyslog. Facility  | n/d | Specifica il [codice della funzionalità](https://en.wikipedia.org/wiki/Syslog#Facility)localsyslog. e.g. `7` 
| telemetria. logs. local. rfc5424. endpoint  | n/d | Specifica l'endpoint rfc5424.  |
| telemetria. logs. local. rfc5424. Facility  | n/d | Specifica il codice della funzionalità per ogni [rfc5424](https://tools.ietf.org/html/rfc5424). e.g. `7`  |
| telemetria. logs. local. Journal. endpoint  | n/d | Specifica l'endpoint Journal.  |

Ecco una configurazione di esempio della registrazione locale:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md)
* Informazioni sulla [configurazione e la permanenza dei log nel cloud](how-to-configure-local-metrics-logs.md)
