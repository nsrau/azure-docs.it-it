---
title: Portale di iscrizione self-service per la collaborazione B2B - Azure AD
description: La collaborazione B2B di Azure Active Directory supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfa67f529408efcc2a703a4f80b15143c774f0b9
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77195794"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Portale self-service per l'iscrizione a Collaborazione B2B di Azure AD

I clienti possono eseguire molte operazioni con le funzionalità predefinite esposte tramite il [portale di Azure](https://portal.azure.com) e il [pannello di accesso alle applicazioni](https://myapps.microsoft.com) per gli utenti finali. Tuttavia, potrebbe essere necessario personalizzare il flusso di lavoro di onboarding per gli utenti B2B in base alle esigenze della propria organizzazione.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Gestione entitlement di Azure AD per l'iscrizione di utenti guest B2B

Come organizzazione che emette inviti, si potrebbe non sapere in anticipo quali singoli collaboratori esterni avranno bisogno di accedere alle risorse. È necessario un modo che consenta agli utenti delle aziende partner di effettuare l'iscrizione con criteri controllati. Se si vuole consentire agli utenti di altre organizzazioni di richiedere l'accesso e, al momento dell'approvazione, di effettuare il provisioning degli account guest e di assegnarli a gruppi, app e siti di SharePoint Online, è possibile usare la [gestione entitlement di Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) per configurare criteri che [gestiscono l'accesso per gli utenti esterni](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>API di invito della funzionalità B2B di Azure Active Directory

Le organizzazioni possono usare l'[API di gestione inviti di Microsoft Graph](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) per creare esperienze di onboarding personalizzate per utenti guest B2B. Quando si vuole consentire a un utente guest B2B di effettuare l'iscrizione self-service, è consigliabile usare la [gestione entitlement di Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Se, però, si vuole creare un'esperienza personalizzata, è ad esempio possibile usare l'[API di creazione inviti](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) per inviare automaticamente il messaggio di invito personalizzato direttamente all'utente B2B. In alternativa, l'app può usare il valore di inviteRedeemUrl restituito nella risposta di creazione per creare un invito personalizzato (tramite il meccanismo di comunicazione scelto) per l'utente invitato.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Licenze per la Collaborazione B2B di Azure AD](licensing-guidance.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](faq.md)
