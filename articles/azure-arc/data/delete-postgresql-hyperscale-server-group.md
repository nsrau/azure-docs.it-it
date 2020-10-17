---
title: Eliminare un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc
description: Eliminare un gruppo di server con iperscalabilità abilitato per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cacef205b614170df210e03ddf3978a64a90ea22
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151936"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Eliminare un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

Questo documento descrive i passaggi per eliminare un gruppo di server dalla configurazione di Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>Eliminare il gruppo di server

Si supponga, ad esempio, di voler eliminare l'istanza di _postgres01_ dall'installazione seguente:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Il formato generale del comando Delete è il seguente:
```console
azdata arc postgres server delete -n <server group name>
```
Per ulteriori informazioni sul comando DELETE, eseguire:
```console
azdata arc postgres server delete --help
```

### <a name="lets-delete-the-server-group-used-in-this-example"></a>Eliminare il gruppo di server usato in questo esempio:
```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Recuperare le attestazioni del volume permanente Kubernetes (PVC)

L'eliminazione di un gruppo di server non comporta la rimozione del [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)associato. Questo si verifica per motivi strutturali. L'intento è di consentire all'utente di accedere ai file di database nel caso in cui l'eliminazione dell'istanza fosse accidentale. L'eliminazione dei PVC non è obbligatoria, ma è consigliabile. Se i PVC non vengono recuperati, alla fine si verificheranno degli errori perché il cluster Kubernetes rileverà spazio insufficiente su disco. Per recuperare i PVC, seguire questa procedura:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. elencare il PVC per il gruppo di server che è stato eliminato
Per elencare il PVC, eseguire questo comando:
```console
kubectl get pvc [-n <namespace name>]
```

Restituisce l'elenco di PVC, in particolare il PVC per il gruppo di server che è stato eliminato. Ad esempio:
```console
kubectl get pvc
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Sono disponibili 8 PVC per questo gruppo di server.

### <a name="2-delete-each-of-the-pvcs"></a>2. eliminare ogni PVC
Eliminare i dati e i log PVC per ognuno dei nodi di iperscala PostgreSQL (coordinatore e worker) del gruppo di server che è stato eliminato.
Il formato generale del comando è il seguente: 
```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Ad esempio:
```console
kubectl delete pvc data-postgres01-0
kubectl delete pvc data-postgres01-1 
kubectl delete pvc data-postgres01-2
kubectl delete pvc data-postgres01-3
kubectl delete pvc logs-postgres01-0
kubectl delete pvc logs-postgres01-1
kubectl delete pvc logs-postgres01-2
kubectl delete pvc logs-postgres01-3
```

Ognuno di questi comandi di kubectl conferma la corretta eliminazione del PVC. Ad esempio:
```console
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>**Nota** Come indicato, la mancata eliminazione di PVC potrebbe eventualmente ottenere il cluster Kubernetes in una situazione in cui verrà generato un errore. Alcuni di questi errori possono includere l'impossibilità di accedere al cluster Kubernetes con azdata perché i baccelli possono essere eliminati a causa di questo problema di archiviazione (normale comportamento di Kubernetes).
>
> Ad esempio, è possibile visualizzare i messaggi nei log simili a:  
    > Annotazioni: microsoft.com/ignore-pod-health: true  
    > Stato: non riuscito  
    > Motivo: rimossi  
    > Messaggio: la risorsa del nodo è insufficiente: effimero-archiviazione. Il controller del contenitore utilizza 16372Ki, che supera la richiesta di 0.
    
## <a name="next-step"></a>Passaggio successivo
Creare un' [iperscalabilità PostgreSQL abilitata per Azure Arc](create-postgresql-hyperscale-server-group.md)
