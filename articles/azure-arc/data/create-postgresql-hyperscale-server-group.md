---
title: Creare un gruppo di server di scalabilità di database di Azure per PostgreSQL in Azure Arc
description: Creare un gruppo di server di scalabilità di database di Azure per PostgreSQL in Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d2eef20b4c5648b1b11f16d8e46b956fc1497181
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364423"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Creare un gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc

Questo documento descrive i passaggi per creare un gruppo di server con iperscalabilità PostgreSQL in Azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Introduzione
Se si ha già familiarità con gli argomenti riportati di seguito, è possibile ignorare questo paragrafo.
È possibile leggere alcuni argomenti importanti prima di procedere con la creazione:
- [Panoramica dei servizi dati abilitati per Azure Arc](overview.md)
- [Modalità e requisiti di connettività](connectivity.md)
- [Concetti relativi alla configurazione dell'archiviazione e all'archiviazione Kubernetes](storage-configuration.md)
- [Modello di risorsa Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Se si preferisce provare senza eseguire il provisioning di un ambiente completo, iniziare rapidamente con [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) in Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure.


## <a name="login-to-the-azure-arc-data-controller"></a>Accedere al controller dati di Azure Arc

Prima di poter creare un'istanza, è necessario prima accedere al controller dati di Azure Arc. Se è già stato effettuato l'accesso al controller dati, è possibile ignorare questo passaggio.

```console
azdata login
```

Verranno quindi richiesti il nome utente, la password e lo spazio dei nomi System.  

> Se è stato usato lo script per creare il controller dati, lo spazio dei nomi deve essere **Arc**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Passaggio preliminare e temporaneo per solo gli utenti OpenShift

Implementare questo passaggio prima di passare al passaggio successivo. Per distribuire il gruppo di server con iperscalabilità PostgreSQL in Red Hat OpenShift in un progetto diverso da quello predefinito, è necessario eseguire i comandi seguenti sul cluster per aggiornare i vincoli di sicurezza. Questo comando concede i privilegi necessari agli account del servizio che eseguiranno il gruppo di server di iperscala PostgreSQL. Il vincolo del contesto di sicurezza (SCC) **_Arc-data-SCC_** è quello aggiunto al momento della distribuzione del controller di dati di Azure Arc.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Nome-gruppo-Server** è il nome del gruppo di server che verrà creato nel passaggio successivo._
   
Per ulteriori informazioni su SCCs in OpenShift, consultare la documentazione di [OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
È ora possibile implementare il passaggio successivo.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Creare un gruppo di server di scalabilità di database di Azure per PostgreSQL

Per creare un gruppo di server con iperscalabilità di database di Azure per PostgreSQL in Azure Arc, usare il comando seguente:

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **Sono disponibili altri parametri della riga di comando.  Vedere l'elenco completo delle opzioni eseguendo `azdata arc postgres server create --help` .**
> - La classe di archiviazione usata per i backup (_--Storage-Class-backups-SCB_) viene impostata per impostazione predefinita sulla classe di archiviazione dati del controller dati, se non è specificata.
> - L'unità accettata dai parametri--Volume-Size-* è una quantità di risorse Kubernetes (un numero intero seguito da uno di questi è sufficiente (T, G, M, K, m) o dai rispettivi equivalenti di Power-of-Two (ti, Gi, mi, ki)).
> - I nomi devono avere una lunghezza inferiore a 12 caratteri e devono essere conformi alle convenzioni di denominazione DNS.
> - Verrà richiesto di immettere la password per l'utente amministratore standard _Postgres_ .  È possibile ignorare il prompt interattivo impostando la `AZDATA_PASSWORD` variabile di ambiente della sessione prima di eseguire il comando create.
> - Se il controller dati è stato distribuito usando AZDATA_USERNAME e AZDATA_PASSWORD variabili di ambiente della sessione nella stessa sessione terminal, i valori per AZDATA_PASSWORD verranno usati anche per distribuire il gruppo di server di iperscala PostgreSQL. Se si preferisce usare un'altra password, (1) aggiornare il valore per AZDATA_PASSWORD o (2) eliminare la variabile di ambiente AZDATA_PASSWORD o eliminarne il valore verrà richiesto di immettere una password in modo interattivo quando si crea un gruppo di server.
> - Il nome dell'utente amministratore predefinito per il motore di database di PostgreSQL iperscale è _Postgres_ e non può essere modificato in questo punto.
> - La creazione di un gruppo di server di iperscala PostgreSQL non registrerà immediatamente le risorse in Azure. Come parte del processo di caricamento di [inventario delle risorse](upload-metrics-and-logs-to-azure-monitor.md)  o [dei dati di utilizzo](view-billing-data-in-azure.md) in Azure, le risorse verranno create in Azure e sarà possibile visualizzare le risorse nella portale di Azure.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Elencare i gruppi di server di database di Azure per PostgreSQL creati nell'installazione di Arc

Per visualizzare i gruppi di server con iperscalabilità PostgreSQL in Azure Arc, usare il comando seguente:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Ottenere gli endpoint per la connessione ai gruppi di server del database di Azure per PostgreSQL

Per visualizzare gli endpoint per un'istanza di PostgreSQL, eseguire il comando seguente:

```console
azdata arc postgres endpoint list -n <server group name>
```
Esempio:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

È possibile usare l'endpoint dell'istanza di PostgreSQL per connettersi al gruppo di server con iperscalabilità PostgreSQL dallo strumento preferito:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) PSQL, pgAdmin e così via.

Se si usa una macchina virtuale di Azure da testare, seguire le istruzioni seguenti:

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Nota speciale sulle distribuzioni delle macchine virtuali di Azure

Quando si usa una macchina virtuale di Azure, l'indirizzo IP dell'endpoint non visualizzerà l'indirizzo IP _pubblico_ . Per individuare l'indirizzo IP pubblico, usare il comando seguente:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Sarà quindi possibile combinare l'indirizzo IP pubblico con la porta per effettuare la connessione.

Potrebbe anche essere necessario esporre la porta del gruppo di server di iperscala PostgreSQL tramite il gateway di sicurezza di rete (NSG). Per consentire il traffico tramite (NSG), è necessario aggiungere una regola che è possibile eseguire usando il comando seguente:

Per impostare una regola, è necessario conoscerne il nome NSG. Per determinare il NSG, usare il comando seguente:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Una volta ottenuto il nome del NSG, è possibile aggiungere una regola del firewall usando il comando seguente. I valori di esempio qui creano una regola NSG per la porta 30655 e consentono la connessione da **qualsiasi** indirizzo IP di origine.  Non si tratta di una procedura consigliata per la sicurezza.  È possibile bloccare meglio gli elementi specificando un valore-Source-prefix-prefissi specifico per l'indirizzo IP del client o un intervallo di indirizzi IP che copre gli indirizzi IP del team o dell'organizzazione.

Sostituire il valore del parametro--Destination-Port-Ranges sotto con il numero di porta ottenuto dal comando ' azdata Arc Postgres server list ' precedente.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Stabilire la connessione con Azure Data Studio

Aprire Azure Data Studio e connettersi all'istanza di con l'indirizzo IP e il numero di porta dell'endpoint esterno e la password specificata al momento della creazione dell'istanza.  Se PostgreSQL non è disponibile nell'elenco a discesa *tipo di connessione* , è possibile installare l'estensione PostgreSQL cercando PostgreSQL nella scheda estensioni.

> [!NOTE]
> Per immettere il numero di porta, è necessario fare clic sul pulsante [Avanzate] nel pannello connessione.

Tenere presente che se si usa una macchina virtuale di Azure, sarà necessario l'indirizzo IP _pubblico_ accessibile tramite il comando seguente:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Connettersi con PSQL

Per accedere al gruppo di server con iperscalabilità PostgreSQL, passare l'endpoint esterno del gruppo di server di iperscala PostgreSQL recuperato in precedenza:

È ora possibile connettersi a PSQL:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Passaggi successivi

- Leggi i concetti e le guide alle procedure per la scalabilità di database di Azure per PostgreSQL per distribuire i dati tra più nodi con scalabilità multipla PostgreSQL e sfruttare tutte le potenzialità dell'iperscalabilità di database di Azure per PostgreSQL. :
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
- [Espansione di attestazioni di volume permanenti](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modello di risorsa Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)