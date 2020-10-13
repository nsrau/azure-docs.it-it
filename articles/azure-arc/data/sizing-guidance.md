---
title: Indicazioni sul dimensionamento
description: Pianificare le dimensioni di una distribuzione di Azure Arc Enabled Data Services.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3bd54d8a23aca7e493cd3c0ddb7f057a6e1f5362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761482"
---
# <a name="sizing-guidance"></a>Linee guida per il ridimensionamento

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Panoramica delle linee guida per il ridimensionamento

Quando si pianifica la distribuzione di Azure Arc Data Services, è necessario pianificare la quantità corretta di risorse di calcolo, memoria e archiviazione che saranno necessarie per eseguire il controller di dati di Azure Arc e per il numero di gruppi di server di istanza gestita di SQL e PostgreSQL che verrà distribuito.  Poiché Azure Arc Enabled Data Services viene distribuito in Kubernetes, si ha la flessibilità di aggiungere ulteriore capacità al cluster Kubernetes nel tempo aggiungendo nodi di calcolo o risorse di archiviazione aggiuntivi.  In questa guida vengono fornite informazioni sui requisiti minimi e vengono fornite indicazioni sulle dimensioni consigliate per alcuni requisiti comuni.

## <a name="general-sizing-requirements"></a>Requisiti di dimensionamento generali

> [!NOTE]
> Se non si ha familiarità con i concetti illustrati in questo articolo, è possibile leggere altre informazioni su [Kubernetes Resource governance](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) e [Kubernetes size Notation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

I numeri di core devono essere un valore intero maggiore o uguale a uno.

Quando si usa azdata per la distribuzione, è necessario specificare i valori di memoria in una potenza di due numeri, ovvero usando i suffissi: Ki, mi o gi.

Se specificato, i valori limite devono essere sempre maggiori di quelli del valore della richiesta.

I valori limite per i core sono la metrica fatturabile nei gruppi di server di istanza gestita di SQL e PostgreSQL.

## <a name="minimum-deployment-requirements"></a>Requisiti di distribuzione minimi

Una distribuzione di Azure Arc abilitata per la dimensione minima può essere considerata come il controller dati di Azure Arc più un'istanza gestita di SQL e un gruppo di server con scalabilità PostgreSQL con due nodi di lavoro.  Per questa configurazione sono necessari almeno 16 GB di RAM e 4 core di capacità _disponibile_ nel cluster Kubernetes.  È necessario assicurarsi di avere una dimensione minima del nodo Kubernetes di 8 GB di RAM e 4 core e una capacità totale di 16 GB di RAM disponibile in tutti i nodi Kubernetes.  Ad esempio, è possibile avere 1 nodo a 32 GB di RAM e 4 core oppure avere 2 nodi con 16 GB di RAM e 4 core ciascuno.

Per informazioni dettagliate sul dimensionamento dell'archiviazione, vedere l'articolo relativo alla [configurazione dell'archiviazione](storage-configuration.md) .

## <a name="data-controller-sizing-details"></a>Dettagli sul dimensionamento del controller dei dati

Il controller dati è una raccolta di Pod distribuiti nel cluster Kubernetes per fornire un'API, il servizio controller, il programma di avvio automatico e i dashboard e i database di monitoraggio.  Questa tabella descrive i valori predefiniti per le richieste di memoria e CPU e i limiti.

|Nome pod|Richiesta CPU|Richiesta di memoria|Limite CPU|Limite memoria|Note|
|---|---|---|---|---|---|
|**Bootstrapper**|100m|100Mi|200m|200Mi||
|**control**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10m|100Mi|100m|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100m|200Mi|200m|300Mi|Metricsdc è un daemonset creato in ogni nodo Kubernetes del cluster.  I numeri nella tabella sono disponibili _per ogni nodo_. Se si imposta allowNodeMetricsCollection = false nel file del profilo di distribuzione prima di creare il controller dati, il daemonset metricsdc non verrà creato.|
|**metricsui**|20 milioni|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

È possibile eseguire l'override delle impostazioni predefinite per il controldb e i pod di controllo nel file del profilo di distribuzione o nel file YAML del controller.  Esempio:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Per informazioni dettagliate sul dimensionamento dell'archiviazione, vedere l'articolo relativo alla [configurazione dell'archiviazione](storage-configuration.md) .

## <a name="sql-managed-instance-sizing-details"></a>Dettagli sul dimensionamento dell'istanza gestita di SQL

Ogni istanza gestita di SQL deve avere le seguenti richieste di risorse minime:
- Memoria: 2Gi
- Core: 1

Ogni pod di istanza gestita di SQL creato ha tre contenitori:
|Nome contenitore|Richiesta CPU|Richiesta di memoria|Limite CPU|Limite memoria|Note|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|Non specificato|Non specificato|Le richieste di risorse del contenitore fluentbit sono _in aggiunta alle_ richieste specificate per l'istanza gestita di SQL.||
|Arc-sqlmi|Utente specificato o non specificato.|Utente specificato o non specificato.|Utente specificato o non specificato.|Utente specificato o non specificato.||
|collectd|Non specificato|Non specificato|Non specificato|Non specificato||

La dimensione predefinita del volume per tutti i volumi persistenti è 5Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>Dettagli sul dimensionamento del gruppo di server di scalabilità PostgreSQL

Ogni nodo del gruppo di server di iperscala PostgreSQL deve avere le seguenti richieste di risorse minime:
- Memoria: 256Mi
- Core: 1

Ogni coordinatore del gruppo di server di scalabilità PostgreSQL o il pod di lavoro creato ha tre contenitori:
|Nome contenitore|Richiesta CPU|Richiesta di memoria|Limite CPU|Limite memoria|Note|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|Non specificato|Non specificato|Le richieste di risorse del contenitore fluentbit sono _in aggiunta alle_ richieste specificate per i nodi del gruppo di server di iperscala PostgreSQL.|
|postgres|Utente specificato o non specificato.|Utente specificato o 256Mi (impostazione predefinita).|Utente specificato o non specificato.|Utente specificato o non specificato.||
|Telegraf|Non specificato|Non specificato|Non specificato|Non specificato||

## <a name="cumulative-sizing"></a>Dimensionamento cumulativo

La dimensione complessiva di un ambiente necessario per i servizi dati abilitati per Azure Arc è principalmente una funzione del numero e delle dimensioni delle istanze di database che verranno create.  Le dimensioni complessive possono essere difficili da prevedere in anticipo per la crescita e la riduzione del numero di istanze e la quantità di risorse necessarie per ogni istanza del database cambierà.

Le dimensioni della linea di base per un dato ambiente di Azure Arc Enabled Data Services sono le dimensioni del controller di dati che richiedono 4 core e 16 GB di RAM.  Da qui è possibile aggiungere in primo piano il totale cumulativo di core e memoria necessari per le istanze di database.  Per istanza SQL gestita, il numero di Pod è uguale al numero di istanze gestite da SQL create.  Per i gruppi di server con iperscalabilità PostgreSQL il numero di Pod è equivalente al numero di nodi del ruolo di lavoro più uno per il nodo coordinatore.  Se, ad esempio, si dispone di un gruppo di server PostgreSQL con 3 nodi di lavoro, il numero totale di pod sarà 4.

Oltre ai core e alla memoria richiesti per ogni istanza del database, è necessario aggiungere 250 milioni di core e 250Mi di RAM per i contenitori degli agenti.

Di seguito è riportato un esempio di calcolo di ridimensionamento.

Requisiti:

- **"SQL1"**: 1 istanza gestita di SQL con 16 GB di RAM, 4 core
- **"SQL2"**: 1 istanza gestita di SQL con 256 GB di RAM, 16 core
- **"Postgres1"**: 1 gruppo di server su scala PostgreSQL con 4 dipendenti a 12 GB di RAM, 4 core

Ridimensionamento di calcoli:

- Le dimensioni di "SQL1" sono: 1 pod * ([16 gi RAM, 4 core] + [RAM 250Mi, 250 core]) per gli agenti per pod = 16,25 gi RAM, 4,25 core.
- Le dimensioni di "SQL2" sono: 1 pod * ([256 gi RAM, 16 core] + + [250Mi RAM, 250 core]) per gli agenti per pod = 256,25 gi RAM, 16,25 core.
- Le dimensioni totali di SQL 1 e SQL 2 sono: (16,25 GB + 256,25 gi) = 272,5 GB di RAM, (4,25 Core + 16,25 Core) = 20,5 Cores.

- Le dimensioni di "Postgres1" sono: (4 pod di lavoro + 1 pod coordinatore) * ([12 GB di RAM, 4 core] + [RAM 250Mi, 250 core]) per gli agenti per pod = 61,25 GB di RAM, 21,25 core.

- La capacità totale necessaria per le istanze di database è: 272,5 GB di RAM, 20,5 core per SQL + 61,25 GB di RAM, 21,25 core per PostgreSQL iperscalabilità = 333,75 GB di RAM, 42,5 Core.

- La capacità totale necessaria per le istanze di database più il controller dati è: 333,75 GB di RAM, 42,5 core per le istanze di database + 16 GB di RAM, 4 core per il controller dati = 349,75 GB di RAM, 46,5 Core.

Per informazioni dettagliate sul dimensionamento dell'archiviazione, vedere l'articolo relativo alla [configurazione dell'archiviazione](storage-configuration.md) .

## <a name="other-considerations"></a>Altre considerazioni

Tenere presente che una determinata richiesta di dimensioni dell'istanza del database per core o RAM non può superare la capacità disponibile dei nodi Kubernetes nel cluster.  Se, ad esempio, il nodo Kubernetes più grande presente nel cluster Kubernetes è 256 GB di RAM e 24 core, non sarà possibile creare un'istanza di database con una richiesta di 512 GB di RAM e 48 core.  

È consigliabile mantenere almeno il 25% della capacità disponibile tra i nodi Kubernetes per consentire a Kubernetes di pianificare in modo efficiente i pod da creare e di consentire la scalabilità elastica e la crescita a lungo termine su richiesta.  

Nei calcoli di ridimensionamento, non dimenticare di aggiungere i requisiti delle risorse dei pod di sistema Kubernetes e di tutti gli altri carichi di lavoro che potrebbero condividere la capacità con Azure Arc Enabled Data Services nello stesso cluster Kubernetes.

Per mantenere la disponibilità elevata durante la manutenzione pianificata e la continuità di emergenza, è consigliabile pianificare almeno uno dei nodi Kubernetes nel cluster in modo che non sia disponibile in un dato momento.  Kubernetes tenterà di ripianificare i pod in esecuzione in un determinato nodo che è stato disattivato per la manutenzione o a causa di un errore.  Se non è disponibile alcuna capacità nei nodi rimanenti, i Pod non verranno ripianificati per la creazione fino a quando non sarà disponibile nuovamente la capacità.  Prestare maggiore attenzione con le istanze di database di grandi dimensioni.  Se, ad esempio, è presente un solo nodo Kubernetes sufficientemente grande per soddisfare i requisiti di risorse di un'istanza di database di grandi dimensioni e il nodo ha esito negativo, Kubernetes non sarà in grado di pianificare il Pod dell'istanza del database su un altro nodo Kubernetes.

Tenere presente i [limiti massimi per le dimensioni del cluster Kubernetes](https://kubernetes.io/docs/setup/best-practices/cluster-large/) .

L'amministratore di Kubernetes potrebbe avere impostato le [quote delle risorse](https://kubernetes.io/docs/concepts/policy/resource-quotas/) nello spazio dei nomi/progetto.  Tenere presenti queste considerazioni quando si pianificano le dimensioni delle istanze di database.
