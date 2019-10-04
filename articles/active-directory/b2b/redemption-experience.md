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
ms.openlocfilehash: 2d32818f9e96e931f9e8c3c13554752327c5c456
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622620"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Riscatto dell'invito di Collaborazione B2B di Azure Active Directory

Questo articolo descrive i modi in cui gli utenti guest possono accedere alle risorse e il processo di consenso che incontra. Se si invia un messaggio di posta elettronica di invito al Guest, l'invito include un collegamento che può essere riscattato dal Guest per ottenere l'accesso all'app o al portale. Il messaggio di posta elettronica di invito è solo uno dei modi in cui gli utenti possono ottenere l'accesso alle risorse. In alternativa, è possibile aggiungere Guest alla directory e assegnare loro un collegamento diretto al portale o all'app che si vuole condividere. Indipendentemente dal metodo usato, i guest vengono guidati tramite un processo di consenso per la prima volta. Questo processo garantisce che i guest accettino le condizioni per la privacy e accettino le condizioni per l' [utilizzo](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) impostate.

Quando si aggiunge un utente guest alla directory, l'account utente Guest dispone di uno stato di consenso (visualizzabile in PowerShell) inizialmente impostato su **PendingAcceptance**. Questa impostazione rimane fino a quando il Guest non accetta l'invito e accetta l'informativa sulla privacy e le condizioni per l'utilizzo. Successivamente, lo stato di consenso diventa **accettato**e le pagine di consenso non vengono più presentate al Guest.

## <a name="redemption-through-the-invitation-email"></a>Riscatto con il messaggio di posta elettronica di invito

Quando si aggiunge un utente guest alla directory [usando il portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), viene inviato un messaggio di posta elettronica di invito al Guest nel processo. È anche possibile scegliere di inviare messaggi di posta elettronica di invito quando si [USA PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) per aggiungere utenti guest alla directory. Ecco una descrizione dell'esperienza del Guest quando riscattano il collegamento nel messaggio di posta elettronica.

1. Il Guest riceve un [messaggio di posta elettronica di invito](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) inviato da **Microsoft inviti**.
2. Il Guest seleziona **Get Started** nel messaggio di posta elettronica.
3. Se il Guest non dispone di un account Azure AD, di un account Microsoft (MSA) o di un account di posta elettronica in un'organizzazione federata, viene richiesto di creare un MSA (a meno che non sia abilitata la funzionalità monouso per il [codice](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) , che non richiede un MSA).
4. Il Guest è guidato attraverso l' [esperienza di consenso](#consent-experience-for-the-guest) descritta di seguito.

## <a name="redemption-through-a-direct-link"></a>Riscatto attraverso un collegamento diretto

In alternativa al messaggio di posta elettronica di invito, è possibile assegnare a un guest un collegamento diretto all'app o al portale. Per prima cosa è necessario aggiungere l'utente guest alla directory tramite il [portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) o [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). È quindi possibile usare uno qualsiasi dei [modi personalizzabili per distribuire le applicazioni agli utenti](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), inclusi i collegamenti di accesso diretto. Quando un Guest usa un collegamento diretto anziché un messaggio di posta elettronica di invito, viene comunque guidato attraverso la prima esperienza di consenso.

> [!IMPORTANT]
> Il collegamento diretto deve essere specifico del tenant. In altre parole, deve includere un ID tenant o un dominio verificato, in modo che il Guest possa essere autenticato nel tenant, in cui si trova l'app condivisa. Un URL comune come https://myapps.microsoft.com non funzionerà per un guest perché verrà reindirizzato al tenant principale per l'autenticazione. Di seguito sono riportati alcuni esempi di collegamenti diretti con il contesto del tenant:
 > - Pannello di accesso per https://myapps.microsoft.com/?tenantid=&lt le app:; ID tenant&gt; 
 > - Pannello di accesso alle app per un dominio https://myapps.microsoft.com/&lt verificato:; dominio verificato&gt;
 > - Portale di Azure: https://portal.azure.com/&lt ; ID tenant&gt;
 > - App singola: vedere come usare un [collegamento di accesso diretto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

In alcuni casi, il messaggio di posta elettronica di invito è consigliato tramite un collegamento diretto. Se questi casi speciali sono importanti per la propria organizzazione, si consiglia di invitare gli utenti usando metodi che ancora inviano il messaggio di posta elettronica di invito:
 - L'utente non dispone di un account Azure AD, un MSA o un account di posta elettronica in un'organizzazione federata. A meno che non si stia usando la funzionalità di accesso monouso, il guest deve riscattare il messaggio di posta elettronica di invito per seguire la procedura per la creazione di un MSA.
 - È a volte possibile che l'oggetto utente invitato non abbia un indirizzo di posta elettronica a causa di un conflitto con un oggetto contatto (ad esempio, un oggetto contatto di Outlook). In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.
 - L'utente può accedere con un alias dell'indirizzo di posta elettronica invitato. Un alias è un indirizzo di posta elettronica aggiuntivo associato a un account di posta elettronica. In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.

## <a name="consent-experience-for-the-guest"></a>Esperienza di consenso per il Guest

Quando un Guest accede per la prima volta a risorse in un'organizzazione partner, viene guidato attraverso le pagine seguenti. 

1. Il Guest esamina la pagina **Verifica autorizzazioni** che descrive l'informativa sulla privacy dell'organizzazione che invita. Per continuare, un utente deve **accettare** l'uso delle proprie informazioni in conformità alle informative sulla privacy dell'organizzazione.

   ![Screenshot che mostra la pagina Verifica le autorizzazioni](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Per informazioni sul modo in cui un amministratore del tenant può collegarsi all'informativa sulla privacy dell'organizzazione, vedere [Procedura: Aggiungere le informazioni sulla privacy dell'organizzazione in Azure Active Directory](https://aka.ms/adprivacystatement).

2. Se le condizioni per l'utilizzo sono configurate, il Guest si apre e esamina le condizioni per l'utilizzo e quindi seleziona **Accept**. 

   ![Screenshot che mostra le nuove condizioni per l'utilizzo](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > È possibile configurare vedere [le condizioni](../governance/active-directory-tou.md) per l' > utilizzo in Gestisci**relazioni** > organizzative**condizioni per l'utilizzo**.

3. Se non diversamente specificato, il Guest viene reindirizzato al pannello di accesso Apps, che elenca le applicazioni a cui il Guest può accedere.

   ![Screenshot che mostra il pannello di accesso delle app](media/redemption-experience/myapps.png) 

Nella directory il valore accettato per l' **invito** del Guest viene modificato in **Sì**. Se è stato creato un MSA, l' **origine** del Guest Mostra l' **account Microsoft**. Per ulteriori informazioni sulle proprietà dell'account utente Guest, vedere [proprietà di un utente di collaborazione B2B di Azure ad](user-properties.md). 

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker](add-users-information-worker.md)
- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](customize-invitation-api.md#powershell)
- [Leave an organization as a guest user (Uscire da un'organizzazione come utente guest)](leave-the-organization.md)
