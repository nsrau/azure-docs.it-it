---
title: Riscatto di invito in Collaborazione B2B - Azure Active Directory | Microsoft Docs
description: Descrive l'esperienza di riscatto di invito di Collaborazione B2B di Azure AD per gli utenti finali, inclusa l'accettazione delle condizioni di privacy.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052499"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Riscatto dell'invito di Collaborazione B2B di Azure Active Directory

Questo articolo descrive i modi in cui gli utenti guest possono accedere le risorse e il processo di consenso che verranno riscontrati. Se si invia un messaggio di posta elettronica di invito al guest, l'invito è incluso un collegamento guest può essere riscattato per ottenere l'accesso all'app o il portale. Messaggio di posta elettronica di invito è solo uno dei modi i guest possono ottenere accesso alle risorse. In alternativa, è possibile aggiungere utenti guest alla directory e assegnare loro un collegamento diretto per il portale o l'app che si vuole condividere. Indipendentemente dal metodo che usano, gli utenti guest vengono indirizzati attraverso un processo il primo consenso. Questo processo assicura che i guest per accettano i termini di privacy e accettano [le condizioni d'uso](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) è stato configurato.

Quando si aggiunge un utente guest nella directory, l'account utente guest ha uno stato di consenso (visualizzabile in PowerShell) che inizialmente è impostato su **PendingAcceptance**. Questa impostazione rimane fino a quando l'utente guest accetta l'invito e acconsente all'informativa sulla privacy e condizioni d'uso. Successivamente, lo stato di consenso diventa **accettato**, e le pagine di consenso non è più vengono presentate al guest.

## <a name="redemption-through-the-invitation-email"></a>Riscatto con il messaggio di posta elettronica di invito

Quando si aggiunge un utente guest nella directory dalla [usando il portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), viene inviata un'e-mail di invito al guest nel processo. È anche possibile scegliere di inviare messaggi di posta elettronica di invito, dopo aver [usando PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) per aggiungere utenti guest alla directory. Ecco una descrizione dell'esperienza del guest quando si Riscatta il collegamento nel messaggio di posta elettronica.

1. Il guest riceve un' [posta elettronica di invito](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) che viene inviato dal **Invitations Microsoft**.
2. Le istruzioni SELECT guest **iniziare a usare** nel messaggio di posta elettronica.
3. Se il guest non dispone di un account Azure AD, un Account Microsoft (MSA) o un account di posta elettronica in un'organizzazione federata, è richiesto di creare un account Microsoft (a meno che il [passcode monouso](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) funzionalità è abilitata, che non richiede un account Microsoft ).
4. Il guest viene guidato la [esperienza di consenso](#consent-experience-for-the-guest) descritto di seguito.

## <a name="redemption-through-a-direct-link"></a>Riscatto attraverso un collegamento diretto

Come alternativa al messaggio di posta elettronica di invito, è possibile assegnare un utente guest un collegamento diretto all'app o il portale. È necessario innanzitutto aggiungere l'utente guest alla directory tramite il [portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) oppure [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). È possibile usare uno qualsiasi dei [soluzioni personalizzabili per distribuire applicazioni agli utenti](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), inclusi i collegamenti diretti sign-on. Quando un utente guest utilizza un collegamento diretto anziché l'invito tramite posta elettronica, sono comunque informazioni dettagliate tramite l'esperienza di consenso prima volta.

> [!IMPORTANT]
> Il collegamento diretto deve essere specifico del tenant. In altre parole, è necessario includere un ID tenant o dominio verificato in modo che l'utente guest può essere autenticato nel tenant, in cui si trova l'app condivisa. Ad esempio un URL comune https://myapps.microsoft.com funzionerà per un utente guest perché si verrà reindirizzati al proprio tenant home per l'autenticazione. Di seguito sono riportati alcuni esempi di collegamenti diretti con contesto tenant:
 > - Pannello di accesso delle App: https://myapps.microsoft.com/?tenantid=&lt; id tenant&gt; 
 > - Pannello di accesso delle App per un dominio verificato: https://myapps.microsoft.com/&lt; verifica dominio&gt;
 > - Portale di Azure: https://portal.azure.com/&lt; id tenant&gt;
 > - App singole: informazioni su come usare un [collegamento diretto sign-on](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Esistono alcuni casi in cui il messaggio di posta elettronica di invito è consigliabile utilizzare un collegamento diretto. Se questi casi speciali sono importanti per l'organizzazione, è consigliabile invitare gli utenti con i metodi continuare a inviare l'invito tramite posta elettronica:
 - L'utente non ha un account Azure AD, un account Microsoft o un account di posta elettronica in un'organizzazione federata. A meno che non si usa la funzionalità di passcode monouso, il guest deve riscattare l'invito tramite posta elettronica per essere illustrati i passaggi per la creazione di un account Microsoft.
 - È a volte possibile che l'oggetto utente invitato non abbia un indirizzo di posta elettronica a causa di un conflitto con un oggetto contatto (ad esempio, un oggetto contatto di Outlook). In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.
 - L'utente può accedere con un alias dell'indirizzo di posta elettronica invitato. Un alias è un indirizzo di posta elettronica aggiuntivo associato a un account di posta elettronica. In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.

## <a name="consent-experience-for-the-guest"></a>Esperienza di consenso per l'utente guest

Quando un utente guest accede accedere alle risorse in un'organizzazione partner per la prima volta, si sta guidati le pagine seguenti. 

1. Le verifiche di guest il **verificare le autorizzazioni** pagina di descrizione informativa sulla privacy dell'organizzazione che emette l'invito. Un utente deve disporre **Accept** l'uso delle loro informazioni in base ai criteri di privacy dell'organizzazione che emette l'invito per continuare.

   ![Screenshot che mostra la pagina Verifica le autorizzazioni](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Per informazioni sul modo in cui un amministratore del tenant può collegarsi all'informativa sulla privacy dell'organizzazione, vedere [Procedura: Aggiungere le informazioni sulla privacy dell'organizzazione in Azure Active Directory](https://aka.ms/adprivacystatement).

2. Se le condizioni d'uso sono configurate, il guest viene aperto ed esamina le condizioni d'uso e quindi seleziona **Accept**. 

   ![Screenshot che mostra le nuove condizioni per l'utilizzo](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > È possibile configurare vedere [le condizioni d'uso](../governance/active-directory-tou.md) nelle **Gestisci** > **relazioni aziendale** > **condizioni d'uso**.

3. Se non diversamente specificato, il guest viene reindirizzato al pannello di accesso delle App, che elenca le applicazioni che può accedere l'utente guest.

   ![Screenshot che mostra il pannello di accesso delle App](media/redemption-experience/myapps.png) 

Nella directory, il guest **Invito accettato** valore diventa **Yes**. Se è stato creato un account Microsoft, il guest **origine** Mostra **Account Microsoft**. Per altre informazioni sulle proprietà dell'account utente guest, vedere [delle proprietà di un utente di collaborazione B2B di Azure AD](user-properties.md). 

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker](add-users-information-worker.md)
- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](customize-invitation-api.md#powershell)
- [Leave an organization as a guest user (Uscire da un'organizzazione come utente guest)](leave-the-organization.md)
