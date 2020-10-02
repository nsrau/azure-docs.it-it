---
title: Elimina il controller dati di Azure Arc
description: Elimina il controller dati di Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a040200c5746defcaee84a951521d5919c0c4d28
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660677"
---
# <a name="delete-azure-arc-data-controller"></a>Elimina il controller dati di Azure Arc

L'articolo seguente descrive come eliminare un controller dati di Azure Arc.

Prima di procedere, verificare che tutti i servizi dati creati nel controller dati vengano rimossi come indicato di seguito:

## <a name="log-in-to-the-data-controller"></a>Accedere al controller dati

Accedere al controller dati che si desidera eliminare:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Elenco & eliminare i servizi dati esistenti

Eseguire il comando seguente per verificare se sono state create istanze gestite da SQL:

```
azdata arc sql mi list
```

Per ogni istanza gestita di SQL dall'elenco precedente, eseguire il comando delete come indicato di seguito:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Analogamente, per verificare la presenza di istanze di iperscala PostgreSQL, eseguire:

```
azdata arc postgres server list
```

Per ogni istanza di scalabilità di PostgreSQL, quindi, eseguire il comando delete come indicato di seguito:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Elimina controller

Dopo che tutte le istanze gestite di SQL e le istanze di iperscalabilità di PostgreSQL sono state rimosse, è possibile eliminare il controller dati come segue:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Rimuovere SCCs (solo Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Elimina oggetti a livello di cluster

Oltre agli oggetti con ambito spazio dei nomi se si desidera eliminare anche gli oggetti a livello di cluster, ad esempio CRD, `clusterroles` ed `clusterrolebindings` eseguire i comandi seguenti:

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Facoltativamente, eliminare lo spazio dei nomi del controller di dati di Azure Arc


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Passaggi successivi

[Che cosa sono i servizi dati abilitati per Azure Arc?](overview.md)
