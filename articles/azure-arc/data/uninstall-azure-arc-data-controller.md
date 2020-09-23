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
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939112"
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

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Facoltativamente, eliminare lo spazio dei nomi del controller di dati di Azure Arc


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Passaggi successivi

[Che cosa sono i servizi dati abilitati per Azure Arc?](overview.md)