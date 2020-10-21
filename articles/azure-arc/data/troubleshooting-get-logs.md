---
title: Ottenere i log per risolvere i problemi del controller di dati abilitato per Azure Arc
description: Ottenere i log del servizio per risolvere i problemi del controller di dati abilitato per Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 625092e0557d40051e1ffd538a496c20edc0222f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320194"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Ottenere i log dei servizi dati abilitati per Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, è necessario:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. [Istruzioni di installazione](./install-client-tools.md).
* Un account amministratore per accedere al controller dei servizi dati abilitati per Azure Arc.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Ottenere i log dei servizi dati abilitati per Azure Arc

Per la risoluzione dei problemi, è possibile ottenere i log di Azure Arc Enabled Data Services in tutti i pod o POD specifici. A tale scopo, è possibile usare gli strumenti Kubernetes standard, ad esempio il `kubectl logs` comando, oppure in questo articolo verrà usato lo [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] strumento, che rende più semplice ottenere tutti i log contemporaneamente.

1. Accedere al controller dati con un account amministratore.

   ```console
   azdata login
   ```

2. Eseguire il comando seguente per eseguire il dump dei log:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Ad esempio:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Il controller dati crea i file di log nella directory di lavoro corrente in una sottodirectory denominata `logs` . 

## <a name="options"></a>Opzioni

`azdata arc dc debug copy-logs` fornisce le opzioni seguenti per gestire l'output.

* Eseguire l'output dei file di log in una directory diversa utilizzando il `--target-folder` parametro.
* Per comprimere i file, omettere il `--skip-compress` parametro.
* Attivare e includere i dump della memoria omettendo il `--exclude-dumps` . Questo metodo non è consigliato a meno che supporto tecnico Microsoft non abbia richiesto i dump della memoria. Per l'esecuzione di un dump della memoria è necessario che l'impostazione del controller `allowDumps` di dati sia impostata `true` sull'ora di creazione del controller dei dati.
* Filtrare per raccogliere i log solo per uno specifico pod ( `--pod` ) o contenitore ( `--container` ) in base al nome.
* Filtrare per raccogliere i log per una risorsa personalizzata specifica passando il `--resource-kind` `--resource-name` parametro e. Il `resource-kind` valore del parametro deve essere uno dei nomi di definizione di risorsa personalizzati, che possono essere recuperati dal comando `kubectl get customresourcedefinition` .

Con questi parametri, è possibile sostituire `<parameters>` nell'esempio seguente. 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Ad esempio:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Esempio di gerarchia di cartelle. La gerarchia di cartelle è organizzata in base al nome del Pod, quindi al contenitore e quindi alla gerarchia di directory all'interno del contenitore.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Passaggi successivi

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)