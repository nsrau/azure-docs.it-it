---
title: Riscatto dell'invito nella collaborazione B2B-Azure AD
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
ms.openlocfilehash: c7917b63b86e4d103061a902f74c368dfd6c123b
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160742"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Riscatto dell'invito di Collaborazione B2B di Azure Active Directory

Questo articolo descrive i modi in cui gli utenti guest possono accedere alle risorse e il processo di consenso che incontra. Se si invia un messaggio di posta elettronica di invito al Guest, l'invito include un collegamento che può essere riscattato dal Guest per ottenere l'accesso all'app o al portale. Il messaggio di posta elettronica di invito è solo uno dei modi in cui gli utenti possono ottenere l'accesso alle risorse. In alternativa, è possibile aggiungere Guest alla directory e assegnare loro un collegamento diretto al portale o all'app che si vuole condividere. Indipendentemente dal metodo usato, i guest vengono guidati tramite un processo di consenso per la prima volta. Questo processo garantisce che i guest accettino le condizioni per la privacy e accettino le condizioni per l' [utilizzo](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) impostate.

Quando si aggiunge un utente guest alla directory, l'account utente Guest dispone di uno stato di consenso (visualizzabile in PowerShell) inizialmente impostato su **PendingAcceptance**. Questa impostazione rimane fino a quando il Guest non accetta l'invito e accetta l'informativa sulla privacy e le condizioni per l'utilizzo. Successivamente, lo stato di consenso diventa **accettato**e le pagine di consenso non vengono più presentate al Guest.

   > [!IMPORTANT]
   > **A partire dal 31 marzo 2021**, Microsoft non supporterà più il riscatto degli inviti creando account Azure ad non gestiti e tenant per gli scenari di collaborazione B2B. In preparazione, si consiglia ai clienti di acconsentire esplicitamente all' [autenticazione del codice di posta elettronica](one-time-passcode.md). Siamo lieti di ricevere commenti e suggerimenti su questa funzionalità di anteprima pubblica e siamo lieti di creare altri modi per collaborare.

## <a name="redemption-through-the-invitation-email"></a>Riscatto con il messaggio di posta elettronica di invito

Quando si aggiunge un utente guest alla directory [usando il portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), viene inviato un messaggio di posta elettronica di invito al Guest nel processo. È anche possibile scegliere di inviare messaggi di posta elettronica di invito quando si [USA PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) per aggiungere utenti guest alla directory. Ecco una descrizione dell'esperienza del Guest quando riscattano il collegamento nel messaggio di posta elettronica.

1. Il Guest riceve un [messaggio di posta elettronica di invito](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) inviato da **Microsoft inviti**.
2. Il Guest seleziona **Accept invito** nel messaggio di posta elettronica.
3. Il Guest utilizzerà le proprie credenziali per accedere alla directory. Se il Guest non dispone di un account che può essere federato alla directory e la funzionalità di accesso monouso [(OTP) del messaggio di posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) non è abilitata; al Guest viene richiesto di creare un account utente [personale o](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) un [account self-service Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup). Per informazioni dettagliate, fare riferimento al [flusso di riscatto dell'invito](#invitation-redemption-flow) .
4. Il Guest è guidato attraverso l' [esperienza di consenso](#consent-experience-for-the-guest) descritta di seguito.

## <a name="redemption-through-a-direct-link"></a>Riscatto attraverso un collegamento diretto

In alternativa al messaggio di posta elettronica di invito, è possibile assegnare a un guest un collegamento diretto all'app o al portale. Per prima cosa è necessario aggiungere l'utente guest alla directory tramite il [portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) o [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). È quindi possibile usare uno qualsiasi dei [modi personalizzabili per distribuire le applicazioni agli utenti](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), inclusi i collegamenti di accesso diretto. Quando un Guest usa un collegamento diretto anziché un messaggio di posta elettronica di invito, viene comunque guidato attraverso la prima esperienza di consenso.

> [!IMPORTANT]
> Il collegamento diretto deve essere specifico del tenant. In altre parole, deve includere un ID tenant o un dominio verificato, in modo che il Guest possa essere autenticato nel tenant, in cui si trova l'app condivisa. Un URL comune come https://myapps.microsoft.com non funzionerà per un guest perché verrà reindirizzato al tenant principale per l'autenticazione. Di seguito sono riportati alcuni esempi di collegamenti diretti con il contesto del tenant:
 > - Pannello di accesso per https://myapps.microsoft.com/?tenantid=&ltle app:; ID tenant&gt; 
 > - Pannello di accesso alle app per un dominio https://myapps.microsoft.com/&ltverificato:; dominio verificato&gt;
 > - Portale di Azure: https://portal.azure.com/&lt; ID tenant&gt;
 > - App singola: vedere come usare un [collegamento di accesso diretto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

In alcuni casi, il messaggio di posta elettronica di invito è consigliato tramite un collegamento diretto. Se questi casi speciali sono importanti per la propria organizzazione, si consiglia di invitare gli utenti usando metodi che ancora inviano il messaggio di posta elettronica di invito:
 - L'utente non dispone di un account Azure AD, un MSA o un account di posta elettronica in un'organizzazione federata. A meno che non si stia usando la funzionalità di accesso monouso, il guest deve riscattare il messaggio di posta elettronica di invito per seguire la procedura per la creazione di un MSA.
 - È a volte possibile che l'oggetto utente invitato non abbia un indirizzo di posta elettronica a causa di un conflitto con un oggetto contatto (ad esempio, un oggetto contatto di Outlook). In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.
 - L'utente può accedere con un alias dell'indirizzo di posta elettronica invitato. Un alias è un indirizzo di posta elettronica aggiuntivo associato a un account di posta elettronica. In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di posta elettronica di invito.

## <a name="invitation-redemption-flow"></a>Flusso di riscatto dell'invito

Quando un utente fa clic sul collegamento **Accept invito** in un [messaggio di posta elettronica di invito](invitation-email-elements.md), Azure ad riscatta automaticamente l'invito in base al flusso di riscatto, come illustrato di seguito:

![Screenshot che illustra il diagramma del flusso di riscatto](media/redemption-experience/invitation-redemption-flow.png)

**Se il nome dell'entità utente (UPN) dell'utente corrisponde a un account di Azure AD e a un account MSA personale, all'utente verrà richiesto di scegliere l'account con cui si desidera riscattare.*

1. Azure AD esegue l'individuazione basata sull'utente per determinare se l'utente esiste in un [tenant di Azure ad esistente](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal).

2. Se un amministratore ha abilitato la [Federazione diretta](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation), Azure ad controlla se il suffisso di dominio dell'utente corrisponde al dominio di un provider di identità SAML/WS-Fed configurato e reindirizza l'utente al provider di identità preconfigurato.

3. Se un amministratore ha abilitato [Google Federation](https://docs.microsoft.com/azure/active-directory/b2b/google-federation), Azure ad controlla se il suffisso di dominio dell'utente è gmail.com o googlemail.com e reindirizza l'utente a Google.

4. Il processo di riscatto controlla se l'utente dispone di un [account Microsoft personale (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)esistente.

5. Una volta identificata la **Home directory** dell'utente, l'utente viene inviato al provider di identità corrispondente per accedere.  

6. Se i passaggi da 1 a 4 non riescono a trovare una home directory per l'utente invitato, Azure AD determina se il tenant che invia l'invito ha abilitato la funzionalità di accesso monouso [(OTP)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) per i guest.

7. Se l' [indirizzo di posta elettronica per gli utenti Guest è abilitato per la posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode), viene inviato un codice di accesso tramite il messaggio di posta elettronica invitato. L'utente recupererà e immetterà questo codice nella pagina di accesso Azure AD.

8. Se il codice di posta elettronica monouso per i Guest è disabilitato, Azure AD controlla il suffisso del dominio per determinare se appartiene a un account del consumer. In tal caso, all'utente viene richiesto di creare un [account Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)personale. In caso contrario, all'utente viene richiesto di creare un [account self-service Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).

9. Azure AD tenta di creare un [account self-service Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) verificando l'accesso al messaggio di posta elettronica. Per verificare che l'account sia stato effettuato, inviare un codice al messaggio di posta elettronica e fare in modo che l'utente lo recuperi e lo invii a Azure AD. Tuttavia, se il tenant dell'utente invitato è federato o se il campo AllowEmailVerifiedUsers è impostato su false nel tenant dell'utente invitato, l'utente non è in grado di completare il riscatto e il flusso genera un errore. Per ulteriori informazioni, vedere [risoluzione dei problemi Azure Active Directory collaborazione B2B](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. All'utente viene richiesto di creare un [account Microsoft personale (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Dopo l'autenticazione al provider di identità appropriato, l'utente viene reindirizzato a Azure AD per completare l' [esperienza di consenso](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest).  

Per i riscatti JIT (just-in-Time), in cui il riscatto avviene tramite un collegamento a un'applicazione tenant, i passaggi da 8 a 10 non sono disponibili. Se un utente raggiunge il passaggio 6 e la funzionalità di accesso monouso di un messaggio di posta elettronica non è abilitata, l'utente riceve un messaggio di errore e non è in grado di riscattare l'invito. Per evitare questo errore, è necessario che gli amministratori [consentano il codice](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode) di accesso monouso per la posta elettronica o che l'utente faccia clic su un collegamento di invito.

## <a name="consent-experience-for-the-guest"></a>Esperienza di consenso per il Guest

Quando un Guest accede per la prima volta a risorse in un'organizzazione partner, viene guidato attraverso le pagine seguenti. 

1. Il Guest esamina la pagina **Verifica autorizzazioni** che descrive l'informativa sulla privacy dell'organizzazione che invita. Per continuare, un utente deve **accettare** l'uso delle proprie informazioni in conformità alle informative sulla privacy dell'organizzazione.

   ![Screenshot che mostra la pagina Verifica le autorizzazioni](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Per informazioni su come l'amministratore di un tenant può creare un collegamento all'informativa sulla privacy dell'organizzazione, vedere [Procedura: Aggiungere le informazioni sulla privacy dell'organizzazione in Azure AD](https://aka.ms/adprivacystatement).

2. Se le condizioni per l'utilizzo sono configurate, il Guest si apre e esamina le condizioni per l'utilizzo e quindi seleziona **Accept**. 

   ![Screenshot che mostra le nuove condizioni per l'utilizzo](media/redemption-experience/terms-of-use-accept.png) 

   È possibile configurare vedere [le condizioni](../governance/active-directory-tou.md) per l'utilizzo in **Gestisci** > **relazioni** > organizzative**condizioni per l'utilizzo**.

3. Se non diversamente specificato, il Guest viene reindirizzato al pannello di accesso Apps, che elenca le applicazioni a cui il Guest può accedere.

   ![Screenshot che mostra il pannello di accesso delle app](media/redemption-experience/myapps.png) 

Nella directory il valore accettato per l' **invito** del Guest viene modificato in **Sì**. Se è stato creato un MSA, l' **origine** del Guest Mostra l' **account Microsoft**. Per ulteriori informazioni sulle proprietà dell'account utente Guest, vedere [proprietà di un utente di collaborazione B2B di Azure ad](user-properties.md). 

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD collaborazione B2B?](what-is-b2b.md)
- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker](add-users-information-worker.md)
- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](customize-invitation-api.md#powershell)
- [Uscire da un'organizzazione come utente guest](leave-the-organization.md)
