---
title: Residenza dei dati
description: Residenza dei dati e informazioni sui server abilitati per Azure Arc.
ms.topic: reference
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 73acb07b8caa3c1368c936463f04969c29d37985
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327766"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Server abilitati per Azure Arc: residenza dei dati

Questo articolo illustra il concetto di residenza dei dati e il modo in cui si applica ai server abilitati per Azure Arc.

I server abilitati per Azure Arc sono **[disponibili](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** in **Stati Uniti, Europa, regno Unito o Asia Pacifico**.

## <a name="data-residency"></a>Residenza dei dati

I server abilitati per Azure Arc archiviano le impostazioni di configurazione dell' [estensione delle macchine virtuali di Azure](manage-vm-extensions.md) , ovvero i valori delle proprietà, che l'estensione richiede specificare prima di provare a abilitare nel computer connesso. Ad esempio, quando si Abilita l'estensione Log Analytics VM, viene richiesto l'ID dell' **area di lavoro** log Analytics e la **chiave primaria**.

Vengono raccolte anche le informazioni sui metadati sul computer connesso. In particolare:

* Nome e versione del sistema operativo
* Nome computer
* Nome di dominio completo (FQDN) del computer
* Versione Agente computer connesso

I server abilitati per Arc consentono di specificare l'area in cui verranno archiviati i dati. Microsoft può eseguire la replica in altre aree per la resilienza dei dati, ma Microsoft non replica né sposta i dati all'esterno dell'area geografica. Questi dati vengono archiviati nell'area geografica in cui è configurata la risorsa del computer Azure Arc. Ad esempio, se il computer è registrato con Arc nell'area Stati Uniti orientali, questi dati vengono archiviati nell'area Stati Uniti.

Per altre informazioni sul supporto per la resilienza e la conformità a livello di area, vedere [geografia di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla progettazione per la [resilienza di Azure](/azure/architecture/reliability/architect).