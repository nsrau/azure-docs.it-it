---
title: Visualizzare gli utenti con ruoli delle risorse di Azure in PIM | Microsoft Docs
description: Questo articolo descrive come visualizzare gli utenti con ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: aee172bc6fc77aaac8d2d52037a481fdb976d308
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188969"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>Visualizzare gli utenti con ruoli delle risorse di Azure in PIM

Con Azure Active Directory Privileged Identity Management (PIM) è possibile gestire, controllare e monitorare l'accesso alle risorse di Azure nell'organizzazione, tra cui sottoscrizioni, gruppi di risorse e anche macchine virtuali. Ogni risorsa nel portale di Azure che sfrutta la funzionalità Controllo degli accessi in base al ruolo di Azure può usare tutte le funzionalità di sicurezza e gestione del ciclo di vita di Azure AD PIM. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM per le risorse di Azure è uno strumento per gli amministratori delle risorse

- Verificare gli utenti e i gruppi a cui sono assegnati ruoli per le risorse di Azure amministrate
- Abilitare l'accesso su richiesta "Just-In-Time" per gestire le risorse come sottoscrizioni, gruppi di risorse e altro ancora
- Determinare la scadenza dell'accesso alle risorse assegnate a utenti/gruppi automaticamente con le nuove impostazioni di assegnazione con associazione temporale
- Assegnare l'accesso temporaneo alle risorse per attività rapide o pianificazioni su richiesta
- Imporre Multi-Factor Authentication per l'accesso alle risorse in qualsiasi ruolo predefinito o personalizzato 
- Ottenere report sull'accesso alle risorse e sulle attività correlate alle risorse durante la sessione attiva di un utente
- Ottenere avvisi quando a nuovi utenti o gruppi viene assegnato l'accesso alle risorse e quando vengono attivate assegnazioni idonee

## <a name="view-activation-and-azure-resource-activity"></a>Visualizzare l'attivazione e l'attività delle risorse di Azure

Se è necessario verificare quali azioni sono state effettuate da un utente specifico su diverse risorse, è possibile esaminare l'attività di Risorse di Azure associata a un periodo di attivazione specifico, per gli utenti idonei. Selezionare prima di tutto un utente dalla visualizzazione Membri o dall'elenco di membri in un ruolo specifico. Il risultato mostra una visualizzazione grafica delle azioni dell'utente in Risorse di Azure in base alla data e le attivazioni di ruoli recenti nello stesso periodo di tempo.

![](media/azure-pim-resource-rbac/user-details.png)

Se si seleziona un'attivazione di ruolo specifica, verranno visualizzati i dettagli relativi all'attivazione del ruolo e l'attività di Risorse di Azure corrispondente effettuata nel periodo in cui l'utente è risultato attivo.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>Verificare gli utenti autorizzati ad accedere in una sottoscrizione

Per verificare le assegnazioni di ruolo nella sottoscrizione, selezionare la scheda Membri dal menu di spostamento a sinistra oppure selezionare Ruoli e scegliere un ruolo specifico per verificarne i membri. 

Selezionare Verifica dalla barra delle azioni per visualizzare le verifiche di accesso esistenti e selezionare Aggiungi per creare una nuova verifica.

![](media/azure-pim-resource-rbac/owner.png)

[Altre informazioni sulle verifiche di accesso](pim-how-to-perform-security-review.md)

>[!NOTE]
Le verifiche sono attualmente supportate solo per risorse di tipo Sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli delle risorse di Azure in PIM](pim-resource-roles-assign-roles.md)
- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM](pim-resource-roles-approval-workflow.md)
- [Ruoli predefiniti in Azure](../../role-based-access-control/built-in-roles.md)
