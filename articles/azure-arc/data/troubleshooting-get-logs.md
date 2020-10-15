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
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939115"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Ottenere i log dei servizi dati abilitati per Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Per recuperare i log di Azure Arc Enabled Data Services, sarà necessario lo strumento dell'interfaccia della riga di comando di Azure Data. [Istruzioni per l'installazione](./install-client-tools.md)

È necessario essere in grado di accedere al servizio del controller di servizi dati abilitato per Azure Arc come amministratore.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Ottenere i log dei servizi dati abilitati per Azure Arc

Per la risoluzione dei problemi, è possibile ottenere i log di Azure Arc Enabled Data Services in tutti i pod o POD specifici.  È possibile eseguire questa operazione usando gli strumenti Kubernetes standard come il `kubectl logs` comando o in questo articolo si utilizzerà lo strumento dell'interfaccia della riga di comando di Azure, che semplifica l'ottenimento di tutti i log contemporaneamente.

Prima di tutto, assicurarsi di essere connessi al controller dati.

```console
azdata login
```

Eseguire quindi il comando seguente per eseguire il dump dei log:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

Per impostazione predefinita, i file di log verranno creati nella directory di lavoro corrente in una sottodirectory denominata "logs".  È possibile eseguire l'output dei file di log in una directory diversa utilizzando il `--target-folder` parametro.

È possibile scegliere di comprimere i file omettendo il `--skip-compress` parametro.

È possibile attivare e includere i dump della memoria omettendo `--exclude-dumps` , ma questa operazione non è consigliata, a meno che supporto tecnico Microsoft non abbia richiesto i dump della memoria.  Per l'esecuzione di un dump della memoria è necessario che l'impostazione del controller `allowDumps` di dati sia impostata `true` sull'ora di creazione del controller dei dati.

Facoltativamente, è possibile scegliere di filtrare per raccogliere i log solo per un pod specifico ( `--pod` ) o un contenitore ( `--container` ) in base al nome.

È anche possibile scegliere di filtrare per raccogliere i log per una risorsa personalizzata specifica passando il `--resource-kind` parametro e il `--resource-name` parametro.  Il `resource-kind` valore del parametro deve essere uno dei nomi di definizione di risorsa personalizzati che possono essere recuperati dal comando `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Esempio di gerarchia di cartelle.  Si noti che la gerarchia di cartelle è organizzata in base al nome del Pod e quindi in base al contenitore e quindi alla gerarchia di directory all'interno del contenitore.

```console
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