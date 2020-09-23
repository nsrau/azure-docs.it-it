---
title: Configurazione dell'archiviazione
description: Illustra le opzioni di configurazione dell'archiviazione di Azure Arc Enabled Data Services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 782a046b92c9d6cf755bfea0551d7f8153faa859
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939192"
---
# <a name="storage-configuration"></a>Configurazione dell'archiviazione

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Concetti relativi all'archiviazione Kubernetes

Kubernetes fornisce un livello di astrazione dell'infrastruttura per lo stack Tech di virtualizzazione sottostante (facoltativo) e l'hardware. Il modo in cui Kubernetes estrae l'archiviazione avviene tramite **[le classi di archiviazione](https://kubernetes.io/docs/concepts/storage/storage-classes/)**. Al momento del provisioning di un pod, è possibile specificare una classe di archiviazione da usare per ogni volume. Nel momento in cui viene eseguito il provisioning del Pod, viene chiamata **[la classe di](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** archiviazione per eseguire il provisioning della risorsa di archiviazione e quindi viene creato un **[volume permanente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** in tale spazio di archiviazione di cui è stato effettuato il provisioning e quindi il Pod viene montato nel volume permanente da un' **[attestazione di volume permanente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)**.

Kubernetes fornisce un modo per i provider dell'infrastruttura di archiviazione per collegare i driver (detti anche "addons") che estendono Kubernetes. Gli addons di archiviazione devono essere conformi allo **[standard dell'interfaccia di archiviazione del contenitore](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**. Sono disponibili dozzine di addons che è possibile trovare in questo elenco non definitivo **[di driver CSI](https://kubernetes-csi.github.io/docs/drivers.html)**. Il driver CSI da usare dipende da fattori quali l'esecuzione in un servizio Kubernetes gestito e ospitato nel cloud o il provider OEM usato per l'hardware.

È possibile visualizzare le classi di archiviazione configurate nel cluster Kubernetes eseguendo questo comando:

``` terminal
kubectl get storageclass
```

Output di esempio di un cluster di Azure Kubernetes Service (AKS):

``` terminal
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

È possibile ottenere i dettagli relativi a una classe di archiviazione eseguendo questo comando:

``` terminal
kubectl describe storageclass\<storage class name>
```

Esempio:

``` terminal
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

È possibile visualizzare i volumi persistenti di cui è stato effettuato il provisioning e le attestazioni del volume permanente eseguendo i comandi seguenti:

``` terminal
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Esempio di visualizzazione di volumi permanenti:

``` terminal

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Esempio di visualizzazione di attestazioni di volume permanenti:

``` terminal

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Fattori da considerare quando si sceglie la configurazione di archiviazione

La scelta della classe di archiviazione corretta è molto importante per la resilienza dei dati e le prestazioni. La scelta della classe di archiviazione errata può mettere i dati a rischio di una perdita di dati totale in caso di errore hardware o potrebbe produrre prestazioni meno ottimali.

Esistono in genere due tipi di archiviazione:

- **Archiviazione locale** -archiviazione di cui viene effettuato il provisioning in dischi rigidi locali in un determinato nodo. Questo tipo di archiviazione può essere ideale in termini di prestazioni, ma richiede una progettazione specifica della ridondanza dei dati tramite la replica dei dati tra più nodi.
- Spazio di archiviazione **condiviso remoto** di cui è stato effettuato il provisioning in un dispositivo di archiviazione remoto, ad esempio una rete San, un NAS o un servizio di archiviazione cloud come EBS o file di Azure. Questo tipo di archiviazione in genere fornisce la ridondanza dei dati automaticamente, ma in genere non è altrettanto veloce quanto può essere l'archiviazione locale.

> [!NOTE]
> Per il momento, se si usa NFS, è necessario impostare allowRunAsRoot su true nel file del profilo di distribuzione prima di distribuire il controller di dati di Azure Arc.

### <a name="data-controller-storage-configuration"></a>Configurazione dell'archiviazione del controller dei dati

Alcuni servizi di Azure Arc per i servizi dati variano in base alla configurazione per l'uso dell'archiviazione remota e condivisa perché i servizi non hanno la possibilità di replicare i dati. Questi servizi sono disponibili nella raccolta di Pod del controller di dati:

|**Servizio**|**Attestazioni volume permanenti**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**Istanza del controller SQL**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Servizio API controller**|`<namespace>/data-controller`|

Al momento del provisioning del controller dei dati, la classe di archiviazione da usare per ognuno di questi volumi permanenti viene specificata passando la classe--Storage-Class | -SC parametro del `azdata arc dc create` comando oppure impostando le classi di archiviazione nel file di modello di distribuzione control.jsin uso.

I modelli di distribuzione forniti per impostazione predefinita hanno una classe di archiviazione predefinita specificata, appropriata per l'ambiente di destinazione, ma è possibile eseguirne l'override in fase di distribuzione. Vedere i passaggi dettagliati per modificare [il profilo di distribuzione](create-data-controller.md) per modificare la configurazione della classe di archiviazione per i pod del controller dati in fase di distribuzione.

Se si imposta la classe di archiviazione usando la classe--Storage-Class | -SC parametro la classe di archiviazione verrà usata per le classi di archiviazione di dati e di log. Se si impostano le classi di archiviazione nel file del modello di distribuzione, è possibile specificare classi di archiviazione diverse per i log e i dati.

Fattori importanti da considerare quando si sceglie una classe di archiviazione per i pod del controller dati:

- È **necessario** usare una classe di archiviazione condivisa remota per garantire la durabilità dei dati e, in questo modo, se un pod o un nodo è in grado di riconnettersi al volume permanente.
- I dati scritti nell'istanza del controller SQL, nel database di metriche e nei log del database sono in genere un volume abbastanza basso e non sono sensibili alla latenza, pertanto l'archiviazione delle prestazioni estremamente veloce non è fondamentale. Se sono presenti utenti che usano spesso le interfacce Grafana e Kibana e si dispone di un numero elevato di istanze di database, gli utenti potrebbero trarre vantaggio dall'archiviazione più veloce.
- La capacità di archiviazione necessaria è la variabile con il numero di istanze di database distribuite, perché i log e le metriche vengono raccolti per ogni istanza del database. I dati vengono conservati nei log e nel database di metrica per 2 settimane prima che vengano eliminati. TODO: quanto spazio di archiviazione è necessario per ogni istanza di database?
- La modifica della classe di archiviazione post-distribuzione è molto difficile, non documentata e non supportata. Assicurarsi di scegliere correttamente la classe di archiviazione in fase di distribuzione.

> **Nota:** Se non viene specificata alcuna classe di archiviazione, verrà usata la classe di archiviazione predefinita. Può essere presente una sola classe di archiviazione predefinita per ogni cluster Kubernetes. È possibile [modificare la classe di archiviazione predefinita](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Configurazione dell'archiviazione dell'istanza di database

Ogni istanza di database dispone di dati, log e volumi permanenti di backup. È possibile specificare le classi di archiviazione per questi volumi permanenti in fase di distribuzione. Se non viene specificata alcuna classe di archiviazione, verrà usata la classe di archiviazione predefinita.

Quando si crea un'istanza `azdata arc sql mi create` usando `azdata arc postgres server create` i comandi o sono disponibili due parametri che possono essere usati per impostare le classi di archiviazione:

> **Nota:** Alcuni di questi parametri sono in fase di sviluppo e diventeranno disponibili in `azdata arc sql mi create` e `azdata arc postgres server create` nelle prossime versioni.

|Nome parametro, nome breve|Utilizzato per|
|---|---|
|`--storage-class-data`, `-scd`|Consente di specificare la classe di archiviazione per tutti i file di dati, inclusi i file di log delle transazioni|
|`--storage-class-logs`, `-scl`|Consente di specificare la classe di archiviazione per tutti i file di log|
|`--storage-class-data-logs`, `-scdl`|Consente di specificare la classe di archiviazione per i file di log delle transazioni del database. **Nota: non ancora disponibile.**|
|`--storage-class-backups`, `-scb`|Consente di specificare la classe di archiviazione per tutti i file di backup. **Nota: non ancora disponibile.**|

La tabella seguente elenca i percorsi all'interno del contenitore di Istanza gestita SQL di Azure di cui è stato eseguito il mapping al volume permanente per dati e log:

|Nome parametro, nome breve|Percorso nel contenitore MSSQL-miaa|Descrizione|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Contiene le directory per l'installazione di MSSQL e altri processi di sistema. La directory MSSQL contiene dati predefiniti (inclusi i log delle transazioni), log degli errori & directory di backup|
|`--storage-class-logs`, `-scl`|/var/log|Contiene le directory che archiviano l'output della console (stderr, stdout), altre informazioni di registrazione dei processi all'interno del contenitore|

La tabella seguente elenca i percorsi all'interno del contenitore di istanze di PostgreSQL mappati al volume permanente per i dati e i log:

|Nome parametro, nome breve|Percorso nel contenitore Postgres|Descrizione|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Contiene i dati e le directory di log per l'installazione Postgres|
|`--storage-class-logs`, `-scl`|/var/log|Contiene le directory che archiviano l'output della console (stderr, stdout), altre informazioni di registrazione dei processi all'interno del contenitore|

Ogni istanza del database avrà un volume permanente separato per i file di dati, i log e i backup. Ciò significa che la separazione dell'i/O per ognuno di questi tipi di file è soggetta alla modalità di provisioning dell'archiviazione da parte del provisioner del volume. Ogni istanza di database dispone di attestazioni del volume permanenti e di volumi permanenti.

Se sono presenti più database in un'istanza di database specifica, tutti i database utilizzeranno la stessa attestazione del volume persistente, il volume persistente e la classe di archiviazione. Tutti i backup: i backup del log differenziali e i backup completi utilizzeranno la stessa attestazione di volume permanente e il volume permanente. Di seguito sono riportate le attestazioni del volume permanente per i pod dell'istanza del database:

|**Istanza**|**Attestazioni volume permanenti**|
|---|---|
|**Istanza gestita di database SQL di Azure**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Istanza di database di Azure per PostgreSQL**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Iperscalabilità PostgreSQL di Azure**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Intervalli ordinali compresi tra 0 e W dove w è il numero di ruoli di lavoro)*|

Fattori importanti da considerare quando si sceglie una classe di archiviazione per i pod dell'istanza del database:

- Le istanze di database possono essere distribuite in un modello a POD singolo o in un modello a più POD. Un esempio di modello a POD singolo è costituito da un'istanza dello sviluppatore di un'istanza gestita di SQL di Azure o da un'istanza gestita di Azure SQL di livello generale. Un esempio di modello a più POD è un'istanza gestita di Azure SQL per il piano tariffario a disponibilità elevata di livello economico. Nota: i piani tariffari sono in fase di sviluppo e non sono ancora disponibili per i clienti.  Le istanze di database distribuite con il modello a POD singolo **devono** usare una classe di archiviazione condivisa remota per garantire la durabilità dei dati e, in questo caso, se un pod o un nodo è in grado di riconnettersi al volume permanente. Un'istanza gestita di SQL di Azure a disponibilità elevata usa invece Always On gruppi di disponibilità per replicare i dati da un'istanza a un'altra in modo sincrono o asincrono. In particolare, nel caso in cui i dati vengono replicati in modo sincrono, sono sempre presenti più copie dei dati, in genere 3 copie. Per questo motivo, è possibile usare l'archiviazione locale o le classi di archiviazione condivise remote per i file di dati e di log. Se si usa l'archiviazione locale, i dati vengono comunque conservati anche nel caso di un pod, un nodo o un hardware di archiviazione non riuscito. Data questa flessibilità, è possibile scegliere di usare l'archiviazione locale per ottenere prestazioni migliori.
- Le prestazioni del database sono in gran parte una funzione della velocità effettiva di I/O di un determinato dispositivo di archiviazione. Se il database è un numero elevato di letture o scritture pesanti, è necessario scegliere una classe di archiviazione con hardware sottostante progettato per quel tipo di carico di lavoro. Se, ad esempio, il database viene usato principalmente per le Scritture, è possibile scegliere archiviazione locale con RAID 0. Se il database viene usato principalmente per le letture di una piccola quantità di "dati attivi", ma è presente un volume di archiviazione globale di grandi dimensioni, è possibile scegliere un dispositivo SAN in grado di usare l'archiviazione a livelli. La scelta della classe di archiviazione corretta non è molto diversa dalla scelta del tipo di archiviazione da usare per qualsiasi database.
- Se si usa un provisioner del volume di archiviazione locale, è necessario assicurarsi che i volumi locali di cui è stato effettuato il provisioning per dati, log e backup siano ognuno in dispositivi di archiviazione sottostanti diversi per evitare conflitti nell'I/O del disco. Il sistema operativo deve trovarsi anche in un volume montato su uno o più dischi separati. Questo è essenzialmente lo stesso materiale sussidiario che verrebbe seguito per un'istanza di database su hardware fisico.
- Poiché tutti i database in un'istanza specifica condividono un'attestazione di volume permanente e un volume permanente, assicurarsi di non rilocare le istanze di database occupate nella stessa istanza del database. Se possibile, separare i database occupati nelle rispettive istanze del database per evitare la contesa di I/O. Usare inoltre l'etichetta del nodo destinata alle istanze di database in modo che vengano distribuite in nodi separati, in modo da distribuire il traffico di I/O complessivo tra più nodi. Se si usa la virtualizzazione, assicurarsi di valutare la possibilità di distribuire il traffico di I/O non solo a livello di nodo, ma anche l'attività di I/O combinata eseguita da tutte le VM del nodo in un host fisico specifico.

## <a name="estimating-storage-requirements"></a>Stima dei requisiti di archiviazione
Ogni pod che contiene dati con stato USA due volumi permanenti in questa versione: un volume permanente per i dati e un altro volume permanente per i log. La tabella seguente elenca il numero di volumi persistenti necessari per un singolo controller di dati, istanza gestita di SQL di Azure, istanza di database di Azure per PostgreSQL e istanza di scalabilità di Azure PostgreSQL:

|Tipo di risorsa|Numero di Pod con stato|Numero necessario di volumi persistenti|
|---|---|---|
|Data Controller|4 ( `control` ,,, `controldb` `logsdb` `metricsdb` )|4 * 2 = 8|
|Istanza gestita di SQL di Azure|1|2|
|Istanza di database di Azure per PostgreSQL|1| 2|
|Iperscalabilità PostgreSQL di Azure|1 + w (W = numero di processi di lavoro)|2 * (1 + W)|

La tabella seguente mostra il numero totale di volumi permanenti richiesti per una distribuzione di esempio:

|Tipo di risorsa|Numero di istanze|Numero necessario di volumi persistenti|
|---|---|---|
|Data Controller|1|4 * 2 = 8|
|Istanza gestita di SQL di Azure|5|5 * 2 = 10|
|Istanza di database di Azure per PostgreSQL|5| 5 * 2 = 10|
|Iperscalabilità PostgreSQL di Azure|2 (numero di processi di lavoro = 4 per istanza)|2 * 2 * (1 + 4) = 20|
|***Numero totale di volumi persistenti***||8 + 10 + 10 + 20 = 48|

Questo calcolo può essere usato per pianificare lo spazio di archiviazione per il cluster Kubernetes in base all'ambiente o al provisioning di archiviazione. Se, ad esempio, viene usato il provisioning dell'archiviazione locale per un cluster Kubernetes con 5 nodi, per la distribuzione di esempio sopra ogni nodo è necessaria almeno l'archiviazione per 10 volumi permanenti. Analogamente, quando si esegue il provisioning di un cluster Azure Kubernetes Service (AKS) con 5 nodi che scelgono una dimensione di macchina virtuale appropriata per il pool di nodi, in modo da poter collegare 10 dischi dati è fondamentale. Altre informazioni su come ridimensionare i nodi per le esigenze di archiviazione per i nodi AKS sono disponibili [qui](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs).

## <a name="choosing-the-right-storage-class"></a>Scelta della classe di archiviazione corretta

### <a name="on-premises-and-edge-sites"></a>Siti locali e perimetrali

Microsoft e i suoi partner OEM, OS e Kubernetes stanno lavorando a un programma di certificazione per Azure Arc Data Services. Questo programma fornirà ai clienti i risultati dei test confrontabili da un toolkit di test di certificazione. I test valuteranno la compatibilità delle funzionalità, i risultati dei test di stress, le prestazioni e la scalabilità. Ognuno di questi risultati del test indicherà il sistema operativo usato, la distribuzione Kubernetes usata, il HW usato, il componente aggiuntivo CSI usato e le classi di archiviazione usate. Ciò consentirà ai clienti di scegliere la migliore classe di archiviazione, sistema operativo, distribuzione Kubernetes e HW per le proprie esigenze. Altre informazioni su questo programma e sui risultati dei test iniziali verranno fornite più vicino alla disponibilità a livello generale di Azure Arc Data Services.

#### <a name="public-cloud-managed-kubernetes-services"></a>Cloud pubblico, servizi Kubernetes gestiti

Per i servizi Kubernetes gestiti basati su cloud pubblici, è possibile effettuare le seguenti raccomandazioni:

|Servizio cloud pubblico|Recommendation|
|---|---|
|**Servizio Azure Kubernetes**|Azure Kubernetes Service (AKS) include due tipi di File di Azure di archiviazione e dischi di Azure. Ogni tipo di archiviazione ha due livelli di prezzo/prestazioni: standard (HDD) e Premium (SSD). Quindi, le quattro classi di archiviazione disponibili in AKS sono `azurefile` (file di Azure livello standard), `azurefile-premium` (file di Azure livello Premium), `default` (livello standard di dischi di Azure) e `managed-premium` (livello Premium di dischi di Azure). La classe di archiviazione predefinita è `default` (livello standard di Azure Disks). Esistono differenze sostanziali nei **[prezzi](https://azure.microsoft.com/en-us/pricing/details/storage/)** tra i tipi e i livelli che devono essere presi in considerazione per la decisione dell'utente. Per i carichi di lavoro di produzione con requisiti di prestazioni elevate, è consigliabile usare `managed-premium` per tutte le classi di archiviazione. Per i carichi di lavoro di sviluppo/test, il concetto di prova e così via, in cui il costo è una considerazione, `azurefile` è l'opzione meno costosa. Tutte e quattro le opzioni possono essere usate per situazioni in cui è richiesta l'archiviazione condivisa e remota, perché si tratta di tutti i dispositivi di archiviazione collegati alla rete in Azure. Scopri di più sull' [archiviazione AKS](../../aks/concepts-storage.md).|
|**AWS Elastic Kubernetes Service (EKS)**| Il servizio Elastic Kubernetes di Amazon ha una classe di archiviazione primaria basata sul [driver di archiviazione EBS CSI](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). Questa operazione è consigliata per i carichi di lavoro di produzione. È disponibile un nuovo [driver di archiviazione EFS CSI](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) , che può essere aggiunto a un cluster EKS, ma è attualmente in fase beta e soggetto a modifiche. Anche se AWS afferma che questo driver di archiviazione è supportato per la produzione, non è consigliabile utilizzarlo perché è ancora in versione beta e soggetto a modifiche. La classe di archiviazione EBS è l'impostazione predefinita e viene chiamato `gp2` . Scopri di più sull' [archiviazione EKS](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html).|
|**Google Kubernetes Engine (GKE)**|Il motore di Google Kubernetes (GKE) dispone di una sola classe di archiviazione denominata `standard` usata per i [dischi persistenti di GCE](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk). È anche l'unico valore predefinito. Sebbene sia disponibile un [provisioning statico del volume locale](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) per GKE che è possibile usare con le unità SSD collegate direttamente, non è consigliabile usarlo perché non è gestito né supportato da Google. Scopri di più sull' [archiviazione GKE](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes).
