---
title: Ottenere i log per risolvere i problemi relativi ai servizi dati abilitati per Azure Arc
description: Informazioni su come ottenere i file di log da un controller di dati per risolvere i problemi relativi a Azure Arc Enabled Data Services.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234045"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Ottenere i log per risolvere i problemi relativi ai servizi dati abilitati per Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, è necessario:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Per altre informazioni, vedere [installare gli strumenti client per la distribuzione e la gestione di Azure Arc Data Services](./install-client-tools.md).
* Un account amministratore per accedere al controller dati abilitato per Azure Arc.

## <a name="get-log-files"></a>Ottenere i file di log

È possibile ottenere i log del servizio in tutti i pod o POD specifici per la risoluzione dei problemi. Un modo consiste nell'usare gli strumenti Kubernetes standard, ad esempio il `kubectl logs` comando. In questo articolo si userà lo [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] strumento, che rende più semplice ottenere tutti i log in una sola volta.

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

Il `azdata arc dc debug copy-logs` comando fornisce le opzioni seguenti per gestire l'output:

* Eseguire l'output dei file di log in una directory diversa utilizzando il `--target-folder` parametro.
* Per comprimere i file, omettere il `--skip-compress` parametro.
* Attivare e includere i dump della memoria omettendo `--exclude-dumps` . Questo metodo non è consigliato a meno che supporto tecnico Microsoft non abbia richiesto i dump della memoria. Per ottenere un dump della memoria, è necessario che l'impostazione del controller `allowDumps` di dati sia impostata su `true` quando viene creato il controller di dati.
* Filtrare per raccogliere i log solo per uno specifico pod ( `--pod` ) o contenitore ( `--container` ) in base al nome.
* Filtrare per raccogliere i log per una risorsa personalizzata specifica passando i `--resource-kind` `--resource-name` parametri e. Il `resource-kind` valore del parametro deve essere uno dei nomi di definizione di risorsa personalizzata. È possibile recuperare tali nomi tramite il comando `kubectl get customresourcedefinition` .

Con questi parametri, è possibile sostituire `<parameters>` nell'esempio seguente: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Ad esempio:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

La gerarchia di cartelle seguente è un esempio. È organizzato in base al nome del Pod, quindi al contenitore e quindi alla gerarchia di directory all'interno del contenitore.

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
