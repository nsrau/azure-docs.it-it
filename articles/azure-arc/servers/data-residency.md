---
title: Residenza dei dati
description: Residenza dei dati e informazioni sui server abilitati per Azure Arc.
ms.topic: reference
ms.date: 10/08/2020
ms.custom: references_regions
ms.openlocfilehash: c5ece96acc3ee07ba2896279888363c7d52d737e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856450"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Server abilitati per Azure Arc: residenza dei dati

Questo articolo illustra il concetto di residenza dei dati e il modo in cui si applica ai server abilitati per Azure Arc.

I server abilitati per Azure Arc sono **[disponibili](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** in **Stati Uniti, Europa, Regno Unito, Australia e Asia Pacifico**.

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