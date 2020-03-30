---
title: Controllo dell'accesso Monitor di Azure
description: Semplifica la creazione di report complessi con cartelle di lavoro predefinite e personalizzate con controllo degli accessi in base al ruolo
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658422"
---
# <a name="access-control"></a>Controllo di accesso

Il controllo dell'accesso nelle cartelle di lavoro si riferisce a due elementi:Access control in workbooks refers to two things:

* Accesso necessario per leggere i dati in una cartella di lavoro. Questo accesso è controllato dai ruoli standard di [Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) nelle risorse usate nella cartella di lavoro. Le cartelle di lavoro non specificano o configurano l'accesso a tali risorse. Gli utenti ottengono in genere questo accesso a tali risorse usando il ruolo [Lettore monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) per tali risorse.

* Accesso necessario per salvare le cartelle di lavoro

    - Il `("My")` salvataggio di cartelle di lavoro private non richiede privilegi aggiuntivi. Tutti gli utenti possono salvare le cartelle di lavoro private e solo loro possono vedere tali cartelle di lavoro.
    - Il salvataggio delle cartelle di lavoro condivise richiede privilegi di scrittura in un gruppo di risorse per salvare la cartella di lavoro. Questi privilegi vengono in genere specificati dal ruolo [Collaboratore monitoraggio,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ma possono essere impostati anche tramite il ruolo *Collaboratore cartelle di* lavoro.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Ruoli standard con privilegi correlati alla cartella di lavoro

[Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) include privilegi standard /lettura che verrebbero utilizzati dagli strumenti di monitoraggio (incluse le cartelle di lavoro) per leggere i dati dalle risorse.

[Collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) di `/write` monitoraggio include privilegi generali utilizzati `workbooks/write` da vari strumenti di monitoraggio per il salvataggio degli elementi (inclusi i privilegi per salvare le cartelle di lavoro condivise).
"Collaboratore cartelle di lavoro" aggiunge privilegi di "cartelle di lavoro/scrittura" a un oggetto per salvare le cartelle di lavoro condivise.
Non sono necessari privilegi speciali per consentire agli utenti di salvare cartelle di lavoro private che solo loro possono visualizzare.

Per il controllo degli accessi in base al ruolo personalizzato:For custom Role-based access control:

Aggiungi `microsoft.insights/workbooks/write` per salvare le cartelle di lavoro condivise. Per ulteriori dettagli, vedere il ruolo [Collaboratore cartella di lavoro.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
