---
title: Elementi del messaggio di posta elettronica di invito B2B-Azure Active Directory | Microsoft Docs
description: Modello di messaggio di posta elettronica di invito per la collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81407202"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory

I messaggi di posta elettronica di invito sono strumenti fondamentali per entrare in contatto con i partner e introdurli alla funzionalità di collaborazione B2B di Azure AD. Anche se [non è necessario inviare un messaggio di posta elettronica per invitare qualcuno a usare la collaborazione B2B](add-user-without-invite.md), in questo modo si fornisce all'utente tutte le informazioni necessarie per decidere se accettare l'invito. Fornisce anche un collegamento a cui gli utenti possono sempre fare riferimento in futuro quando dovranno tornare alle risorse.

![Screenshot che mostra il messaggio di posta elettronica di invito B2B](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Questo nuovo modello di messaggio di posta elettronica è ancora in fase di implementazione a tutti i tenant, quindi alcuni tenant usano ancora una progettazione precedente. Entro la fine del 2020 maggio, questo modello verrà usato dagli inviti di tutti i tenant.

## <a name="explaining-the-email"></a>Descrizione del messaggio di posta elettronica

Per sfruttare al meglio le potenzialità del messaggio di posta elettronica, di seguito ne vengono descritti alcuni elementi.

### <a name="subject"></a>Oggetto

L'oggetto del messaggio di posta elettronica segue questo modello:

&lt;nome&gt; utente ha invitato l'utente ad accedere alle applicazioni all'interno dell'organizzazione.

### <a name="from-address"></a>Indirizzo del mittente.

Per l'indirizzo del mittente si userà un modello simile a quello LinkedIn. Questo modello dovrebbe chiarire che, anche se il messaggio di invites@microsoft.composta elettronica deriva da, l'invito è di un'altra organizzazione. Il formato è: inviti Microsoft <invites@microsoft.com> o inviti Microsoft per conto del &lt;TenantName&gt; <invites@microsoft.com>. 

### <a name="reply-to"></a>Rispondi a

Se disponibile, viene impostato l'indirizzo di posta elettronica del mittente dell'invito. Un'eventuale risposta all'invito viene quindi inviata al mittente.

### <a name="phishing-warning"></a>Avviso di phishing

Il messaggio di posta elettronica inizia con un breve avviso per l'utente sul phishing, avvisando che devono accettare solo gli inviti previsti. È consigliabile assicurarsi che i partner da invitare non rimarranno sorpresi dall'invito e li menzionino in anticipo.

![Immagine dell'avviso di phishing nel messaggio di posta elettronica](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informazioni sul mittente dell'invito

Il messaggio di posta elettronica include informazioni sull'invito e sull'organizzazione da cui inviano l'invito. Sono inclusi il nome e l'indirizzo di posta elettronica del mittente, nonché il nome e il dominio primario associati all'organizzazione. Tutte queste informazioni dovrebbero aiutare l'invito a prendere decisioni informate sull'accettazione dell'invito.

![Immagine delle informazioni del mittente dell'invito nel messaggio di posta elettronica](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Messaggio di invito

Se il mittente dell'invito include un messaggio come parte del proprio invito quando [invita un utente guest alla directory, al gruppo o all'app](add-users-administrator.md) o quando [Usa l'API di invito](customize-invitation-api.md), il messaggio viene evidenziato nella sezione principale del messaggio di posta elettronica. Sono incluse anche il nome e l'immagine del profilo dell'utente invitato se ne è stata impostata una. Il messaggio stesso è un'area di testo, quindi, per motivi di sicurezza, non elabora i tag HTML.

![Immagine del messaggio di invito nel messaggio di posta elettronica](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Pulsante accetta e URL di Reindirizzamento

La sezione successiva del messaggio di posta elettronica contiene informazioni sulla posizione in cui verrà eseguito l'invito dopo l'accettazione dell'invito, oltre a un pulsante per farlo.  In futuro, l'invito può sempre usare questo collegamento per tornare direttamente alle risorse.

![Immagine del pulsante accetta e URL di reindirizzamento nel messaggio di posta elettronica](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Sezione piè di pagina

Il piè di pagina contiene altre informazioni sull'invito inviato. È sempre disponibile un'opzione che consente all'invito di bloccare gli inviti futuri. Se l'organizzazione ha [impostato un'informativa sulla privacy](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area), viene visualizzato il collegamento all'istruzione.  In caso contrario, una nota indica che l'organizzazione non ha impostato un'informativa sulla privacy.

![Immagine della sezione del piè di pagina nel messaggio di posta elettronica](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>Come viene determinata la lingua

La lingua presentata all'utente guest nel messaggio di posta elettronica di invito è determinata dalle impostazioni seguenti. Queste impostazioni sono elencate in base all'ordine di precedenza. Se un'impostazione non è configurata, viene usata l'impostazione successiva per determinare la lingua.

- Proprietà **messageLanguage** dell'oggetto [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) se viene usata l'API di creazione dell'invito
-   Proprietà **preferredLanguage** specificata nell'[oggetto utente](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) del guest
-   **Lingua delle notifiche** impostata nelle proprietà del tenant principale dell'utente guest (solo per i tenant di Azure AD)
-   **Lingua delle notifiche** impostata nelle proprietà del tenant della risorsa

Se non è configurata alcuna di queste impostazioni, come lingua verrà usato l'inglese (Stati Uniti) per impostazione predefinita.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è la collaborazione B2B di Azure AD?](what-is-b2b.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](add-users-administrator.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](add-users-information-worker.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Aggiungere utenti di Collaborazione B2B senza invito](add-user-without-invite.md)
