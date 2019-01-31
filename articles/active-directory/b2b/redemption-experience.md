---
title: Riscatto di invito in Collaborazione B2B - Azure Active Directory | Microsoft Docs
description: Descrive l'esperienza di riscatto di invito di Collaborazione B2B di Azure AD per gli utenti finali, inclusa l'accettazione delle condizioni di privacy.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 4f282edc08b16e226e6028b01f04dc62c1a57fea
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077269"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Riscatto dell'invito di Collaborazione B2B di Azure Active Directory

Per collaborare con gli utenti delle organizzazioni partner usando Collaborazione B2B di Azure Active Directory (Azure AD), è possibile invitare gli utenti guest ad accedere alle app condivise. Dopo essere stato aggiunto alla directory dall'interfaccia utente o essere stato invitato attraverso PowerShell, l'utente guest deve eseguire il primo processo di consenso in cui accetta le [condizioni di privacy](#privacy-policy-agreement). Questo processo avviene in uno dei modi seguenti:

- Il mittente dell'invito guest invia un collegamento diretto a un'applicazione condivisa. L'invitato fa clic sul collegamento per accedere, accetta le condizioni di privacy e accede senza problemi alla risorsa condivisa. L'utente guest riceve comunque un invito via posta elettronica con un URL di riscatto, ma, se non in casi particolari, non è più necessario usare il messaggio di invito.  
- L'utente guest riceve un invito via posta elettronica e fa clic sull'URL di riscatto. Durante il primo accesso, viene chiesto di accettare le condizioni di privacy.

## <a name="redemption-through-a-direct-link"></a>Riscatto attraverso un collegamento diretto

Un mittente dell'invito guest può invitare un utente guest inviando un [collegamento diretto a un'app condivisa](../manage-apps/end-user-experiences.md#direct-sign-on-links). Per l'utente guest, l'esperienza di riscatto è facile come accedere all'app condivisa. Può fare clic su un collegamento all'app, esaminare e accettare le condizioni di privacy e quindi accedere all'app senza problemi. Nella maggior parte dei casi, gli utenti guest non devono più fare clic su un URL di riscatto in un messaggio di invito.

Se gli utenti guest sono stati invitati dall'interfaccia utente o si è scelto di inviare l'invito via posta elettronica nell'ambito dell'esperienza di invito di PowerShell, l'utente invitato riceve comunque un messaggio di posta elettronica di invito. Questo messaggio è utile per i casi seguenti:

- L'utente non ha un account Microsoft o un account Azure AD. In questo caso, l'utente deve creare un account Microsoft prima di fare clic sul collegamento o può usare l'URL di riscatto nel messaggio di invito. Il processo di riscatto chiede automaticamente all'utente di creare un account Microsoft.
- È a volte possibile che l'oggetto utente invitato non abbia un indirizzo di posta elettronica a causa di un conflitto con un oggetto contatto (ad esempio, un oggetto contatto di Outlook). In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.
- L'utente può accedere con un alias dell'indirizzo di posta elettronica invitato. Un alias è un indirizzo di posta elettronica aggiuntivo associato a un account di posta elettronica. In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.

Se questi particolari casi sono importanti per l'organizzazione, è consigliabile invitare gli utenti usando i metodi che inviano ancora l'invito via posta elettronica. Se un utente non rientra in uno di questi casi specifici, può fare clic sull'URL incluso in un invito ricevuto via posta elettronica per ottenere l'accesso.

## <a name="redemption-through-the-invitation-email"></a>Riscatto con il messaggio di posta elettronica di invito

Se invitati con un metodo che invia un invito via posta elettronica, gli utenti possono anche riscattare un invito usando il messaggio di posta elettronica. Un utente invitato può fare clic sull'URL di riscatto nel messaggio di posta elettronica e quindi esaminare e accettare le condizioni di privacy. Il processo viene descritto in modo più dettagliato qui:

1.  Dopo essere stato invitato, l'invitato riceve un invito inviato via posta elettronica da **Microsoft Invitations** (Inviti Microsoft).
2.  L'invitato seleziona **Get Started** (Inizia) nel messaggio di posta elettronica.
3.  Se l'invitato non ha un account Azure AD o un account Microsoft, gli viene chiesto di creare un account Microsoft.
4.  L'invitato viene reindirizzato alla schermata **Verifica le autorizzazioni**, dove può esaminare l'informativa sulla privacy dell'organizzazione che ha inviato l'invito e accettare le condizioni.

## <a name="privacy-policy-agreement"></a>Accettazione dell'informativa sulla privacy

Dopo che un utente guest effettua l'accesso per usare le risorse di un'organizzazione partner per la prima volta, visualizza la schermata **Verifica le autorizzazioni**, dove può esaminare l'informativa sulla privacy dell'organizzazione che ha inviato l'invito. Per continuare, un utente deve accettare l'uso delle informazioni in conformità alle norme sulla privacy dell'organizzazione che ha inviato l'invito.

![Screenshot che illustra le impostazioni utente nel riquadro di accesso](media/redemption-experience/ConsentScreen.png) 

Per informazioni sul modo in cui un amministratore del tenant può collegarsi all'informativa sulla privacy dell'organizzazione, vedere [Procedura: Aggiungere le informazioni sulla privacy dell'organizzazione in Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Condizioni per l'utilizzo

È possibile presentare le condizioni per l'utilizzo all'utente guest durante il processo di riscatto iniziale usando la funzionalità Condizioni per l'utilizzo di Azure AD. In Azure Active Directory è possibile accedere a questa funzionalità in **Gestisci** > **Relazioni aziendale** > **Condizioni per l'utilizzo** o in **Sicurezza** > **Accesso condizionale** > **Condizioni per l'utilizzo**. Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure AD](../governance/active-directory-tou.md).

![Screenshot che mostra le nuove condizioni per l'utilizzo](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker](add-users-information-worker.md)
- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](customize-invitation-api.md#powershell)
- [Leave an organization as a guest user (Uscire da un'organizzazione come utente guest)](leave-the-organization.md)
