---
title: 'Panoramica: eseguire una verifica di accesso in Privileged Identity Management per le risorse di Azure | Microsoft Docs'
description: Questo documento descrive come eseguire una verifica di accesso in PIM per risorse di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b53ae66d927dfacd1048a6bd0fa9abdb6f2a7862
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751153"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Usare un dashboard di risorse per eseguire una verifica di accesso

È possibile usare un dashboard di risorse per eseguire una verifica di accesso in Privileged Identity Management (PIM) per le risorse di Azure. Il dashboard Visualizzazione amministratore include tre componenti principali:

- Una rappresentazione grafica delle attivazioni del ruolo delle risorse.
- Due grafici che visualizzano la distribuzione delle assegnazioni di ruolo in base al tipo di assegnazione.
- Un'area dati che si riferisce a nuove assegnazioni di ruolo.

![Screenshot del dashboard Visualizzazione amministratore che mostra grafici e diagrammi](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Screenshot del dashboard Visualizzazione amministratore che mostra elenchi di dati](media/azure-pim-resource-rbac/role-settings.png)

La rappresentazione grafica delle attivazioni del ruolo delle risorse copre gli ultimi sette giorni. Come ambito per questi dati viene specificata la risorsa selezionata e vengono visualizzate attivazioni per i ruoli più comuni (proprietario, collaboratore, amministratore accesso utenti) e per tutti i ruoli combinati.

Alla destra del grafico relativo alle attivazioni due grafici mostrano la distribuzione delle assegnazioni di ruolo in base al tipo di assegnazione, per utenti e gruppi. È possibile cambiare il valore in una percentuale o viceversa selezionando una sezione del grafico.

Sotto i grafici viene visualizzato il numero di utenti e gruppi con nuove assegnazioni di ruolo negli ultimi 30 giorni e viene mostrato un elenco di ruoli ordinati in base a numero totale di assegnazioni (in ordine decrescente).


