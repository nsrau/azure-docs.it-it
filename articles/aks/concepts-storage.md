---
title: Concetti - Archiviazione nel servizio Azure Kubernetes
description: Informazioni sull'archiviazione nel servizio Azure Kubernetes, inclusi volumi, volumi permanenti, classi di archiviazione e attestazioni
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: cce38eb12d803c0640d9ee774dbc6c98ab5db219
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466818"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes

Le applicazioni eseguite nel servizio Azure Kubernetes potrebbero richiedere l'archiviazione e il recupero dei dati. Per alcuni carichi di lavoro delle applicazioni, l'archiviazione dei dati può usare risorse di archiviazione locali e veloci sul nodo, che non sono più necessarie quando vengono eliminati i pod. Per altri carichi di lavoro potrebbe essere richiesta l'archiviazione permanente su volumi di dati più regolari all'interno della piattaforma di Azure. Più pod potrebbero dover condividere gli stessi volumi di dati oppure potrebbe essere necessario ricollegare i volumi di dati se il pod viene ripianificato su un nodo diverso. Infine, potrebbe essere necessario inserire i dati sensibili o le informazioni di configurazione delle applicazioni nei pod.

![Opzioni di archiviazione per le applicazioni in un cluster del servizio Azure Kubernetes](media/concepts-storage/aks-storage-options.png)

Questo articolo introduce i concetti di base per rendere disponibili risorse di archiviazione per le applicazioni nel servizio Kubernetes di Azure:

- [Volumi](#volumes)
- [Volumi permanenti](#persistent-volumes)
- [Classi di archiviazione](#storage-classes)
- [Attestazioni di volume permanente](#persistent-volume-claims)

## <a name="volumes"></a>Volumi

Le applicazioni devono spesso essere in grado di archiviare e recuperare dati. Dato che Kubernetes considera in genere i singoli pod come risorse temporanee eliminabili, sono disponibili approcci diversi utilizzabili dalle applicazioni per usare e rendere permanenti i dati in base alle esigenze. Un *volume* rappresenta un modo per archiviare, recuperare e salvare i dati tra i pod e per l'intero ciclo di vita dell'applicazione.

Vengono creati volumi tradizionali per archiviare e recuperare i dati come risorse di Kubernetes supportate da Archiviazione di Azure. È possibile creare manualmente questi volumi di dati da assegnare ai pod direttamente o lasciare che vengano creati automaticamente da Kubernetes. Questi volumi di dati possono usare Dischi di Azure o File di Azure:

- È possibile usare *Dischi di Azure* per creare una risorsa *DataDisk* Kubernetes. I dischi possono usare l'archiviazione Premium di Azure, supportata da unità SSD a prestazioni elevate, o l'archiviazione Standard di Azure, supportata da unità HDD regolari. Per la maggior parte dei carichi di lavoro di produzione e di sviluppo, usare l'archiviazione Premium. I dischi di Azure sono montati come *ReadWriteOnce*, pertanto sono disponibili solo per un singolo nodo. Per i volumi di archiviazione accessibili contemporaneamente da più nodi, usare File di Azure.
- È possibile usare *File di Azure* per montare una condivisione SMB 3.0 supportata da un account di archiviazione di Azure nei pod. I file consentono di condividere dati tra più nodi e pod. Attualmente, per File di Azure è possibile usare solo l'archiviazione Standard di Azure supportata da unità HDD regolari.

In Kubernetes, i volumi possono rappresentare più di un semplice disco tradizionale in cui possono essere archiviate e recuperate le informazioni. I volumi di Kubernetes possono anche essere usati come modo per inserire dati in un pod per l'uso da parte dei contenitori. I tipi di volumi aggiuntivi comuni in Kubernetes includono:

- *emptyDir* - Questo volume viene comunemente usato come spazio temporaneo per un pod. Tutti i contenitori all'interno di un pod possono accedere ai dati nel volume. I dati scritti in questo tipo di volume rimangono disponibili solo per il ciclo di vita del pod. Quando viene eliminato il pod, viene eliminato anche il volume. Questo volume usa in genere l'archiviazione su disco del nodo locale sottostante, ma può esistere anche solo nella memoria del nodo.
- *secret* - Questo volume viene usato per inserire i dati sensibili nei pod, ad esempio le password. Si crea prima di tutto un segreto usando l'API di Kubernetes. Quando si definisce il pod o la distribuzione, è possibile richiedere un segreto specifico. I segreti vengono forniti solo ai nodi che hanno un pod pianificato che li richiede, perché i segreti vengono archiviati in *tmpfs* e non scritti su disco. Quando viene eliminato l'ultimo pod in un nodo che richiede un segreto, il segreto viene eliminato da tmpfs del nodo. I segreti vengono archiviati all'interno di un determinato spazio dei nomi e sono accessibili solo dai pod all'interno dello stesso spazio dei nomi.
- *configMap* - Questo tipo di volume viene usato per inserire le proprietà di coppie chiave-valore nei pod, ad esempio le informazioni di configurazione dell'applicazione. Invece di definire le informazioni di configurazione dell'applicazione all'interno di un'immagine del contenitore, è possibile definirle come una risorsa Kubernetes che può essere facilmente aggiornata e applicata alle nuove istanze dei pod durante la distribuzione. Come per l'uso di un segreto, è prima di tutto necessario creare un volume di tipo ConfigMap usando l'API di Kubernetes. Questo volume ConfigMap può quindi essere richiesto quando si definisce un pod o distribuzione. I volumi ConfigMap vengono archiviati all'interno di un determinato spazio dei nomi e sono accessibili solo da pod all'interno dello stesso spazio dei nomi.

## <a name="persistent-volumes"></a>Volumi permanenti

I volumi definiti e creati nell'ambito del ciclo di vita del pod esistono solo finché non viene eliminato il pod. I pod si aspettano in genere che le rispettive risorse di archiviazione rimangano disponibili se un pod viene ripianificato su un host diverso durante un evento di manutenzione, in particolare in StatefulSets. Un *volume permanente* è una risorsa di archiviazione creata e gestita dall'API di Kubernetes che può esistere oltre la durata di un singolo pod.

Per rendere disponibile un volume persistente si usano Dischi di Azure o File di Azure. Come indicato nella sezione precedente sui volumi, la scelta di dischi o file dipende spesso dalla necessità di accedere contemporaneamente ai dati o al livello di prestazioni.

![Volumi permanenti in un cluster del servizio Azure Kubernetes](media/concepts-storage/persistent-volumes.png)

Un volume permanente può essere creato *staticamente* da un amministratore del cluster oppure *dinamicamente* dal server dell'API di Kubernetes. Se un pod viene pianificato e richiede risorse di archiviazione non attualmente disponibili, Kubernetes può creare la risorsa di archiviazione sottostante Dischi di Azure o File di Azure e collegarla al pod. Il provisioning dinamico usa una *StorageClass* per identificare il tipo di archiviazione di Azure da creare.

## <a name="storage-classes"></a>Classi di archiviazione

Per definire livelli di archiviazione diversi, ad esempio Premium e Standard, è possibile creare una *StorageClass*. La StorageClass definisce anche i *reclaimPolicy*. I criteri reclaimPolicy controllano il comportamento della risorsa di archiviazione di Azure sottostante quando il pod viene eliminato e il volume permanente potrebbe non essere più necessario. La risorsa di archiviazione sottostante può essere eliminata o conservata per l'uso con un pod futuro.

Nel servizio Azure Kubernetes vengono create due StorageClass iniziali:

- *default* - Usa l'archiviazione Standard di Azure per creare un disco gestito. I criteri di recupero indicano che il disco di Azure sottostante deve essere eliminato quando viene eliminato il pod che lo ha usato.
- *managed-premium* - Usa l'archiviazione Premium di Azure per creare un disco gestito. Anche in questo caso, i criteri di recupero indicano che il disco di Azure sottostante deve essere eliminato quando viene eliminato il pod che lo ha usato.

Se non si specifica una StorageClass per un volume permanente, viene usata la StorageClass predefinita. Prestare attenzione per la richiesta di volumi permanenti, in modo che usino le risorse di archiviazione appropriate necessarie. È possibile creare una StorageClass per esigenze aggiuntive con `kubectl`. L'esempio seguente usa Managed Disks Premium e specifica che il disco di Azure sottostante deve essere *conservato* quando viene eliminato il pod:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>Attestazioni di volume permanente

Una PersistentVolumeClaim richiede risorse di archiviazione su disco o file con StorageClass, modalità di accesso e dimensioni particolari. Il server dell'API di Kubernetes può effettuare in modo dinamico il provisioning della risorsa di archiviazione sottostante in Azure se non è presente alcuna risorsa esistente per soddisfare l'attestazione in base alla StorageClass definita. La definizione del pod include il montaggio del volume dopo la connessione del volume al pod.

![Attestazioni di volume permanente in un cluster del servizio Azure Kubernetes](media/concepts-storage/persistent-volume-claims.png)

Un volume permanente viene *associato* a un PersistentVolumeClaim dopo l'assegnazione di una risorsa di archiviazione disponibile al pod che la richiede. Esiste un mapping 1:1 tra volumi permanenti e attestazioni.

Il manifesto YAML di esempio seguente mostra un'attestazione di volume permanente che usa la StorageClass *managed-premium* e richiede un disco *5Gi*:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Quando si crea una definizione di pod, viene specificata l'attestazione di volume permanente per richiedere le risorse di archiviazione desiderate. È quindi necessario specificare anche il *volumeMount* per consentire alle applicazioni di leggere e scrivere dati. Il manifesto YAML di esempio seguente mostra come l'attestazione di volume permanente precedente può essere usata per montare un volume in */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Passaggi successivi

Per procedure consigliate associati, vedere [procedure consigliate per i backup nel servizio contenitore di AZURE e archiviazione][operator-best-practices-storage].

Per informazioni su come creare i volumi dinamici e statici che usano Dischi di Azure o File di Azure, vedere gli articoli di procedure seguenti:

- [Creare un volume statico usando Dischi di Azure][aks-static-disks]
- [Creare un volume statico usando File di Azure][aks-static-files]
- [Creare un volume dinamico usando Dischi di Azure][aks-dynamic-disks]
- [Creare un volume dinamico usando File di Azure][aks-dynamic-files]

Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes / Cluster servizio Azure Kubernetes e carichi di lavoro][aks-concepts-clusters-workloads]
- [Kubernetes / Identità di servizio Azure Kubernetes][aks-concepts-identity]
- [Kubernetes / Sicurezza di servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes / Reti virtuali in servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes / Ridimensionamento in servizio Azure Kubernetes][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
