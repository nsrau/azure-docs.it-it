---
title: Controllo di accesso delle cartelle di lavoro di monitoraggio di Azure
description: Semplifica la creazione di report complessi con cartelle di lavoro predefinite con parametri personalizzati con controllo degli accessi in base al ruolo
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 92ac1887aca8f30c551419ef9149073d79f333a5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143836"
---
# <a name="access-control"></a>Controllo di accesso

Il controllo degli accessi nelle cartelle di lavoro fa riferimento a due elementi:

* Accesso richiesto per leggere i dati in una cartella di lavoro. Questo accesso è controllato dai [ruoli di Azure](../../role-based-access-control/overview.md) standard sulle risorse utilizzate nella cartella di lavoro. Le cartelle di lavoro non specificano o configurano l'accesso a tali risorse. In genere gli utenti ottengono questo accesso a tali risorse usando il ruolo di [lettore di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) su tali risorse.

* Accesso richiesto per salvare le cartelle di lavoro

    - Per salvare le `("My")` cartelle di lavoro private non sono necessari privilegi aggiuntivi. Tutti gli utenti possono salvare le cartelle di lavoro private e possono visualizzare solo le cartelle di lavoro.
    - Per salvare le cartelle di lavoro condivise, è necessario disporre dei privilegi di scrittura in un gruppo di risorse. Questi privilegi vengono in genere specificati dal ruolo [collaboratore monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor) , ma possono essere impostati anche tramite il ruolo *collaboratore cartelle di lavoro* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Ruoli standard con privilegi correlati alla cartella di lavoro

Il [lettore di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) include privilegi/Read standard che verrebbero usati dagli strumenti di monitoraggio (incluse le cartelle di lavoro) per leggere i dati dalle risorse.

Il [collaboratore del monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor) include `/write` privilegi generali utilizzati da vari strumenti di monitoraggio per il salvataggio di elementi (incluso il `workbooks/write` privilegio per salvare cartelle di lavoro condivise).
"Collaboratore cartelle di lavoro" aggiunge privilegi "cartelle di lavoro/scrittura" a un oggetto per salvare le cartelle di lavoro condivise.
Non sono necessari privilegi speciali per consentire agli utenti di salvare le cartelle di lavoro private che possono visualizzare solo.

Per il controllo degli accessi in base al ruolo personalizzato:

Aggiungere `microsoft.insights/workbooks/write` per salvare le cartelle di lavoro condivise. Per ulteriori informazioni, vedere il ruolo [collaboratore cartella di lavoro](../../role-based-access-control/built-in-roles.md#monitoring-contributor) .

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](./workbooks-overview.md#visualizations) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.