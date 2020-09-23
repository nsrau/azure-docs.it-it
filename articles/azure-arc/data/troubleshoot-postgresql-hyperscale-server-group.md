---
title: Risolvere i problemi relativi ai gruppi di server di scalabilità PostgreSQL
description: Risolvere i problemi relativi ai gruppi di server con iperscalabilità PostgreSQL con una Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c0d3d9c74be8dabaec20ff5d4c7e7cfc74d8eef
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939125"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Risoluzione dei problemi relativi ai gruppi di server di iperscala PostgreSQL

I notebook possono documentare le procedure includendo il contenuto Markdown per descrivere le operazioni da eseguire/come eseguire questa operazione. Può inoltre fornire codice eseguibile per automatizzare una stored procedure.  Questo modello è utile per tutti gli elementi, dalle procedure operative standard alle guide per la risoluzione dei problemi.

Ad esempio, è possibile risolvere i problemi relativi a un gruppo di server con iperscalabilità PostgreSQL che potrebbe avere problemi usando Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="install-tools"></a>Installare gli strumenti

Installare Azure Data Studio `kubectl` e nel `azdata` computer client usato per eseguire il notebook in Azure Data Studio. A tale scopo, seguire le istruzioni disponibili in [installare gli strumenti client](install-client-tools.md)

## <a name="update-the-path-environment-variable"></a>Aggiornare la variabile di ambiente PATH

Assicurarsi che questi strumenti possano essere richiamati da qualsiasi punto del computer client. Ad esempio, in un computer client Windows aggiornare la variabile di ambiente di sistema PATH e aggiungere la cartella in cui è stato installato kubectl.

## <a name="sign-in-with-azdata"></a>Accedi con `azdata`

Accedere al controller di dati Arc da questo computer client e prima di avviare Azure Data Studio. A tale scopo, eseguire un comando simile al seguente:

```console
azdata login --endpoint https://<IP address>:<port>
```

Sostituire `<IP address>` con l'indirizzo IP del cluster Kubernetes e `<port>` la porta su cui Kubernetes è in ascolto. Verranno richiesti il nome utente e la password. Per visualizzare altri dettagli, eseguire: _

```console
azdata login --help
```

## <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Accedere al cluster Kubernetes con kubectl

A tale scopo, è possibile usare i comandi di esempio forniti in [questo](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) post di Blog.
Eseguire comandi come:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

### <a name="the-troubleshooting-notebook"></a>Notebook per la risoluzione dei problemi

Avviare Azure Data Studio e aprire il notebook per la risoluzione dei problemi. 

Implementare i passaggi descritti in  [033-Manage-Postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) per:

1. Connettersi al controller di dati Arc
2. Fare clic con il pulsante destro del mouse sull'istanza Postgres e scegliere **[Gestisci]**
3. Selezionare il **Dashboard [diagnostica e Risolvi i problemi]**
4. Selezionare il **collegamento [risoluzione dei problemi]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio-aprire il notebook per la risoluzione dei problemi PostgreSQL":::

**TSG100: il notebook di Azure Arc abilitato per la risoluzione dei problemi di iperscalabilità di PostgreSQL** viene aperto: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio-usare il notebook per la risoluzione dei problemi PostgreSQL":::

### <a name="run-the-scripts"></a>Esecuzione degli script
Selezionare il pulsante "Esegui tutto" nella parte superiore per eseguire il notebook in una sola volta oppure è possibile eseguire tutte le celle del codice una alla volta.

Visualizzare l'output dell'esecuzione delle celle di codice per eventuali problemi potenziali.

Nel corso del tempo verranno aggiunti altri dettagli al notebook su come riconoscere i problemi comuni e come risolverli.

## <a name="next-step"></a>Passaggio successivo
- Scopri di più su [come ottenere i log per i servizi dati abilitati per Azure Arc](troubleshooting-get-logs.md)
- Scopri di più sulla [ricerca di log con Kibana](monitor-grafana-kibana.md)
- Scopri di più sul [monitoraggio con Grafana](monitor-grafana-kibana.md)
- Creare Notebook personalizzati
