---
title: Elimina Istanza gestita SQL abilitata per Azure Arc
description: Elimina Istanza gestita SQL abilitata per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940851"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Elimina Istanza gestita SQL abilitata per Azure Arc
Questo articolo descrive come eliminare un Istanza gestita SQL abilitato per Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Visualizza istanze di SQL gestite abilitate per Azure Arc esistenti
Per visualizzare le istanze gestite di SQL, eseguire il comando seguente:

```console
azdata arc sql mi list
```

L'output avrà un aspetto simile al seguente:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Eliminare un Istanza gestita SQL abilitato per Azure Arc
Per eliminare un Istanza gestita SQL, eseguire il comando seguente:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

L'output avrà un aspetto simile al seguente:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Recuperare le attestazioni del volume permanente Kubernetes (PVC)

L'eliminazione di un Istanza gestita SQL non comporta la rimozione del [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)associato. Questo si verifica per motivi strutturali. L'intenzione è di consentire all'utente di accedere ai file di database nel caso in cui l'eliminazione dell'istanza fosse accidentale. L'eliminazione di PVC non è obbligatoria. È tuttavia consigliabile. Se questi PVC non vengono recuperati, si verificheranno errori in quanto il cluster Kubernetes esaurirà lo spazio su disco. Per recuperare il PVC, seguire questa procedura:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. elencare il PVC per il gruppo di server che è stato eliminato
Per elencare il PVC, eseguire il comando seguente:
```console
kubectl get pvc
```

Nell'esempio seguente si noti la PVC per le istanze gestite di SQL eliminate.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. eliminare ogni PVC
Eliminare i dati e i PVC di log per ogni istanza di SQL gestita eliminata.
Il formato generale del comando è il seguente: 
```console
kubectl delete pvc <name of pvc>
```

Ad esempio:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Ognuno di questi comandi di kubectl conferma la corretta eliminazione del PVC. Ad esempio:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Come indicato, la mancata eliminazione di PVC potrebbe eventualmente ottenere il cluster Kubernetes in una situazione in cui verrà generato un errore. Alcuni di questi errori possono includere l'impossibilità di accedere al cluster Kubernetes con azdata perché i baccelli possono essere eliminati a causa di questo problema di archiviazione (normale comportamento di Kubernetes).
>
> Ad esempio, è possibile visualizzare i messaggi nei log simili a:  
> - Annotazioni: microsoft.com/ignore-pod-health: true  
> - Stato: non riuscito  
> - Motivo: rimossi  
> - Messaggio: la risorsa del nodo è insufficiente: effimero-archiviazione. Il controller del contenitore utilizza 16372Ki, che supera la richiesta di 0.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [caratteristiche e funzionalità di Istanza gestita di SQL con abilitazione di Azure Arc](managed-instance-features.md)

[Iniziare creando un controller dati](create-data-controller.md)

Il controller dati è già stato creato? [Creare un'istanza gestita di SQL con abilitazione di Azure Arc](create-sql-managed-instance.md)