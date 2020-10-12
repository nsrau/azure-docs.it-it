---
title: Panoramica della verifica dell'autore - Microsoft Identity Platform | Azure
description: Viene fornita una panoramica del programma di verifica dell'editore per la piattaforma di identità Microsoft. Elenco dei vantaggi, dei requisiti del programma e delle domande frequenti. Se un'applicazione è contrassegnata come con autore verificato, significa che l'autore ha verificato la propria identità usando un account Microsoft Partner Network che ha completato il processo di verifica e ha associato l'account MPN durante la registrazione dell'applicazione.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 53861bbff9ba328ca97063df59cfedc6e886212d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258150"
---
# <a name="publisher-verification"></a>Verifica dell'autore

La verifica dell'editore consente agli amministratori e agli utenti finali di comprendere l'autenticità degli sviluppatori di applicazioni che si integrano con la piattaforma di identità Microsoft. Se un'applicazione è contrassegnata come con autore verificato, significa che l'autore ha verificato la propria identità usando un account [Microsoft Partner Network](https://partner.microsoft.com/membership) che ha completato il processo di [verifica](/partner-center/verification-responses) e ha associato l'account MPN durante la registrazione dell'applicazione. 

Un badge di verifica completata blu viene visualizzato nella richiesta di consenso di Azure AD e in altre schermate: ![Richiesta di consenso](./media/publisher-verification-overview/consent-prompt.png)

Questa funzionalità è destinata principalmente agli sviluppatori che creano app multi-tenant che sfruttano [OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) con [Microsoft Identity Platform](v2-overview.md). Queste app possono concedere l'accesso agli utenti tramite OpenID Connect oppure possono usare OAuth 2.0 per richiedere l'accesso ai dati usando API come [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Vantaggi
La verifica dell'autore offre i vantaggi seguenti:
- **Maggiore trasparenza e riduzione dei rischi per i clienti** - Questa funzionalità aiuta i clienti a scoprire quali app usate nelle organizzazioni sono pubblicate da sviluppatori considerati attendibili. 

- **Miglioramento della personalizzazione** - Viene visualizzato un badge di verifica completata nella [richiesta di consenso](application-consent-experience.md) di Azure AD, nella pagina App aziendali e in altre aree dell'esperienza utente usate dagli utenti finali e dagli amministratori. 

- **Adozione più agevole dell'organizzazione**: gli amministratori possono configurare i criteri di [consenso dell'utente](../manage-apps/configure-user-consent.md), con lo stato di verifica del server di pubblicazione come uno dei criteri principali.

> [!NOTE]
> A partire da novembre 2020, gli utenti finali non saranno più in grado di concedere il consenso per le app multi-tenant registrate più di recente senza autori verificati. Questo problema si applica alle app registrate dopo l'8 novembre 2020, usare OAuth 2.0 per richiedere autorizzazioni oltre al profilo di accesso e lettura del profilo utente di base e richiedere il consenso degli utenti in tenant diversi da quello in cui è registrata l'app. Verrà visualizzato un avviso nella schermata di consenso per informare gli utenti che queste app sono rischiose e che provengano da autori non verificati.    

## <a name="requirements"></a>Requisiti
Esistono alcuni prerequisiti per la verifica dell'autore, alcuni dei quali saranno già stati completati da molti partner Microsoft, ovvero: 

-  ID MPN per un account [Microsoft Partner Network](https://partner.microsoft.com/membership) valido che ha completato il processo di [verifica](/partner-center/verification-responses). Questo account MPN deve essere l'[account globale del partner (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) per l'organizzazione. 

-  App registrata in un tenant di Azure AD con un [dominio dell'editore](howto-configure-publisher-domain.md) configurato.

-  Il dominio dell'indirizzo di posta elettronica usato durante la verifica dell'account MPN deve corrispondere al dominio dell'editore configurato nell'app o a un [dominio personalizzato](../fundamentals/add-custom-domain.md) verificato da DNS aggiunto al tenant Azure ad. 

-  L'utente che esegue la verifica deve essere autorizzato ad apportare modifiche sia alla registrazione dell'app in Azure AD che all'account MPN in Partner Center. 

    -  In Azure AD l'utente deve essere membro di uno dei seguenti [ruoli](../users-groups-roles/directory-assign-admin-roles.md): amministratore dell'applicazione, amministratore dell'applicazione cloud o amministratore globale. 

    -  In Partner Center questo utente deve avere uno dei [ruoli](/partner-center/permissions-overview) seguenti: Amministratore MPN, Amministratore account o Amministratore globale (si tratta di un ruolo condiviso gestito in Azure AD).
    
-  L'utente che esegue la verifica deve eseguire l'accesso con [l'autenticazione a più fattori](../authentication/howto-mfa-getstarted.md).

-  L'autore accetta le [condizioni per l'utilizzo di Microsoft Identity Platform per sviluppatori](/legal/microsoft-identity-platform/terms-of-use).

Gli sviluppatori che hanno già soddisfatto questi prerequisiti possono essere verificati in pochi minuti. Se i requisiti non sono stati soddisfatti, la configurazione è gratuita. 

## <a name="frequently-asked-questions"></a>Domande frequenti 
Di seguito sono riportate alcune domande frequenti relative al programma di verifica dell'autore. Per le domande frequenti relative ai requisiti e al processo, vedere [Contrassegnare un'app come con autore verificato](mark-app-as-publisher-verified.md).

- **Quali informazioni __non__ vengono fornite dalla verifica dell'autore?**  Quando un'applicazione viene contrassegnata come con autore verificato, questo non indica se l'applicazione o il relativo autore abbia ottenuto una certificazione specifica, sia conforme agli standard del settore, rispetti procedure consigliate e così via. Queste informazioni sono fornite da altri programmi Microsoft, tra cui il [programma di certificazione delle app di Microsoft 365](/microsoft-365-app-certification/overview).

- **Qual è il costo? È necessaria una licenza?** Microsoft non addebita alcun costo agli sviluppatori per la verifica dell'autore e non richiede una licenza specifica. 

- **In che modo questa verifica è correlata all'attestazione dell'editore di Microsoft 365? E al programma di certificazione delle app di Microsoft 365?** Si tratta di programmi complementari che gli sviluppatori possono usare per creare app attendibili che possono essere adottate in tutta tranquillità dai clienti. La verifica dell'autore è il primo passaggio di questo processo e deve essere completata da tutti gli sviluppatori che creano app che soddisfano i criteri indicati in precedenza. 

  Gli sviluppatori che prevedono anche l'integrazione con Microsoft 365 possono ottenere vantaggi aggiuntivi da questi programmi. Per altre informazioni, vedere [Attestazione dell'editore di Microsoft 365](/microsoft-365-app-certification/docs/attestation) e [Certificazione delle app di Microsoft 365](/microsoft-365-app-certification/docs/certification). 

- **Si tratta della stessa cosa della Raccolta di applicazioni di Azure AD?** No. La verifica dell'autore è un programma complementare ma separato rispetto alla [Raccolta di applicazioni di Azure Active Directory](v2-howto-app-gallery-listing.md). Gli sviluppatori che soddisfano i criteri sopra indicati devono completare il processo di verifica dell'autore indipendentemente dalla partecipazione a tale programma. 

## <a name="next-steps"></a>Passaggi successivi
* Scoprire come [contrassegnare un'app come con autore verificato](mark-app-as-publisher-verified.md).
* [Risolvere i problemi](troubleshoot-publisher-verification.md) di verifica dell'autore.
