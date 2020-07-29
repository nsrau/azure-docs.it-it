---
title: Riscatto dell'invito di Collaborazione B2B - Azure AD
description: Descrive l'esperienza di riscatto di invito di Collaborazione B2B di Azure AD per gli utenti finali, inclusa l'accettazione delle condizioni di privacy.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6bf5d40262c5991504d3dc62490fb50f6a20592
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826095"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Riscatto dell'invito di Collaborazione B2B di Azure Active Directory

Questo articolo descrive i modi in cui gli utenti guest possono accedere alle risorse e i processi di consenso previsti. Se si invia un messaggio di posta elettronica di invito all'utente guest, l'invito include un collegamento che può essere riscattato dall'utente guest per ottenere l'accesso all'app o al portale. Il messaggio di posta elettronica di invito è solo uno dei modi in cui gli utenti possono ottenere l'accesso alle risorse. In alternativa, è possibile aggiungere gli utenti guest alla directory e assegnare loro un collegamento diretto al portale o all'app che si vuole condividere. Indipendentemente dal metodo usato, gli utenti guest vengono guidati attraverso un primo processo di consenso. Questo processo garantisce che gli utenti guest accettino l'informativa sulla privacy e le eventuali [condizioni per l'utilizzo](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) previste.

Quando si aggiunge un utente guest alla directory, l'account utente guest presenta uno stato di consenso (visualizzabile in PowerShell) inizialmente impostato su **Accettazione in sospeso**. Questa impostazione rimane fino a quando l'utente guest non accetta l'invito e accetta l'informativa sulla privacy e le condizioni per l'utilizzo. Successivamente, lo stato di consenso passa ad **Accettato** e le pagine di consenso non vengono più presentate all'utente guest.

   > [!IMPORTANT]
   > **A partire dal 31 marzo 2021** Microsoft non supporterà più il riscatto degli inviti tramite la creazione di account e tenant di Azure AD non gestiti per gli scenari di collaborazione B2B. Nel frattempo, i clienti sono invitati ad acconsentire esplicitamente all'[autenticazione con passcode monouso tramite posta elettronica](one-time-passcode.md). Saremo lieti di ricevere feedback su questa funzionalità di anteprima pubblica e di creare ancora altri modi per collaborare.

## <a name="redemption-through-the-invitation-email"></a>Riscatto con il messaggio di posta elettronica di invito

Quando si aggiunge un utente guest alla directory [usando il portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), nel processo viene inviato un messaggio di posta elettronica di invito all'utente guest. È anche possibile scegliere di inviare messaggi di posta elettronica di invito quando si [usa PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) per aggiungere utenti guest alla directory. Ecco una descrizione dell'esperienza dell'utente guest quando riscatta il collegamento nel messaggio di posta elettronica.

1. L'utente guest riceve un [messaggio di posta elettronica di invito](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) inviato da **Microsoft Invitations**.
2. L'utente guest seleziona **Accetta l'invito** nel messaggio di posta elettronica.
3. L'utente guest userà le proprie credenziali per accedere alla directory. Se l'utente guest non dispone di un account che può essere federato alla directory e la funzionalità [passcode monouso tramite posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) non è abilitata, all'utente guest viene richiesto di creare un [account del servizio gestito](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personale o un [account self-service Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup). Per informazioni dettagliate, vedere il [flusso di riscatto dell'invito](#invitation-redemption-flow).
4. L'utente guest viene guidato attraverso l'[esperienza di consenso](#consent-experience-for-the-guest) descritta di seguito.

## <a name="redemption-through-a-direct-link"></a>Riscatto attraverso un collegamento diretto

In alternativa al messaggio di posta elettronica di invito, è possibile assegnare a un utente guest un collegamento diretto all'app o al portale. È prima necessario aggiungere l'utente guest alla directory tramite il [portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) o [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Quindi è possibile usare uno dei [modi personalizzabili per distribuire le applicazioni agli utenti](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), inclusi i collegamenti di accesso diretto. Quando un utente guest usa un collegamento diretto anziché un messaggio di posta elettronica di invito, viene comunque guidato attraverso la prima esperienza di consenso.

> [!IMPORTANT]
> Il collegamento diretto deve essere specifico del tenant. In altre parole, deve includere un ID tenant o un dominio verificato, in modo che l'utente guest possa essere autenticato nel tenant in cui si trova l'app condivisa. Un URL comune come https://myapps.microsoft.com non funzionerà per un utente guest perché verrà reindirizzato al tenant principale per l'autenticazione. Di seguito sono riportati alcuni esempi di collegamenti diretti con il contesto del tenant:
 > - Pannello di accesso per le app: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Pannello di accesso per le app per un dominio verificato: `https://myapps.microsoft.com/<;verified domain>`
 > - Portale di Azure: `https://portal.azure.com/<tenant id>`
 > - App singola: vedere come usare un [collegamento di accesso diretto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

In alcuni casi, è consigliabile usare il messaggio di posta elettronica di invito anziché un collegamento diretto. Se questi casi particolari sono importanti per l'organizzazione, è consigliabile invitare gli utenti usando metodi che inviano il messaggio di posta elettronica di invito:
 - L'utente non dispone di un account Azure AD, un account del servizio gestito o un account di posta elettronica in un'organizzazione federata. A meno che non sia previsto l'uso della funzionalità di passcode monouso, l'utente guest deve riscattare il messaggio di posta elettronica di invito per seguire la procedura per la creazione di un account del servizio gestito.
 - È a volte possibile che l'oggetto utente invitato non abbia un indirizzo di posta elettronica a causa di un conflitto con un oggetto contatto (ad esempio, un oggetto contatto di Outlook). In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.
 - L'utente può accedere con un alias dell'indirizzo di posta elettronica invitato. Un alias è un indirizzo di posta elettronica aggiuntivo associato a un account di posta elettronica. In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.

## <a name="invitation-redemption-flow"></a>Flusso di riscatto dell'invito

Quando un utente fa clic sul collegamento **Accetta l'invito** in un [messaggio di posta elettronica di invito](invitation-email-elements.md), Azure AD riscatta automaticamente l'invito in base al flusso di riscatto, come illustrato di seguito:

![Screenshot che illustra il diagramma del flusso di riscatto](media/redemption-experience/invitation-redemption-flow.png)

**Se il nome dell'entità utente (UPN) corrisponde a un account Azure AD e a un account del servizio gestito personale, all'utente verrà richiesto di scegliere l'account con cui vuole riscattare l'invito.*

1. Azure AD esegue l'individuazione basata sull'utente per determinare se l'utente è presente in un [tenant di Azure AD esistente](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal).

2. Se un amministratore ha abilitato la [federazione diretta](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation), Azure AD controlla se il suffisso del dominio dell'utente corrisponde al dominio di un provider di identità SAML/WS-Fed configurato e reindirizza l'utente al provider di identità preconfigurato.

3. Se un amministratore ha abilitato la [federazione Google](https://docs.microsoft.com/azure/active-directory/b2b/google-federation), Azure AD controlla se il suffisso del dominio dell'utente è gmail.com o googlemail.com e reindirizza l'utente a Google.

4. Il processo di riscatto controlla se l'utente dispone di un [account Microsoft (account del servizio gestito)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personale esistente.

5. Una volta identificato la **home directory** dell'utente, l'utente viene reindirizzato al provider di identità corrispondente per l'accesso.  

6. Se durante i passaggi da 1 a 4 non è possibile individuare una home directory per l'utente invitato, Azure AD determina se il tenant che invia l'invito ha abilitato la funzionalità [passcode monouso tramite posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) per gli utenti guest.

7. Se la funzionalità [passcode monouso tramite posta elettronica è abilitata per gli utenti guest](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode), all'utente viene inviato un passcode tramite il messaggio di posta elettronica di invito. L'utente recupererà il passcode e lo immetterà nella pagina di accesso di Azure AD.

8. Se la funzionalità passcode monouso tramite posta elettronica è disabilitata per gli utenti guest, Azure AD controlla il suffisso del dominio per determinare se appartiene a un account consumer. In tal caso, all'utente viene richiesto di creare un [account Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personale. In caso contrario, all'utente viene richiesto di creare un [account self-service Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).

9. Azure AD tenta di creare un [account self-service Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) verificando l'accesso al messaggio di posta elettronica. La verifica dell'account viene effettuata inviando tramite posta elettronica un codice che l'utente dovrà recuperare e immettere in Azure AD. Tuttavia, se il tenant dell'utente invitato è federato o se il campo AllowEmailVerifiedUsers è impostato su false nel tenant dell'utente invitato, l'utente non potrà completare la procedura di riscatto e il flusso genera un errore. Per altre informazioni, vedere [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. All'utente viene richiesto di creare un [account Microsoft (account del servizio gestito)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personale.

11. Dopo l'autenticazione al provider di identità corretto, l'utente viene reindirizzato ad Azure AD per completare l'[esperienza di consenso](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest).  

Per i riscatti JIT, in cui il riscatto avviene tramite un collegamento a un'applicazione tenant, i passaggi da 8 a 10 non sono disponibili. Se un utente raggiunge il passaggio 6 e la funzionalità passcode monouso tramite posta elettronica non è abilitata, l'utente riceve un messaggio di errore e non può riscattare l'invito. Per evitare questo errore, è necessario che gli amministratori [abilitino la funzionalità passcode monouso tramite posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode) o che si assicurino che l'utente faccia clic su un collegamento di invito.

## <a name="consent-experience-for-the-guest"></a>Esperienza di consenso per l'utente guest

Quando un utente guest effettua l'accesso per usare le risorse di un'organizzazione partner per la prima volta, viene guidato attraverso le pagine seguenti. 

1. L'utente guest esamina la pagina **Verifica le autorizzazioni** che riporta l'informativa sulla privacy dell'organizzazione che ha inviato l'invito. Per continuare, un utente deve **accettare** l'uso delle informazioni in conformità alle norme sulla privacy dell'organizzazione che ha inviato l'invito.

   ![Screenshot che mostra la pagina Verifica le autorizzazioni](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Per informazioni sul modo in cui un amministratore del tenant può collegarsi all'informativa sulla privacy dell'organizzazione, vedere [Procedura: Aggiungere le informazioni sulla privacy dell'organizzazione in Azure Active Directory](https://aka.ms/adprivacystatement).

2. Se le condizioni per l'utilizzo sono configurate, l'utente guest apre e verifica le condizioni per l'utilizzo, quindi seleziona **Accetto**. 

   ![Screenshot che mostra le nuove condizioni per l'utilizzo](media/redemption-experience/terms-of-use-accept.png) 

   È possibile configurare le [condizioni per l'utilizzo](../governance/active-directory-tou.md) in **Identità esterne** > **Condizioni per l'utilizzo**.

3. Se non diversamente specificato, l'utente guest viene reindirizzato al pannello di accesso App, che elenca le applicazioni a cui l'utente guest può accedere.

   ![Screenshot che mostra il pannello di accesso App](media/redemption-experience/myapps.png) 

Nella directory il valore di **Invito accettato** dell'utente guest passa a **Sì**. Se è stato creato un account del servizio gestito, il valore di **Origine** dell'utente guest mostra **Account Microsoft**. Per ulteriori informazioni sulle proprietà dell'account utente guest, vedere [Proprietà di un utente di Collaborazione B2B di Azure AD](user-properties.md). 

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker](add-users-information-worker.md)
- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](customize-invitation-api.md#powershell)
- [Leave an organization as a guest user (Uscire da un'organizzazione come utente guest)](leave-the-organization.md)
