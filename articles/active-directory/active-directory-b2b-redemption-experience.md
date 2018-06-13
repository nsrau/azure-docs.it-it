---
title: Riscatto di invito in Collaborazione B2B - Azure Active Directory | Microsoft Docs
description: Descrive l'esperienza di riscatto di invito di Collaborazione B2B di Azure AD per gli utenti finali, inclusa l'accettazione delle condizioni di privacy.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2e354bc4ae06e86afd5d14e87ef796fce942521b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074782"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Riscatto dell'invito di Collaborazione B2B di Azure Active Directory

Per collaborare con gli utenti delle organizzazioni partner tramite Collaborazione B2B di Azure Active Directory (Azure AD), è possibile invitare gli utenti guest ad accedere alle app condivise. Dopo che un utente guest è stato aggiunto alla directory tramite l'interfaccia utente o dopo che l'utente è stato invitato tramite PowerShell, deve eseguire il primo processo di consenso in cui accetta le [condizioni di privacy](#privacy-policy-agreement). Questo processo avviene in uno dei modi seguenti:

- Il mittente dell'invito guest invia un collegamento diretto a un'applicazione condivisa. L'invitato fa clic sul collegamento per accedere, accetta le condizioni di privacy e accede senza problemi alla risorsa condivisa. L'utente guest riceve tuttavia un invito tramite posta elettronica con un URL di riscatto, ma, se non in casi particolari, non è più necessario usare l'invito tramite posta elettronica.  
- L'utente guest riceve un invito tramite posta elettronica e fa clic sull'URL di riscatto. Durante il primo accesso, viene chiesto di accettare le condizioni di privacy.

## <a name="redemption-through-a-direct-link"></a>Riscatto tramite un collegamento diretto

Un mittente dell'invito guest può invitare un utente guest inviando un collegamento diretto a un'app condivisa. Per l'utente guest, l'esperienza di riscatto è facile come accedere all'app condivisa. Può fare clic su un collegamento all'app, esaminare e accettare le condizioni di privacy e quindi accedere all'app senza problemi. Nella maggior parte dei casi, gli utenti guest non devono più fare clic su un URL di riscatto in un invito tramite posta elettronica.

Se gli utenti guest sono stati invitati tramite l'interfaccia utente o si è scelto di inviare l'invito tramite posta elettronica nell'ambito dell'esperienza di invito di PowerShell, l'utente invitato riceve ancora un invito tramite posta elettronica. Questo messaggio di posta elettronica è utile per i casi seguenti:

- L'utente non ha un account Microsoft o un account Azure AD. In questo caso, l'utente deve creare un account Microsoft prima di fare clic sul collegamento o può usare l'URL di riscatto nell'invito tramite posta elettronica. Il processo di riscatto chiede automaticamente all'utente di creare un account Microsoft.
- È a volte possibile che l'oggetto utente invitato non abbia un indirizzo di posta elettronica a causa di un conflitto con un oggetto contatto (ad esempio, un oggetto contatto di Outlook). In questo caso, l'utente deve fare clic sull'URL di riscatto nell'invito tramite posta elettronica.
- L'utente può accedere con un alias dell'indirizzo di posta elettronica invitato. Un alias è un indirizzo di posta elettronica aggiuntivo associato a un account di posta elettronica. In questo caso, l'utente deve fare clic sull'URL di riscatto nell'invito tramite posta elettronica.

Se questi particolari casi sono importanti per l'organizzazione, è consigliabile invitare gli utenti usando i metodi che inviano ancora l'invito tramite posta elettronica. Se poi un utente non rientra in uno di questi casi specifici, può fare clic sull'URL incluso in un invito tramite posta elettronica per ottenere l'accesso.

## <a name="redemption-through-the-invitation-email"></a>Riscatto tramite il messaggio di posta elettronica di invito

Se invitati con un metodo che invia un invito tramite posta elettronica, gli utenti possono anche riscattare un invito tramite il messaggio di posta elettronica. Un utente invitato può fare clic sull'URL di riscatto nel messaggio di posta elettronica e quindi esaminare e accettare le condizioni di privacy. Il processo viene descritto in modo più dettagliato qui:

1.  Dopo essere stato invitato, l'invitato riceve un invito tramite posta elettronica inviato da **Microsoft Invitations** (Inviti Microsoft).
2.  L'invitato seleziona **Get Started** (Inizia) nel messaggio di posta elettronica.
3.  Se l'invitato non ha un account Azure AD o un account Microsoft, gli viene chiesto di creare un account Microsoft.
4.  L'invitato viene reindirizzato alla schermata **Verifica le autorizzazioni**, dove può esaminare l'informativa sulla privacy dell'organizzazione che ha inviato l'invito e accettare le condizioni.

## <a name="privacy-policy-agreement"></a>Accettazione dell'informativa sulla privacy

Dopo che un utente guest effettua l'accesso per usare le risorse di un'organizzazione partner per la prima volta, visualizza una schermata **Verifica le autorizzazioni**, dove può esaminare l'informativa sulla privacy dell'organizzazione che ha inviato l'invito. Per continuare, un utente deve accettare l'uso delle informazioni in conformità alle norme sulla privacy dell'organizzazione che ha inviato l'invito.

![Screenshot che illustra le impostazioni utente nel riquadro di accesso](media/active-directory-b2b-redemption-experience/ConsentScreen.png) 

Per informazioni su come l'amministratore di un tenant può creare un collegamento all'informativa sulla privacy dell'organizzazione, vedere [How-to: Add your organization's privacy info in Azure Active Directory](https://aka.ms/adprivacystatement) (Procedura: Aggiungere le informazioni sulla privacy dell'organizzazione in Azure Active Directory).

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](active-directory-b2b-admin-add-users.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker](active-directory-b2b-iw-add-users.md)
- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](active-directory-b2b-api.md#powershell)
- [Leave an organization as a guest user (Uscire da un'organizzazione come utente guest)](active-directory-b2b-leave-the-organization.md)