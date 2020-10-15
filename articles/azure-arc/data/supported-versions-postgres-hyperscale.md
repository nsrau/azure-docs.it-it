---
title: Versioni supportate Postgres con Azure Arc abilitata per l'iperscalabilità PostgreSQL
description: Versioni supportate Postgres con Azure Arc abilitata per l'iperscalabilità PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939185"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Versioni supportate di Postgres con PostgreSQL Hyperscale abilitato per Azure Arc

Questo articolo illustra le versioni di Postgres disponibili con l'iperscalabilità di PostgreSQL abilitata per Azure Arc.
L'elenco delle versioni supportate si evolve nel tempo. Attualmente, le versioni principali supportate sono:
- Postgres 12 (impostazione predefinita)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Come scegliere tra le versioni?
Si consiglia di esaminare le versioni per le quali sono state progettate le applicazioni e quali sono le funzionalità di ognuna delle versioni. Per altre informazioni, vedere la pagina relativa a ogni versione nel sito ufficiale Postgres:
- [Postgres 12 (impostazione predefinita)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Come creare una particolare versione in Azure Arc abilitata per l'iperscalabilità di PostgreSQL
Al momento della creazione, è possibile indicare la versione da creare passando il parametro _--Engine-Version_ . Se non si specificano le informazioni sulla versione, per impostazione predefinita viene creato un gruppo di server di Postgres versione 12.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Come ricevere una notifica quando sono disponibili altre versioni?
Tornare a leggere questo articolo. Verrà aggiornato in base alle esigenze. È anche possibile elencare i tipi di definizioni di risorse personalizzate (CRD) nel controller dati Arc del cluster Kubernetes.
Eseguire il comando seguente:
```console
kubectl get crds
```

Verrà restituito un output simile al seguente:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

In questo esempio, questo output indica che esistono due tipi di CRD correlati a Postgres:
- postgresql-12s.arcdata.microsoft.com è la CRD per Postgres 12
- postgresql-11s.arcdata.microsoft.com è la CRD per Postgres 11

Si tratta di CRD, non di gruppi di server. La presenza di un CRD non indica che è stato creato o meno un gruppo di server di tale versione.
Il CRD indica il tipo di risorse che è possibile creare.

## <a name="next-steps"></a>Passaggi successivi:
- [Scopri di più sulla creazione dell'iperscalabilità PostgreSQL abilitata per Azure Arc](create-postgresql-hyperscale-server-group.md)
- [Leggere le informazioni su come ottenere un elenco dei gruppi di server con iperscalabilità di PostgreSQL abilitati per Azure Arc creati nel controller di dati Arc](list-server-groups-postgres-hyperscale.md)
