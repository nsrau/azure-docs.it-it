---
title: Riscatto dell'invito nella collaborazione B2B - Azure ADInvitation redemption in B2B collaboration - Azure AD
description: Descrive l'esperienza di riscatto di invito di Collaborazione B2B di Azure AD per gli utenti finali, inclusa l'accettazione delle condizioni di privacy.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0645807aa40557c163643f1393c310668518f9be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535137"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Riscatto dell'invito di Collaborazione B2B di Azure Active Directory

In questo articolo vengono descritti i modi in cui gli utenti guest possono accedere alle risorse e il processo di consenso che incontreranno. Se si invia un messaggio di posta elettronica di invito all'ospite, l'invito include un collegamento che l'ospite può riscattare per ottenere l'accesso all'app o al portale. L'e-mail di invito è solo uno dei modi in cui gli ospiti possono accedere alle tue risorse. In alternativa, puoi aggiungere ospiti alla tua directory e fornire loro un collegamento diretto al portale o all'app che vuoi condividere. Indipendentemente dal metodo che utilizzano, gli ospiti sono guidati attraverso un processo di consenso per la prima volta. Questo processo garantisce che i tuoi ospiti accettino le condizioni sulla privacy e accettino le [condizioni d'uso](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) che hai configurato.

Quando si aggiunge un utente guest alla directory, lo stato di consenso dell'account utente guest (visualizzabile in PowerShell) inizialmente impostato su **PendingAcceptance**. Questa impostazione rimane finché l'ospite non accetta l'invito e non accetta l'informativa sulla privacy e le condizioni per l'utilizzo. Dopo di che, lo stato di consenso diventa **Accettato**e le pagine di consenso non vengono più presentate all'ospite.

   > [!IMPORTANT]
   > **A partire dal 31 marzo 2021,** Microsoft non supporterà più il riscatto degli inviti creando account e tenant di Azure AD non gestiti per scenari di collaborazione B2B. In preparazione, incoraggiamo i clienti a scegliere [l'autenticazione monouso del passcode via e-mail.](one-time-passcode.md) Accogliamo con favore il tuo feedback su questa funzione di anteprima pubblica e siamo entusiasti di creare ancora più modi per collaborare.

## <a name="redemption-through-the-invitation-email"></a>Riscatto con il messaggio di posta elettronica di invito

Quando si aggiunge un utente guest alla directory [tramite il portale](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)di Azure , viene inviato un messaggio di posta elettronica di invito al guest nel processo. È anche possibile scegliere di inviare messaggi di posta elettronica di invito quando si [usa PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) per aggiungere utenti guest alla directory. Ecco una descrizione dell'esperienza dell'ospite quando riscatta il link nell'e-mail.

1. Il guest riceve un messaggio di [posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) di invito inviato da Microsoft **Invitations**.
2. L'ospite seleziona **Inizia** nell'e-mail.
3. Se il guest non dispone di un account Azure AD, di un account Microsoft (MSA) o di un account di posta elettronica in un'organizzazione federata, viene richiesto di creare un'unica funzionalità di [passcode,](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) che non richiede un MSA.
4. L'ospite è guidato attraverso [l'esperienza](#consent-experience-for-the-guest) di consenso descritta di seguito.

## <a name="redemption-through-a-direct-link"></a>Riscatto attraverso un collegamento diretto

In alternativa all'e-mail di invito, puoi fornire a un ospite un collegamento diretto all'app o al portale. È innanzitutto necessario aggiungere l'utente guest alla directory tramite il portale di [Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) o [PowerShell.](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) È quindi possibile utilizzare uno dei [modi personalizzabili per distribuire](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)le applicazioni agli utenti, inclusi i collegamenti di accesso diretto. Quando un ospite utilizza un link diretto invece dell'e-mail di invito, verrà comunque guidato durante la prima esperienza di consenso.

> [!IMPORTANT]
> Il collegamento diretto deve essere specifico del tenant. In altre parole, deve includere un ID tenant o un dominio verificato in modo che l'ospite possa essere autenticato nel tenant, in cui si trova l'app condivisa. Un URL https://myapps.microsoft.com comune come non funziona per un guest perché verrà reindirizzato al tenant principale per l'autenticazione. Ecco alcuni esempi di collegamenti diretti con il contesto tenant:Here are some examples of direct links with tenant context:
 > - Pannello di https://myapps.microsoft.com/?tenantid=&ltaccesso alle app: ;ID tenant&gt; 
 > - Pannello di accesso alle https://myapps.microsoft.com/&ltapp per un dominio verificato:&gt;
 > - Portale di https://portal.azure.com/&ltAzure: ;ID tenant&gt;
 > - Singola app: scopri come usare un [collegamento di accesso diretto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

In alcuni casi l'e-mail di invito è consigliata tramite un collegamento diretto. Se questi casi speciali sono importanti per l'organizzazione, è consigliabile invitare gli utenti utilizzando metodi che inviano comunque il messaggio di posta elettronica di invito:
 - L'utente non dispone di un account Azure AD, un account MSA o un account di posta elettronica in un'organizzazione federata. A meno che non si utilizzi la funzionalità di passcode monouso, l'ospite deve riscattare l'e-mail di invito per essere guidata attraverso i passaggi per la creazione di un MSA.
 - È a volte possibile che l'oggetto utente invitato non abbia un indirizzo di posta elettronica a causa di un conflitto con un oggetto contatto (ad esempio, un oggetto contatto di Outlook). In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.
 - L'utente può accedere con un alias dell'indirizzo di posta elettronica invitato. Un alias è un indirizzo di posta elettronica aggiuntivo associato a un account di posta elettronica. In questo caso, l'utente deve fare clic sull'URL di riscatto nell'e-mail di invito.

## <a name="invitation-redemption-flow"></a>Flusso di riscatto dell'invito

Quando un utente fa clic sul collegamento Accetta invito in un messaggio di posta elettronica di [invito,](invitation-email-elements.md)Azure AD riscatta automaticamente l'invito in base al flusso di riscatto, come illustrato di seguito:When a user clicks the **Accept invitation** link in an invitation email , Azure AD automatically redeems the invitation on the redemption flow as shown below:

![Screenshot che mostra il diagramma di flusso di riscatto](media/redemption-experience/invitation-redemption-flow.png)

1. Il processo di riscatto verifica se l'utente dispone di un account Microsoft personale [(MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)esistente.

2. Se un amministratore ha abilitato la [federazione diretta](direct-federation.md), Azure AD verifica se il suffisso di dominio dell'utente corrisponde al dominio di un provider di identità SAML/WS-Fed configurato e reindirizza l'utente al provider di identità preconfigurato.

3. Se un amministratore ha abilitato la [federazione di Google](google-federation.md), Azure AD verifica se il suffisso di dominio dell'utente è gmail.com o googlemail.com e reindirizza l'utente a Google.

4. Azure AD esegue l'individuazione basata sull'utente per determinare se l'utente esiste in un tenant di [Azure AD esistente.](what-is-b2b.md#easily-add-guest-users-in-the-azure-ad-portal)

5. Una volta identificata la **home directory** dell'utente, l'utente viene inviato al provider di identità corrispondente per l'accesso.  

6. Se i passaggi da 1 a 4 non riescono a trovare una home directory per l'utente invitato, Azure AD determina se il tenant invitante ha abilitato la funzionalità [di passcode monouso (OTP) tramite posta elettronica](one-time-passcode.md) per gli ospiti.

7. Se l'opzione [E-mail passcode per gli ospiti è abilitata,](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)viene inviato un passcode all'utente tramite l'e-mail invitata. L'utente recupererà e immetterà questo passcode nella pagina di accesso di Azure AD.

8. Se il passcode monouso per i guest tramite posta elettronica è disabilitato, Azure AD controlla il suffisso di dominio rispetto a un elenco di domini consumer gestito da Microsoft.If Email one-time passcode for guests is disabled, Azure AD checks the domain suffix against a consumer domain list maintained by Microsoft. Se il dominio corrisponde a qualsiasi dominio nell'elenco dei domini consumer, all'utente viene richiesto di creare un account Microsoft personale. In caso contrario, all'utente viene richiesto di creare un [account self-service](../users-groups-roles/directory-self-service-signup.md) di Azure AD (account virale).

9. Azure AD tenta di creare un account self-service di Azure AD (account virale) verificando l'accesso alla posta elettronica. La verifica dell'account viene eseguita inviando un codice al messaggio di posta elettronica e facendo in modo che l'utente recuperi e lo invii ad Azure AD. Tuttavia, se il tenant dell'utente invitato è federato o se il campo AllowEmailVerifiedUsers è impostato su false nel tenant dell'utente invitato, l'utente non è in grado di completare il riscatto e il flusso genera un errore. Per altre informazioni, vedere Risoluzione dei problemi di [collaborazione B2B](troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)di Azure Active Directory .

10. All'utente viene richiesto di creare un account Microsoft personale (MSA).

11. Dopo l'autenticazione al provider di identità corretto, l'utente viene reindirizzato ad Azure AD per completare l'esperienza di [consenso.](redemption-experience.md#consent-experience-for-the-guest)  

Per i rimborsi JIT, in cui il riscatto è tramite un collegamento a un'applicazione tenant, i passaggi da 8 a 10 non sono disponibili. Se un utente raggiunge il passaggio 6 e la funzionalità di passcode monouso di posta elettronica non è abilitata, l'utente riceve un messaggio di errore e non è in grado di riscattare l'invito. Per evitare questo problema, gli amministratori devono abilitare il [passcode monouso](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) di Posta elettronica o assicurarsi che l'utente fa clic su un collegamento di invito.

## <a name="consent-experience-for-the-guest"></a>Esperienza di consenso per l'ospite

Quando un ospite accede per accedere alle risorse di un'organizzazione partner per la prima volta, viene guidato tramite le pagine seguenti. 

1. L'ospite esamina la pagina **Controlla autorizzazioni** che descrive l'informativa sulla privacy dell'organizzazione invitante. Un utente deve **accettare** l'uso delle proprie informazioni in conformità alle politiche sulla privacy dell'organizzazione invitante per continuare.

   ![Screenshot che mostra la pagina Verifica le autorizzazioni](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Per informazioni su come l'amministratore di un tenant può creare un collegamento all'informativa sulla privacy dell'organizzazione, vedere [Procedura: Aggiungere le informazioni sulla privacy dell'organizzazione in Azure AD](https://aka.ms/adprivacystatement).

2. Se le condizioni per l'utilizzo sono configurate, l'ospite apre e rivede le condizioni per l'utilizzo, quindi seleziona **Accetta**. 

   ![Screenshot che mostra le nuove condizioni per l'utilizzo](media/redemption-experience/terms-of-use-accept.png) 

   È possibile configurare [le condizioni per l'utilizzo](../governance/active-directory-tou.md) in **Gestire le** > **relazioni organizzative** > **Condizioni per l'utilizzo**.

3. Se non diversamente specificato, il guest viene reindirizzato al pannello di accesso App, che elenca le applicazioni a cui il guest può accedere.

   ![Screenshot del pannello di accesso App](media/redemption-experience/myapps.png) 

Nella directory, il valore **Di invito accettato** dell'ospite viene modificato in **Sì**. Se è stato creato un MSA, **l'origine** del guest mostra **l'account Microsoft**. Per altre informazioni sulle proprietà dell'account utente guest, vedere [Proprietà di un utente di collaborazione B2B](user-properties.md)di Azure AD. 

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è la collaborazione B2B di Azure AD?](what-is-b2b.md)
- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker](add-users-information-worker.md)
- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](customize-invitation-api.md#powershell)
- [Uscire da un'organizzazione come utente guest](leave-the-organization.md)
