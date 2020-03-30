---
title: Gestione dei ruoli di Azure AD in Privileged Identity Management (PIM) Documenti Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245980"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Funzionalità di gestione per i ruoli di Azure AD in Gestione delle identità con privilegiManagement

L'esperienza di gestione per i ruoli di Azure AD in Gestione delle identità con privilegi è stata aggiornata per unificare la modalità di gestione dei ruoli di Azure AD e dei ruoli delle risorse di Azure.The management experience for Azure AD roles in Privileged Identity Management has been updated to unify how Azure AD roles and Azure resource roles are managed. In precedenza, la gestione delle identità con privilegi per i ruoli delle risorse di Azure ha avuto un paio di funzionalità chiave che non erano disponibili per i ruoli di Azure AD.

Con l'aggiornamento attualmente implementato, stiamo unendo i due in un'unica esperienza di gestione e in esso si ottiene la stessa funzionalità per i ruoli di Azure AD come per i ruoli delle risorse di Azure.With the update being currently rolled out, we are merge the two into a single management experience, and in it you get the same functionality for Azure AD roles as for Azure resource roles. In questo articolo viene informa dell'utente delle funzionalità aggiornate e di eventuali requisiti.


## <a name="time-bound-assignments"></a>Assegnazioni con limite di tempo

In precedenza, nei ruoli Gestione identità con privilegi per Azure AD, si aveva familiarità con le assegnazioni di ruolo con due stati possibili: *idonei* e *permanenti.* Ora è possibile impostare un'ora di inizio e di fine per ogni tipo di assegnazione. Questa aggiunta offre quattro possibili stati in cui è possibile inserire un'assegnazione:This addition gives you four possible states in which you can place an assignment:

- Idoneo in modo permanente
- Attivo in modo permanente
- Idoneo, con date di inizio/fine specificate per l'assegnazione
- Attivo, con date di inizio/fine specificate per l'assegnazione

In molti casi, anche se non si desidera che gli utenti dispongano di assegnazioni idonee e attivino i ruoli ogni volta, è comunque possibile proteggere l'organizzazione di Azure AD impostando un'ora di scadenza per le assegnazioni. Ad esempio, se si dispone di alcuni utenti temporanei idonei, è consigliabile impostare una scadenza in modo da rimuoverli automaticamente dall'assegnazione di ruolo al termine del lavoro.

## <a name="new-role-settings"></a>Nuove impostazioni del ruolo

Vengono inoltre aggiunti nuove impostazioni per i ruoli di Azure AD. In precedenza, era possibile configurare le impostazioni di attivazione solo in base al ruolo. In altre parole, le impostazioni di attivazione, ad esempio i requisiti di autenticazione a più fattori e i requisiti di ticket di eventi imprevisti/richieste, sono state applicate a tutti gli utenti idonei per un ruolo specificato. A questo punto, è possibile configurare se un singolo utente deve eseguire l'autenticazione a più fattori prima di poter attivare un ruolo. Inoltre, è possibile avere il controllo avanzato sulle e-mail di gestione delle identità privilegiate relative a ruoli specifici.

## <a name="extend-and-renew-assignments"></a>Estendere e rinnovare le assegnazioni

Non appena si capisce l'assegnazione limite di tempo, la prima domanda che si potrebbe chiedere è cosa succede se un ruolo è scaduto? In questa nuova versione vengono fornite due opzioni per questo scenario:In this new version, we provide two options for this scenario:

- Estendi: quando un'assegnazione di ruolo si avvicina alla scadenza, l'utente può utilizzare Gestione identità con privilegi per richiedere un'estensione per tale assegnazione di ruoloExtend – When a role assignment nears its expiration, the user can use Privileged Identity Management to request an extension for that role assignment
- Rinnovo: quando un'assegnazione di ruolo è scaduta, l'utente può utilizzare Gestione identità con privilegi per richiedere un rinnovo per tale assegnazione di ruolo

Entrambe le azioni avviate dall'utente richiedono l'approvazione di un amministratore globale o di un amministratore del ruolo Privileged.Both user-initiated actions require an approval from a Global administrator or Privileged role administrator. Gli amministratori non dovranno più gestire queste scadenze. Devono solo attendere l'estensione o le richieste di rinnovo e approvarle se la richiesta è valida.

## <a name="api-changes"></a>Modifiche all'API

Quando i clienti hanno la versione aggiornata implementata nell'organizzazione di Azure AD, l'API del grafico esistente smetterà di funzionare. È necessario eseguire la transizione per usare l'API Graph per i ruoli delle risorse di Azure.You must transition to use the [Graph API for Azure resource roles](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Per gestire i ruoli di `/azureResources` Azure `/aadroles` AD usando tale API, `resourceId`scambiare con la firma e usare l'ID directory per il file .

Abbiamo fatto del nostro meglio per raggiungere tutti i clienti che utilizzano l'API precedente per far loro conoscere questo cambiamento in anticipo. Se l'organizzazione di Azure AD è stata spostata alla nuova versione e pim_preview@microsoft.comsi dipende ancora dall'API precedente, contattare il team in .

## <a name="powershell-change"></a>Modifica di PowerShell

Per i clienti che utilizzano il modulo PowerShell per la gestione delle identità con privilegi per i ruoli di Azure AD, PowerShell smetterà di funzionare con l'aggiornamento. Al posto dei cmdlet precedenti è necessario utilizzare i cmdlet di Gestione identità con privilegi all'interno del modulo PowerShell di Azure AD Preview. Installare il modulo Azure AD PowerShell da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). È ora possibile [leggere la documentazione e gli esempi per le operazioni PIM in questo modulo di PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo personalizzato di Azure AD](azure-ad-custom-roles-assign.md)
- [Rimuovere o aggiornare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-configure.md)
- [Definizioni dei ruoli in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
