---
title: Informazioni su Collaborazione B2B di Azure Active Directory
description: Collaborazione B2B di Azure Active Directory supporta l'accesso di utenti guest per consentire di condividere in modo sicuro le risorse e collaborare con partner esterni.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d10eb3334154f4915aa0bddd488ddc78bde06805
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897263"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Che cos'è l'accesso utente guest in Azure Active Directory B2B?

Collaborazione B2B di Azure Active Directory (Azure AD) è una funzionalità relativa alle identità esterne che consente di invitare utenti guest a collaborare con l'organizzazione. Collaborazione B2B consente di condividere in modo sicuro le applicazioni e i servizi della propria azienda con utenti guest di altre organizzazioni, mantenendo il controllo sui dati aziendali. Permette inoltre di lavorare in modo sicuro e protetto con partner esterni, di piccole o grandi dimensioni, che abbiano o meno Azure AD o un reparto IT. Un semplice processo di invito e riscatto consente infatti ai partner di accedere alle risorse aziendali usando le credenziali personali. Gli sviluppatori possono inoltre usare le API B2B di Azure AD per personalizzare il processo di invito o scrivere applicazioni come i portali per l'iscrizione self-service. Per informazioni sulle licenze e sui prezzi correlati agli utenti guest, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).  

   > [!IMPORTANT]
   > **A partire dal 31 marzo 2021** Microsoft non supporterà più il riscatto degli inviti tramite la creazione di account e tenant di Azure AD non gestiti per gli scenari di collaborazione B2B. Nel frattempo, i clienti sono invitati ad acconsentire esplicitamente all'[autenticazione con passcode monouso tramite posta elettronica](one-time-passcode.md). Saremo lieti di ricevere feedback su questa funzionalità di anteprima pubblica e di creare ancora altri modi per collaborare.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Collaborare con i partner usando le relative identità

Con Azure AD B2B il partner usa la propria soluzione di gestione delle identità e l'organizzazione, quindi, non ha alcun sovraccarico amministrativo esterno. Gli utenti guest accedono alle app e ai servizi dell'organizzazione con le proprie identità aziendali, dell'istituto di istruzione o di social networking.

- Il partner usa le proprie identità e credenziali: Azure AD non è obbligatorio.
- Non è necessario gestire password o account esterni.
- Non è necessario sincronizzare gli account o gestire i cicli di vita degli account.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Invitare facilmente utenti guest dal portale di Azure AD

Gli amministratori possono aggiungere facilmente utenti guest all'organizzazione nel portale di Azure.

- [Creare un nuovo utente guest](b2b-quickstart-add-guest-users-portal.md) in Azure AD, in modo analogo all'aggiunta di un nuovo utente.
- Assegnare gli utenti guest ad app o gruppi.
- Inviare un messaggio di posta elettronica di invito contenente un collegamento di riscatto oppure inviare un collegamento diretto a un'app che si vuole condividere.

![Screenshot che mostra la pagina di immissione dell'invito Nuovo utente guest](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Gli utenti guest accedono seguendo una semplice [procedura di riscatto](redemption-experience.md).

![Screenshot che mostra la pagina Verifica le autorizzazioni](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Usare criteri per condividere in modo sicuro app e servizi

È possibile usare criteri di autorizzazione per proteggere i contenuti aziendali. Possono essere applicati criteri di accesso condizionale, ad esempio l'autenticazione a più fattori:

- A livello di tenant.
- A livello di applicazione.
- Per utenti guest specifici per proteggere dati e applicazioni aziendali.

![Screenshot che mostra l'opzione Accesso condizionale](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Consentire ai proprietari di un'applicazione e di un gruppo di gestire i relativi utenti guest

È possibile delegare la gestione degli utenti guest ai proprietari delle applicazioni in modo che possano aggiungere direttamente utenti guest a qualsiasi applicazione vogliano condividere, anche se non è un'applicazione Microsoft.

- Gli amministratori configurano la gestione self-service dell'app e del gruppo.
- Gli utenti non amministratori usano il proprio [pannello di accesso](https://myapps.microsoft.com) per aggiungere utenti guest ad applicazioni o gruppi.

![Screenshot che mostra il pannello di accesso per un utente guest](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Personalizzare l'esperienza di onboarding per gli utenti guest B2B

È possibile personalizzare il caricamento di partner esterni in base alle esigenze dell'organizzazione.

- Usare [Gestione entitlement di Azure AD](../governance/entitlement-management-overview.md) per configurare i criteri che [gestiscono l'accesso per gli utenti esterni](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- È possibile usare le [API di invito di Collaborazione B2B](/graph/api/resources/invitation) per personalizzare le esperienze di onboarding.

## <a name="integrate-with-identity-providers"></a>Integrazione con i provider di identità

Azure AD supporta provider di identità esterni come Facebook, account Microsoft, Google o provider di identità aziendali. È possibile configurare la federazione con i provider di identità in modo che gli utenti esterni possano accedere con i propri account aziendali o di social networking invece di crearne uno nuovo solo per l'applicazione. Vedere altre informazioni sui provider di identità per Identità esterne.

![Screenshot con la pagina di provider di identità](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>Aggiungere un flusso utente di iscrizione self-service (anteprima)

Con un flusso utente di iscrizione self-service, è possibile creare un'esperienza di iscrizione per gli utenti esterni che vogliono accedere alle app. Come parte del flusso di iscrizione, è possibile fornire opzioni per diversi provider di identità aziendali o di social networking e raccogliere informazioni sull'utente. Leggere altre informazioni sull'[iscrizione self-service e su come configurarla](self-service-sign-up-overview.md).

È anche possibile usare [connettori API](api-connectors-overview.md) per integrare i flussi utente di iscrizione self-service con sistemi cloud esterni. È possibile connettersi con flussi di lavoro di approvazione personalizzati, eseguire la verifica dell'identità, convalidare le informazioni fornite dall'utente e altro ancora.

![Screenshot che mostra la pagina di flussi utente](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Passaggi successivi

- [Prezzi delle identità esterne](external-identities-pricing.md)
- [Aggiungere utenti guest di Collaborazione B2B nel portale](add-users-administrator.md)
- [Panoramica sul processo di riscatto di un invito](redemption-experience.md)