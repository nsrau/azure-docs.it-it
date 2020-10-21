---
title: Creare un'iperscalabilità PostgreSQL abilitata per Azure Arc con Azure Data Studio
description: Creare un'iperscalabilità PostgreSQL abilitata per Azure Arc con Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310909"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Creare un'iperscalabilità PostgreSQL abilitata per Azure Arc con Azure Data Studio

Questo documento illustra i passaggi per l'uso di Azure Data Studio per eseguire il provisioning di gruppi di server con iperscalabilità PostgreSQL abilitati per Azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Connettersi al controller di dati di Azure Arc

Prima di poter creare un'istanza, accedere al controller dati di Azure Arc se non è già stato effettuato l'accesso.

```console
azdata login
```

Verrà quindi richiesto lo spazio dei nomi in cui viene creato il controller dati, il nome utente e la password per accedere al controller.

> Se è necessario convalidare lo spazio dei nomi, è possibile eseguire ```kubectl get pods -A``` per ottenere un elenco di tutti gli spazi dei nomi nel cluster.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Passaggio preliminare e temporaneo per solo gli utenti OpenShift

Implementare questo passaggio prima di passare al passaggio successivo. Per distribuire il gruppo di server con iperscalabilità PostgreSQL in Red Hat OpenShift in un progetto diverso da quello predefinito, è necessario eseguire i comandi seguenti sul cluster per aggiornare i vincoli di sicurezza. Questo comando concede i privilegi necessari agli account del servizio che eseguiranno il gruppo di server di iperscala PostgreSQL. Il vincolo del contesto di sicurezza (SCC) **_Arc-data-SCC_** è quello aggiunto al momento della distribuzione del controller di dati di Azure Arc.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Nome-gruppo-Server** è il nome del gruppo di server che verrà distribuito durante il passaggio successivo._
   
Per ulteriori informazioni su SCCs in OpenShift, consultare la documentazione di [OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
È ora possibile implementare il passaggio successivo.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Creare un gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc

1. Avvia Azure Data Studio
1. Nella scheda connessioni fare clic sui tre puntini in alto a sinistra e scegliere "nuova distribuzione".
1. Dalle opzioni di distribuzione selezionare **gruppo di server con iperscalabilità PostgreSQL-Azure Arc**
    >[!NOTE]
    > Se non è attualmente installato, potrebbe essere richiesto di installare [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] qui.
1. Accetta le condizioni di licenza e privacy e fai clic su **Seleziona** nella parte inferiore
1. Nel pannello Distribuisci un gruppo di server con iperscalabilità PostgreSQL-Azure Arc immettere le informazioni seguenti:
   - Immettere un nome per il gruppo di server
   - Immettere e confermare una password per l'utente amministratore _Postgres_ del gruppo di server
   - Selezionare la classe di archiviazione in base alle esigenze dei dati
   - Selezionare la classe di archiviazione in base alle esigenze dei log
   - Selezionare la classe di archiviazione in base alle esigenze dei backup
   - Selezionare il numero di nodi di lavoro di cui eseguire il provisioning
1. Fare clic sul pulsante **Distribuisci** .

Viene avviata la creazione del gruppo di server di iperscalabilità PostgreSQL abilitato per Azure Arc sul controller dati.

In pochi minuti la creazione dovrebbe essere completata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- [Gestire il gruppo di server utilizzando Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Monitorare il gruppo di server](monitor-grafana-kibana.md)
- Leggi i concetti e le guide alle procedure per la scalabilità di database di Azure per PostgreSQL per la distribuzione dei dati in più nodi di iperscala PostgreSQL e per sfruttare tutte le potenzialità del database di Azure per la scalabilità Ipergres. :
    * [Nodi e tabelle](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinare il tipo di applicazione](../../postgresql/concepts-hyperscale-app-type.md)
    * [Scegliere una colonna di distribuzione](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Condivisione delle tabelle](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuire e modificare tabelle](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Progettare un database multi-tenant](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Progettare un dashboard di analisi in tempo reale](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Nei documenti precedenti ignorare le sezioni accedere al **portale di Azure**& **creare un database di Azure per PostgreSQL-iperscalabilità (CITUS)**. Implementare i passaggi rimanenti nella distribuzione di Azure Arc. Queste sezioni sono specifiche dell'iperscalabilità di database di Azure per PostgreSQL (CITUS) offerta come servizio PaaS nel cloud di Azure, ma le altre parti dei documenti sono direttamente applicabili all'iperscalabilità di PostgreSQL abilitata per Azure Arc.

- [Aumentare le istanze in Database di Azure per il gruppo di server PostgreSQL Hyperscale](scale-out-postgresql-hyperscale-server-group.md)
- [Concetti relativi alla configurazione dell'archiviazione e all'archiviazione Kubernetes](storage-configuration.md)
- [Modello di risorsa Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

