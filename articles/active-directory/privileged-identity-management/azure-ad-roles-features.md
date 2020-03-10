---
title: Gestione dei ruoli di Azure AD in Privileged Identity Management (PIM) | Microsoft Docs
description: Come gestire i ruoli di Azure AD per l'assegnazione Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375412"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Funzionalità di gestione per i ruoli di Azure AD in Privileged Identity Management

L'esperienza di gestione per i ruoli Azure AD in Privileged Identity Management è stata aggiornata per unificare il modo in cui vengono gestiti Azure AD ruoli e i ruoli delle risorse di Azure. In precedenza, Privileged Identity Management per i ruoli delle risorse di Azure includeva due funzionalità chiave che non erano disponibili per i ruoli di Azure AD.

Con l'aggiornamento attualmente implementato, i due vengono uniti in un'unica esperienza di gestione e, in questo modo, si ottiene la stessa funzionalità per i ruoli di Azure AD come per i ruoli delle risorse di Azure. Questo articolo informa le funzionalità aggiornate e tutti i requisiti.


## <a name="time-bound-assignments"></a>Assegnazioni con scadenza

In precedenza Privileged Identity Management per i ruoli di Azure AD, si conoscevano le assegnazioni di ruolo con due stati possibili, *idonei* e *permanenti*. A questo punto è possibile impostare un'ora di inizio e di fine per ogni tipo di assegnazione. Questa aggiunta fornisce quattro stati possibili in cui è possibile effettuare un'assegnazione:

- Idoneo in modo permanente
- Attivo in modo permanente
- Idoneo, con le date di inizio/fine specificate per l'assegnazione
- Attivo, con le date di inizio/fine specificate per l'assegnazione

In molti casi, anche se non si vuole che gli utenti dispongano di assegnazione idonea e attivino ogni volta i ruoli, è comunque possibile proteggere l'organizzazione Azure AD impostando una data di scadenza per le assegnazioni. Ad esempio, se si dispone di alcuni utenti temporanei idonei, è consigliabile impostare una scadenza in modo da rimuoverli automaticamente dall'assegnazione di ruolo al termine del lavoro.

## <a name="new-role-settings"></a>Impostazioni nuovo ruolo

Vengono inoltre aggiunte nuove impostazioni per i ruoli Azure AD. In precedenza era possibile configurare solo le impostazioni di attivazione per singolo ruolo. Ovvero le impostazioni di attivazione, ad esempio i requisiti di autenticazione a più fattori e i requisiti del ticket per eventi imprevisti e richieste, sono stati applicati a tutti gli utenti idonei per un ruolo specifico. A questo punto, è possibile configurare se un singolo utente deve eseguire l'autenticazione a più fattori prima di poter attivare un ruolo. Inoltre, è possibile disporre di un controllo avanzato sui messaggi di posta elettronica di Privileged Identity Management correlati a ruoli specifici.

## <a name="extend-and-renew-assignments"></a>Estendi e rinnova le assegnazioni

Non appena si scopre l'assegnazione temporale, la prima domanda che si potrebbe chiedere è che cosa accade se un ruolo è scaduto? In questa nuova versione sono disponibili due opzioni per questo scenario:

- Estendi: quando un'assegnazione di ruolo si avvicina alla scadenza, l'utente può usare Privileged Identity Management per richiedere un'estensione per tale assegnazione di ruolo
- Rinnovo: quando un'assegnazione di ruolo scade, l'utente può usare Privileged Identity Management per richiedere un rinnovo per tale assegnazione di ruolo

Per entrambe le azioni avviate dall'utente è richiesta un'approvazione da un amministratore globale o da un amministratore del ruolo con privilegi. Gli amministratori non dovranno più occuparsi della gestione di queste scadenze. È sufficiente attendere le richieste di estensione o rinnovo e approvarle se la richiesta è valida.

## <a name="api-changes"></a>Modifiche all'API

Quando la versione aggiornata dei clienti viene implementata nell'organizzazione Azure AD, l'API Graph esistente smette di funzionare. È necessario eseguire la transizione per usare la [API Graph per i ruoli delle risorse di Azure](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Per gestire i ruoli Azure AD usando tale API, scambiare `/azureResources` con `/aadroles` nella firma e usare l'ID directory per la `resourceId`.

Abbiamo provato il nostro meglio per raggiungere tutti i clienti che usano l'API precedente per informare in anticipo questa modifica. Se il Azure AD organizzazione è stato spostato nella nuova versione e si dipende ancora dalla vecchia API, contattare il team in pim_preview@microsoft.com.

## <a name="powershell-change"></a>Modifica di PowerShell

Per i clienti che usano il modulo Privileged Identity Management PowerShell per Azure AD ruoli, PowerShell smetterà di funzionare con l'aggiornamento. Al posto dei cmdlet precedenti è necessario usare i cmdlet di Privileged Identity Management all'interno del modulo PowerShell di Azure AD Preview. Installare il modulo Azure AD PowerShell dal [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). È ora possibile [leggere la documentazione e gli esempi per le operazioni PIM in questo modulo di PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo personalizzato di Azure AD](azure-ad-custom-roles-assign.md)
- [Rimuovere o aggiornare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-configure.md)
- [Definizioni dei ruoli in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
