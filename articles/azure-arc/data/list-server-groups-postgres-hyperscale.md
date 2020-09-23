---
title: Elencare i gruppi di server di scalabilità di PostgreSQL abilitati per Azure Arc creati in un controller di dati di Azure Arc
description: Elencare i gruppi di server di scalabilità di PostgreSQL abilitati per Azure Arc creati in un controller di dati di Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940791"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Elencare i gruppi di server di scalabilità di PostgreSQL abilitati per Azure Arc creati in un controller di dati di Azure Arc

Questo articolo illustra come recuperare l'elenco dei gruppi di server creati nel controller di dati Arc.

Per recuperare l'elenco, usare uno dei metodi seguenti una volta connessi al controller di dati Arc:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>Dall'interfaccia della riga di comando con azdata
Il formato generale del comando è:
```console
azdata arc postgres server list
```

Verrà restituito un output simile al seguente:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Per ulteriori informazioni sui parametri disponibili per questo comando, eseguire:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>Dall'interfaccia della riga di comando con kubectl
Eseguire uno dei comandi seguenti.

**Per elencare i gruppi di server indipendentemente dalla versione di Postgres, eseguire:**
```console
kubectl get postgresqls
```
Verrà restituito un output simile al seguente:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Per elencare i gruppi di server di una versione specifica di Postgres, eseguire:**
```console
kubectl get postgresql-12
```

Per elencare i gruppi di server che eseguono la versione 11 di Postgres, sostituire _PostgreSQL-12_ con _PostgreSQL-11_.

## <a name="next-steps"></a>Passaggi successivi:

* [Leggere l'articolo su come ottenere gli endpoint di connessione e formare le stringhe di connessione per la connessione al gruppo di server](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Vedere l'articolo su come visualizzare la configurazione di un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc](show-configuration-postgresql-hyperscale-server-group.md)
