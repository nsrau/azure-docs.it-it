---
title: Gestire i dati degli utenti rilevati in un'indagine del Centro sicurezza di Azure | Microsoft Docs
description: " Questo articolo illustra come gestire i dati degli utenti rilevati tramite la funzionalità di indagine del Centro sicurezza di Azure. "
services: operations-management-suite
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rkarlin
ms.openlocfilehash: bec9be627937913c268d65bb6cdadcf68c7a3fb4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60909214"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gestire i dati degli utenti rilevati in un'indagine del Centro sicurezza di Azure
Questo articolo illustra come gestire i dati degli utenti rilevati tramite la funzionalità di indagine del Centro sicurezza di Azure. Analisi dati vengono archiviati in [monitoraggio di Azure registra](../log-analytics/log-analytics-overview.md) ed esposte nel Centro sicurezza. La gestione dei dati degli utenti include la possibilità di eliminare o esportare i dati.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Ricerca e identificazione dei dati personali
Nel portale di Azure è possibile usare la [funzionalità di indagine](../security-center/security-center-investigation.md) del Centro sicurezza per cercare dati personali. La funzionalità di indagine è disponibile in **Avvisi di sicurezza**.

La funzionalità di indagine consente di visualizzare tutte le entità, le informazioni sull'utente e i dati nella scheda **Entità**.

## <a name="securing-and-controlling-access-to-personal-information"></a>Sicurezza e controllo dell'accesso alle informazioni personali
Un utente del Centro sicurezza a cui è assegnato il ruolo Lettore, Proprietario, Collaboratore o Amministratore dell'account può accedere ai dati dei clienti direttamente dallo strumento.

Per altre informazioni sui ruoli Lettore, Proprietario e Collaboratore, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md). Per altre informazioni sul ruolo Amministratore dell'account, vedere [Amministratori delle sottoscrizioni di Azure](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="deleting-personal-data"></a>Eliminazione dei dati personali
Un utente del Centro sicurezza a cui è assegnato il ruolo Proprietario, Collaboratore o Amministratore dell'account può eliminare le informazioni dell'indagine.

Per eliminare un'indagine, è possibile inviare una richiesta `DELETE` all'API REST di Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Per trovare l'input `incidentName`, è possibile elencare tutti gli eventi imprevisti tramite una richiesta `GET`:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Esportazione dei dati personali
Un utente del Centro sicurezza a cui è assegnato il ruolo Proprietario, Collaboratore o Amministratore dell'account può esportare le informazioni dell'indagine. Per esportare le informazioni dell'indagine, passare alla scheda **Entità** per copiare e incollare le informazioni pertinenti.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla gestione dei dati degli utenti, vedere [Gestire i dati degli utenti nel Centro sicurezza di Azure](security-center-privacy.md).
Per altre informazioni sull'eliminazione di dati privati nei log di monitoraggio di Azure, vedere [come esportare ed eliminare dati privati](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
