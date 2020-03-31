---
title: Informazioni su Collaborazione B2B di Azure Active Directory
description: Collaborazione B2B di Azure Active Directory supporta l'accesso di utenti guest per consentire di condividere in modo sicuro le risorse e collaborare con partner esterni.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 167c7f7aa3dbf967575c0ee8905821fb9d3f77ce
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050731"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Che cos'è l'accesso utente guest in Azure Active Directory B2B?

Collaborazione B2B (Business-to-Business) di Azure Active Directory consente di condividere in modo sicuro le applicazioni e i servizi della propria azienda con utenti guest di altre organizzazioni, senza perdere il controllo sui dati aziendali. Permette inoltre di lavorare in modo sicuro e protetto con partner esterni, di piccole o grandi dimensioni, che abbiano o meno Azure AD o un reparto IT. Un semplice processo di invito e riscatto consente infatti ai partner di accedere alle risorse aziendali usando le credenziali personali. Gli sviluppatori possono inoltre usare le API B2B di Azure AD per personalizzare il processo di invito o scrivere applicazioni come i portali per l'iscrizione self-service.

Guardare il video per scoprire come collaborare in modo sicuro con utenti guest invitandoli ad accedere alle app e ai servizi aziendali usando le identità personali.

Il video seguente fornisce un'utile panoramica.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

   > [!IMPORTANT]
   > **A partire dal 31 marzo 2021** Microsoft non supporterà più il riscatto degli inviti tramite la creazione di account e tenant di Azure AD non gestiti per gli scenari di collaborazione B2B. Nel frattempo, i clienti sono invitati ad acconsentire esplicitamente all'[autenticazione con passcode monouso tramite posta elettronica](one-time-passcode.md). Saremo lieti di ricevere feedback su questa funzionalità di anteprima pubblica e di creare ancora altri modi per collaborare.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Collaborare con i partner usando le relative identità

Con Azure AD B2B il partner usa la propria soluzione di gestione delle identità e l'organizzazione, quindi, non ha alcun sovraccarico amministrativo esterno.

- Il partner usa le proprie identità e credenziali: Azure AD non è obbligatorio.
- Non è necessario gestire password o account esterni.
- Non è necessario sincronizzare gli account o gestire i cicli di vita degli account.  

![Screenshot che mostra la pagina Aggiungi membri](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Invitare utenti guest con un semplice processo di invito e riscatto

Gli utenti guest accedono alle app e ai servizi dell'organizzazione con le proprie identità aziendali, dell'istituto di istruzione o di social networking. Se l'utente guest non dispone di un account Microsoft o di un account Azure AD, ne viene creato uno al momento del riscatto dell'invito. 

- Invitare gli utenti guest usando l'identità di posta elettronica che hanno scelto.
- Inviare un collegamento diretto a un'app o inviare un invito al pannello di accesso dell'utente guest.
- Gli utenti guest accedono seguendo una semplice procedura di riscatto.

![Screenshot che mostra la pagina Verifica le autorizzazioni](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Usare criteri per condividere in modo sicuro app e servizi

È possibile usare criteri di autorizzazione per proteggere i contenuti aziendali. Possono essere applicati criteri di accesso condizionale, ad esempio l'autenticazione a più fattori:

- A livello di tenant.
- A livello di applicazione.
- Per utenti guest specifici per proteggere dati e applicazioni aziendali.

![Screenshot che mostra l'opzione Accesso condizionale](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Aggiungere facilmente utenti guest nel portale di Azure AD

Gli amministratori possono aggiungere facilmente utenti guest all'organizzazione nel portale di Azure.

- Creare un nuovo utente guest in Azure AD, analogamente a come è stato aggiunto un nuovo utente.
- L'utente guest riceve immediatamente un invito personalizzabile che gli consente di accedere al proprio pannello di accesso.
- Gli utenti guest nella directory possono essere assegnati ad app o gruppi.  

![Screenshot che mostra la pagina di immissione dell'invito Nuovo utente guest](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Consentire ai proprietari di un'applicazione e di un gruppo di gestire i relativi utenti guest

È possibile delegare la gestione degli utenti guest ai proprietari delle applicazioni in modo che possano aggiungere direttamente utenti guest a qualsiasi applicazione vogliano condividere, anche se non è un'applicazione Microsoft.

- Gli amministratori configurano la gestione self-service dell'app e del gruppo.
- Gli utenti non amministratori usano il proprio [pannello di accesso](https://myapps.microsoft.com) per aggiungere utenti guest ad applicazioni o gruppi.

![Screenshot che mostra il pannello di accesso per un utente guest](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Personalizzare l'esperienza di onboarding per gli utenti guest B2B

È possibile personalizzare il caricamento di partner esterni in base alle esigenze dell'organizzazione.

- Usare [Gestione entitlement di Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) per configurare i criteri che [gestiscono l'accesso per gli utenti esterni](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).
- È possibile usare le [API di invito di Collaborazione B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) per personalizzare le esperienze di onboarding.

## <a name="next-steps"></a>Passaggi successivi

- [Linee guida sulla Collaborazione B2B di Azure AD](licensing-guidance.md)
- [Aggiungere utenti guest di Collaborazione B2B nel portale](add-users-administrator.md)
- [Panoramica sul processo di riscatto di un invito](redemption-experience.md)
- È sempre possibile mettersi in contatto con il team di prodotto per eventuali commenti, confronti e suggerimenti tramite [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
