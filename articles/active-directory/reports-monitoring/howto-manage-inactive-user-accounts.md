---
title: Come gestire gli account utente inattivi in Azure AD | Microsoft Docs
description: Informazioni su come rilevare e gestire gli account utente in Azure AD diventati obsoleti
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886042"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Procedura: gestire account utente inattivi in Azure AD

Negli ambienti di grandi dimensioni, gli account utente non vengono sempre eliminati quando i dipendenti lasciano un'organizzazione. In qualità di amministratore IT, si desidera rilevare e gestire questi account utente obsoleti perché rappresentano un rischio per la sicurezza.

Questo articolo illustra un metodo per gestire gli account utente obsoleti in Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Che cosa sono gli account utente inattivi?

Gli account inattivi sono account utente che non sono più necessari per i membri dell'organizzazione per ottenere l'accesso alle risorse. Un identificatore di chiave per gli account inattivi è che non sono stati usati *per un periodo di tempo* per accedere all'ambiente. Poiché gli account inattivi sono collegati all'attività di accesso, è possibile usare il timestamp dell'ultimo accesso che ha avuto esito positivo per rilevarli. 

La sfida di questo metodo consiste nel definire cosa accade *per un periodo di tempo* nel caso dell'ambiente in uso. Ad esempio, gli utenti potrebbero non accedere a un ambiente *per un periodo di tempo*, perché sono in vacanza. Quando si definisce l'elemento Delta per gli account utente inattivi, è necessario considerare tutti i motivi legittimi per non accedere all'ambiente. In molte organizzazioni, il Delta per gli account utente inattivi è compreso tra 90 e 180 giorni. 

L'ultimo accesso riuscito fornisce le informazioni potenziali per la continua necessità dell'accesso alle risorse da parte dell'utente.  Può essere utile per determinare se l'appartenenza al gruppo o l'accesso all'app è ancora necessario o potrebbe essere rimosso. Per la gestione degli utenti esterni, è possibile capire se un utente esterno è ancora attivo nel tenant o se deve essere eliminato. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Come rilevare gli account utente inattivi

Si rilevano account inattivi valutando la proprietà **lastSignInDateTime** esposta dal tipo di risorsa **signInActivity** dell'API **Microsoft Graph** . Utilizzando questa proprietà, è possibile implementare una soluzione per gli scenari seguenti:

- **Utenti per nome**: in questo scenario si cerca un utente specifico in base al nome, che consente di valutare il lastSignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Utenti per data**: in questo scenario viene richiesto un elenco di utenti con un lastSignInDateTime prima di una data specificata:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Informazioni importanti

Questa sezione elenca le informazioni che è necessario conoscere sulla proprietà lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Come è possibile accedere a questa proprietà?

La proprietà **lastSignInDateTime** è esposta dal [tipo di risorsa SIGNINACTIVITY](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) dell' [API REST di Microsoft Graph](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>La proprietà lastSignInDateTime è disponibile tramite il cmdlet Get-AzureAdUser?

No.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Quale edizione di Azure AD è necessario accedere alla proprietà?

È possibile accedere a questa proprietà in tutte le edizioni di Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Quali autorizzazioni sono necessarie per leggere la proprietà?

Per leggere questa proprietà, è necessario concedere i diritti seguenti: 

- AuditLogs. Read. All
- Organization. Read. All  


### <a name="when-does-azure-ad-update-the-property"></a>Quando Azure AD aggiorna la proprietà?

Ogni accesso interattivo che ha avuto esito positivo comporta un aggiornamento dell'archivio dati sottostante. In genere, gli accessi riusciti vengono visualizzati nel report di accesso correlato entro 10 minuti.
 

### <a name="what-does-a-blank-property-value-mean"></a>Che cosa significa un valore di proprietà vuota?

Per generare un timestamp lastSignInDateTime, è necessario un accesso riuscito. Poiché la proprietà lastSignInDateTime è una nuova funzionalità, il valore della proprietà lastSignInDateTime può essere vuoto se:

- L'ultimo accesso riuscito di un utente ha avuto luogo prima del rilascio di questa funzionalità (1 dicembre 2019).
- L'account utente interessato non è mai stato usato per un accesso riuscito.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](tutorial-access-api-with-certificates.md)
* [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
