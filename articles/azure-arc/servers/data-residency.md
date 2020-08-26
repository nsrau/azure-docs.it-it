---
title: Residenza dei dati
description: Residenza dei dati e informazioni sui server abilitati per Azure Arc (anteprima).
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 028398c27dde9760192d366b9e8a91dca8e429cf
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88860972"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Server abilitati per Azure Arc (anteprima): residenza dei dati

Questo articolo illustra il concetto di residenza dei dati e il modo in cui si applica ai server abilitati per Azure Arc (anteprima).

I server abilitati per Azure Arc (anteprima) sono **[disponibili in anteprima](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** in **Stati Uniti, Europa o Asia Pacifico**.

## <a name="data-residency"></a>Residenza dei dati

I server abilitati per Azure Arc (anteprima) archiviano le impostazioni di configurazione dell' [estensione VM di Azure](manage-vm-extensions.md) , ovvero i valori delle proprietà, l'estensione richiede l'impostazione prima di provare ad abilitare nel computer connesso. Ad esempio, quando si Abilita l'estensione Log Analytics VM, viene richiesto l'ID dell' **area di lavoro** log Analytics e la **chiave primaria**.

Vengono raccolte anche le informazioni sui metadati sul computer connesso. In particolare:

* Nome e versione del sistema operativo
* Nome computer
* Nome di dominio completo (FQDN) del computer
* Versione Agente computer connesso

I server abilitati per Arc (anteprima) consentono di specificare l'area in cui verranno archiviati i dati. Microsoft può eseguire la replica in altre aree per la resilienza dei dati, ma Microsoft non replica né sposta i dati all'esterno dell'area geografica. Questi dati vengono archiviati nell'area geografica in cui è configurata la risorsa del computer Azure Arc. Ad esempio, se il computer è registrato con Arc nell'area Stati Uniti orientali, questi dati vengono archiviati nell'area Stati Uniti.

Per altre informazioni sul supporto per la resilienza e la conformità a livello di area, vedere [geografia di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla progettazione per la [resilienza di Azure](/architecture/reliability/architect).