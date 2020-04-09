---
title: Come gestire gli account utente inattivi in Azure AD Documenti Microsoft
description: Informazioni su come rilevare e gestire gli account utente in Azure AD che sono diventati obsoleti
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886042"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Procedura: Gestire gli account utente inattivi in Azure ADHow To: Manage inactive user accounts in Azure AD

In ambienti di grandi dimensioni, gli account utente non vengono sempre eliminati quando i dipendenti lasciano un'organizzazione. In qualità di amministratore IT, si desidera rilevare e gestire questi account utente obsoleti perché rappresentano un rischio per la sicurezza.

Questo articolo illustra un metodo per gestire gli account utente obsoleti in Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Che cosa sono gli account utente inattivi?

Gli account inattivi sono account utente che non sono più richiesti dai membri dell'organizzazione per accedere alle risorse. Un identificatore di chiave per gli account inattivi è che non vengono utilizzati *per un po'* di tempo per accedere all'ambiente. Poiché gli account inattivi sono legati all'attività di accesso, è possibile usare il timestamp dell'ultimo accesso riuscito a rilevarli. 

La sfida di questo metodo è definire cosa *significa per un po 'nel* caso del proprio ambiente. Ad esempio, gli utenti potrebbero non accedere a un ambiente per un certo periodo di *tempo,* perché sono in vacanza. Quando si definisce il delta per gli account utente inattivi, è necessario tenere conto di tutti i motivi legittimi per non accedere all'ambiente. In molte organizzazioni, il delta per gli account utente inattivi è compreso tra 90 e 180 giorni. 

L'ultimo accesso riuscito fornisce potenziali informazioni sulla continua necessità di accesso alle risorse da parte di un utente.  Può aiutare a determinare se l'appartenenza al gruppo o l'accesso all'app è ancora necessario o potrebbe essere rimosso. Per la gestione degli utenti esterni, è possibile capire se un utente esterno è ancora attivo all'interno del tenant o deve essere pulito. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Come rilevare gli account utente inattivi

È possibile rilevare gli account inattivi valutando la proprietà **lastSignInDateTime** esposta dal tipo di risorsa **signInActivity** dell'API **Microsoft Graph.** Utilizzando questa proprietà, è possibile implementare una soluzione per gli scenari seguenti:Using this property, you can implement a solution for the following scenarios:

- **Utenti per nome**: In questo scenario, si cerca un utente specifico in base al nome, che consente di valutare lastSignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Utenti per data**: In questo scenario, si richiede un elenco di utenti con un lastSignInDateTime prima di una data specificata:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Informazioni importanti

In questa sezione vengono elencati gli elementi da conoscere sulla proprietà lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Come posso accedere a questa proprietà?

La proprietà **lastSignInDateTime** è esposta dal tipo di [risorsa signInActivity](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) dell'API REST di [Microsoft Graph.](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph)   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>La proprietà lastSignInDateTime è disponibile tramite il cmdlet Get-AzureAdUser?

No.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Quale edizione di Azure AD è necessaria per accedere alla proprietà?

È possibile accedere a questa proprietà in tutte le edizioni di Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Di quale autorizzazione ho bisogno per leggere la proprietà?

Per leggere questa proprietà, è necessario concedere i seguenti diritti: 

- AuditLogs.Read.All
- Organisation.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Quando Azure AD aggiorna la proprietà?

Ogni accesso interattivo che è stato eseguito correttamente comporta un aggiornamento dell'archivio dati sottostante. In genere, gli accessi riusciti vengono visualizzati nel report di accesso correlato entro 10 minuti.
 

### <a name="what-does-a-blank-property-value-mean"></a>Che cosa significa un valore di proprietà vuoto?

Per generare un timestamp lastSignInDateTime, è necessario un accesso corretto. Poiché la proprietà lastSignInDateTime è una nuova funzionalità, il valore della proprietà lastSignInDateTime può essere vuoto se:

- L'ultimo accesso riuscito di un utente ha avuto luogo prima del rilascio di questa funzionalità (1 dicembre 2019).
- L'account utente interessato non è mai stato utilizzato per un accesso riuscito.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](tutorial-access-api-with-certificates.md)
* [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
